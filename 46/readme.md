# Day 46

## Task

The Nautilus Application development team recently finished development of one of the apps that they want to deploy on a containerized platform. The Nautilus Application development and DevOps teams met to discuss some of the basic pre-requisites and requirements to complete the deployment. The team wants to test the deployment on one of the app servers before going live and set up a complete containerized stack using a docker compose fie. Below are the details of the task:

    On App Server 3 in Stratos Datacenter create a docker compose file /opt/sysops/docker-compose.yml (should be named exactly).

    The compose should deploy two services (web and DB), and each service should deploy a container as per details below:

For web service:

a. Container name must be php_web.

b. Use image php with any apache tag. Check here for more details.

c. Map php_web container's port 80 with host port 5001

d. Map php_web container's /var/www/html volume with host volume /var/www/html.

For DB service:

a. Container name must be mysql_web.

b. Use image mariadb with any tag (preferably latest). Check here for more details.

c. Map mysql_web container's port 3306 with host port 3306

d. Map mysql_web container's /var/lib/mysql volume with host volume /var/lib/mysql.

e. Set MYSQL_DATABASE=database_web and use any custom user ( except root ) with some complex password for DB connections.

    After running docker-compose up you can access the app with curl command curl <server-ip or hostname>:5001/

For more details check here.

Note: Once you click on FINISH button, all currently running/stopped containers will be destroyed and stack will be deployed again using your compose file.

## Solution

ssh into stapp03 and create the dockerfile:

```bash
vi /opt/sysops/docker-compose.yml
```

And add this to fit all the requirements:

```yaml
services:
  web:
    container_name: php_web
    image: php:apache
    ports:
      - "5001:80"
    volumes:
      - /var/www/html:/var/www/html

  db:
    container_name: mysql_web
    image: mariadb:latest
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      MYSQL_DATABASE: database_web
      MYSQL_USER: mysql_user
      MYSQL_PASSWORD: my_complex_PassWORD123!
      MYSQL_ROOT_PASSWORD: my_complex_PassWORD123!
```

Then just do:

```bash
cd /opt/sysops
docker compose up -d
```

## Validation

```bash
curl localhost:5001
```

## Insights

Quite a long looking task.  But actually it was just writing a Dockerfile following the specs shown in the task.  The most interesting part is that it has multiple containers, but that's just as simple as adding another entry in the `services:` group: one for `web` and one for `db`

I actually took a look at one of my multi-container Compose files that I have in my homelab just to make sure I was doing it right, and sure enough everything worked when I did `docker compose up` on the first time.
