# Day 47

## Task

A python app needed to be Dockerized, and then it needs to be deployed on App Server 3. We have already copied a requirements.txt file (having the app dependencies) under /python_app/src/ directory on App Server 3. Further complete this task as per details mentioned below:

    Create a Dockerfile under /python_app directory:
        Use any python image as the base image.
        Install the dependencies using requirements.txt file.
        Expose the port 8089.
        Run the server.py script using CMD.

    Build an image named nautilus/python-app using this Dockerfile.

    Once image is built, create a container named pythonapp_nautilus:
        Map port 8089 of the container to the host port 8098.

    Once deployed, you can test the app using curl command on App Server 3.

curl http://localhost:8098/

## Solution

ssh into stapp03 and:

```bash
cd /python_app
vi Dockerfile
```

And we just follow the requirements using the guide here: <https://docs.docker.com/guides/python/containerize/>

```dockerfile
FROM python:3.12-slim

WORKDIR /app

# copy the requirements
COPY src/requirements.txt .

# install those dependencies
RUN pip install -r requirements.txt

# Expose designated port:
EXPOSE 8089

# Copy the script:
COPY src/ .

# run the script
CMD ["python", "server.py"]
```

Then build and run the image:

```bash
docker build -t nautilus/python-app .
docker run -d -p 8098:8089 --name pythonapp_nautilus nautilus/python-app
```

## Validation

First check that the container is running, then do the curl if it is:

```bash
docker ps
curl http://localhost:8098/
```

## Insights

It seems like this was the final exam for the Docker section, and it really wasn't that much.  I don't have a ton of experience installing dependencies, but fortunately there's plenty of guides online.  The official dockerdocs "Containerize a Python application" had everything I needed, in a much fancier version than what I actually went with.  I just stripped it down to the requirements from the task and everything worked fine.
