# Day 16

## Task

Day by day traffic is increasing on one of the websites managed by the Nautilus production support team. Therefore, the team has observed a degradation in website performance. Following discussions about this issue, the team has decided to deploy this application on a high availability stack i.e on Nautilus infra in Stratos DC. They started the migration last month and it is almost done, as only the LBR server configuration is pending. Configure LBR server as per the information given below:

a. Install nginx on the LBR (load balancer) server if it is not already installed.

b. Configure load-balancing with the http context making use of all App Servers. Ensure that you update only the main Nginx configuration file located at /etc/nginx/nginx.conf.

c. Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all the app servers.

d. Once done, you can access the website by running curl http://stlb01:80 in the terminal.

## Solution

The task mentioned that nginx may already be installed, so check if it's already running:

```bash
systemctl status nginx
```

It's already set up, just disabled, so enable and start it:

```bash
sudo systemctl enable --now nginx
systemctl status nginx
```

Then edit the `/etc/nginx/nginx.conf` file to set up nginx as a load balancer to the three app servers:

```bash
sudo vim /etc/nginx/nginx.conf
``` 

I'm just going for simplicy here, so I copied the nginx basic load balancer from the nginx documentation and replaced the main http block in `nginx.conf`

```bash
http {
    upstream app_servers {
        server 10.244.195.253:8086;
        server 10.244.73.210:8086;
        server 10.244.49.44:8086;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://app_servers;
        }
    }
```

I did have to go in to the stapp servers and check what port httpd was running, and added that after the server hostnames.

## Validation

Test the nginx config and restart nginx if it works

```bash
nginx -t
sudo systemctl reload nginx
```

Then go back to the jump host and `curl http://stlb01`

> I was getting errors, and figured that the config didn't like the hostnames that I had, so I went back in and switched them to the IP addresses, which I got by running:

```bash
getent hosts stapp01
getent hosts stapp02
getent hosts stapp03
```

and just pasted those in to the upstream servers.  Tested and restarted nginx again, did the curl, and everything was good.

## Insights

My only real hiccup here was the hostnames unsurprisingly not being resolved by nginx, but once I switched them to the IP addresses everything worked great.

I remember when I was initially learning about load balancers, and setting one up with nginx, I thought it seemed like a ridiculously advanced task, but now that I've done it a few times, most of which were as practice for the LFCS, they're no longer intimidating.

I did have to check how to exactly do it again, but fortunately the nginx documentation has a page for it which is very simple.
