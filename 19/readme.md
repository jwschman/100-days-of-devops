# Day 19

## Task

xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter. The development of these websites is still in-progress, but we want to get the servers ready. Please perform the following steps to accomplish the task:

a. Install httpd package and dependencies on app server 3.

b. Apache should serve on port 3002.

c. There are two website's backups /home/thor/beta and /home/thor/cluster on jump_host. Set them up on Apache in a way that beta should work on the link http://localhost:3002/beta/ and cluster should work on link http://localhost:3002/cluster/ on the mentioned app server.

d. Once configured you should be able to access the website using curl command on the respective app server, i.e curl http://localhost:3002/beta/ and curl http://localhost:3002/cluster/

## Solution

ssh in to app server 3:

```bash
sudo yum install httpd
sudo vim /etc/httpd/conf/httpd.conf
```

Change the listen port from 80 to 3002 per the task.

```bash
sudo systemctl enable --now httpd
systemctl status httpd
```

Just to check that it's running and using the right port.

Then copy the files over and set permissions:

```bash
sudo scp -r thor@jump-host:/home/thor/beta /var/www/html/beta
sudo scp -r thor@jump-host:/home/thor/cluster /var/www/html/cluster
sudo chown -R apache:apache /var/www/html/beta
sudo chown -R apache:apache /var/www/html/cluster
```

## Validation

Still on the app server see if we can curl the pages:

```bash
curl localhost:3002/beta/
curl localhost:3002/cluster/
```

Looks like they're being served, so we're good.

## Insights

Like I mentioned before, I don't have a ton of experience with Apache, but setting this up wasn't too much of a hassle.  The real issue I ran into was that initially I tried to `scp` the files from jump-host to the app server, which was giving me permissions problems because the app server user couldn't write to `/var/www/html` without using `sudo`.  But once I changed things around and ran `scp` directly from the app server, things worked perfectly.

I also did remember from before that the ownership for the files needed to be `apache:apache` so I set that and was good to go.

Or I would have been good to go, but initially I did a `curl` to `localhost:3002/beta` without the trailing slash which gave me a 301 permanent redirect.  When I included the trailing slash, things were fine.
