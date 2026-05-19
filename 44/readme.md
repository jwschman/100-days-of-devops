# Day 44

## Task

The Nautilus application development team shared static website content that needs to be hosted on the httpd web server using a containerised platform. The team has shared details with the DevOps team, and we need to set up an environment according to those guidelines. Below are the details:

a. On App Server 1 in Stratos DC create a container named httpd using a docker compose file /opt/docker/docker-compose.yml (please use the exact name for file).

b. Use httpd (preferably latest tag) image for container and make sure container is named as httpd; you can use any name for service.

c. Map 80 number port of container with port 3004 of docker host.

d. Map container's /usr/local/apache2/htdocs volume with /opt/itadmin volume of docker host which is already there. (please do not modify any data within these locations).

## Solution

ssh into stapp01 and:

```bash
vi /opt/docker/docker-compose.yml
```

Then add:

```yaml
services:
  httpd:
    image: httpd:latest
    container_name: httpd
    ports:
      - "3004:80"
    volumes:
      - /opt/itadmin:/usr/local/apache2/htdocs
```

Then start the container:

```bash
cd /opt/docker
docker compose up -d
```

## Validation

```bash
docker ps
```

There we should see that it's running httpd:latest, has the name httpd, and is mapping port 3004->80.

We can also run:

```bash
curl http://localhost:3004
```

to see the expected content.

## Insights

Like I mentioned in the previous task, I've been migrating some containers to my TrueNAS host, and I'm doing it all with compose files, so this was actually quite simple.  I actually used one of my `compose.yaml` files as reference and just changed the names, ports, and mounts to what was required by the task.
