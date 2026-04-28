# Day 15

## Task

The system admins team of xFusionCorp Industries needs to deploy a new application on App Server 2 in Stratos Datacenter. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:

1. Install and configure nginx on App Server 2.

2. On App Server 2 there is a self signed SSL certificate and key present at location /tmp/nautilus.crt and /tmp/nautilus.key. Move them to some appropriate location and deploy the same in Nginx.

3. Create an index.html file with content Welcome! under Nginx document root.

4. For final testing try to access the App Server 2 link (via hostname) from jump host using curl command. For example: curl -Ik https://<app-server-name>/.

## Solution

```bash
sudo yum install nginx
sudo mkdir /etc/nginx/ssl
sudo chmod 600 /etc/nginx/ssl
sudo cp /tmp/nautilus.crt /etc/nginx/ssl/
sudo cp /tmp/nautilus.key /etc/nginx/ssl/
sudo chmod 600 /etc/nginx/ssl/nautilus.key
sudo chmod 644 /etc/nginx/ssl/nautilus.crt
vim /etc/nginx/nginx.conf
```

add these lines to the `server` block.  I'm not actually sure if the `location /` is necessary or not...

```bash
    server {
        listen       443 ssl http2;
        listen       [::]:443 ssl http2;
        server_name  _;
        root         /usr/share/nginx/html;

        ssl_certificate "/etc/nginx/ssl/nautilus.crt";
        ssl_certificate_key "/etc/nginx/ssl/nautilus.key";
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;
        ssl_ciphers PROFILE=SYSTEM;
        ssl_prefer_server_ciphers on;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
                root /usr/share/nginx/html;
                index index.html index.htm;
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```

backup the default `index.html` and make the required `index.html` in `/usr/share/nginx/html/`

```bash
sudo mv /usr/share/nginx/html/index.html /usr/share/nginx/html/index.html.BAK
echo "Welcome!" | sudo tee /usr/share/nginx/html/index.html
```

test the nginx config

```bash
sudo nginx -t
```

if it's successful enable and start nginx

```bash
sudo systemctl enable --now nginx
```

## Validation

Check the status of nginx just to be sure:

```bash
systemctl status nginx
```

Just do the testing step it says--back on the jump host:

```bash
curl -Ik https://stapp02
```

## Insights

I have a bit of experience using NGINX as a reverse proxy previously as a FreeBSD jail and recently as a docker host, so this wasn't difficult.  I did initially search where to put the ssl certs, but if I had just looked in the `nginx.conf` I would have seen an example.

I did mention above that I didn't know if I needed to add the `location /` block in the `nginx.conf` file, but I did add it just to be sure.
