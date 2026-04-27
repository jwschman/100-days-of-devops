# Day 14

## Task

The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in Stratos DC.

Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not have hosted any code yet on these servers, so you don't need to worry if Apache isn't serving any pages. Just make sure the service is up and running. Also, make sure Apache is running on port 3000 on all app servers.

## Solution

ssh in to the app servers and check the status of httpd

```bash
systemctl status httpd
```

First server says it failed, says the port is already in use, so check what's using it

```bash
sudo ss -tulnp
```

It's sendmail... again.  Did I do this one already?  Stop and disable it, then restart httpd and check that it's running.

```bash
sudo systemctl disable sendmail
sudo systemctl stop sendmail
sudo systemctl restart httpd
sudo systemctl status httpd
```

## Validation

```bash
curl http:stapp01:3000
curl http:stapp02:3000
curl http:stapp03:3000
```

## Insights

This is definitely a repeat of day 12... right?  At least that means this one ended pretty quickly.
