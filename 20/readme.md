# Day 20

## Task

The Nautilus application development team is planning to launch a new PHP-based application, which they want to deploy on Nautilus infra in Stratos DC. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:

a. Install nginx on app server 1 , configure it to use port 8094 and its document root should be /var/www/html.

b. Install php-fpm version 8.1 on app server 1, it must use the unix socket /var/run/php-fpm/default.sock (create the parent directories if don't exist).

c. Configure php-fpm and nginx to work together.

d. Once configured correctly, you can test the website using curl http://stapp01:8094/index.php command from jump host.

NOTE: We have copied two files, index.php and info.php, under /var/www/html as part of the PHP-based application setup. Please do not modify these files.

## Solution

ssh into stapp01 and install and configure nginx:

```bash
sudo yum install nginx -y
```

Then set the port to 8094, document root to /var/www/html, and include the php location and unix socket in the nginx configuration file:

```bash
sudo vim /etc/nginx/nginx.conf
```

Add this to the server block in http:

```nginx
    listen: 8094;
    root /var/www/html;
    index index.php index.html;

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
```

test the config:

```bash
sudo nginx -t
```

Next install and configure php-fpm 8.1.  But first we need epel and others (following this guide <https://infotechys.com/install-php-8-3-on-rhel-9-centos-9/>):

```bash
sudo dnf install epel-release -y
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y
sudo dnf module reset php
sudo dnf module enable php:remi-8.1 -y
sudo dnf install php php-cli php-fpm php-mysqlnd php-xml php-mbstring php-json php-zip -y
```

That final command might be more than I need here, but I don't think it matters for this task.

Make sure it's installed with `php-fpm -v`.

Next configure the user and socket:

```bash
sudo vim /etc/php-fpm.d/www.conf
```

Set these in the `[www]` block:

```php
user = nginx
group = nginx
listen = /var/run/php-fpm/default.sock
```

Create the socket directory and set permissions:

```bash
sudo mkdir -p /var/run/php-fpm
sudo chown -R nginx:nginx /var/run/php-fpm
```

Enable and start nginx and php-fpm

```bash
sudo systemctl enable --now nginx
sudo systemctl enable --now php-fpm
systemctl status nginx
systemctl status php-fpm
```

Both should be running fine.

## Validation

Like it says in the task, just go to the jump host and run:

```bash
curl http://stapp01:8094/index.php
```

And hopefully we should see "Welcome to xFusionCorp Industries!"

## Insights

This has been the most demanding task in 100 days of DevOps so far.  I had to check some guides online to see how to install a specific version of php-fpm and also needed some guidance for using the unix socket in both nginx and php-fpm.

I actually had a little bit of trouble with the socket, and that caused me to fail the task the first time I submitted it.  The guide I read showed me the correct way to set it, but I added the line at the top of the `[www]` block rather than setting the already-set value, so when the config was loaded it still just loaded the default configuration.  My `curl` from the jump host still showed that it was working, but the socket wasn't configured correctly so I failed.

On my second attempt I set the value in the correct place and it worked fine.
