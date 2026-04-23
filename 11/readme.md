# Day 11

## Task

The Nautilus application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in Stratos DC. After an internal team meeting, they have decided to use the tomcat application server. Based on the requirements mentioned below complete the task:

a. Install tomcat server on App Server 2.

b. Configure it to run on port 5002.

c. There is a ROOT.war file on Jump host at location /tmp.

Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e curl http://stapp02:5002

## Solution

First we can scp the `ROOT.war` file from the Jump host to App Server 2:

```bash
scp /tmp/ROOT.war steve@stapp02:/tmp
```

SSH in to App Server 2 and install tomcat:

```bash
sudo yum install tomcat -y
```

Edit the server.xml file to change the port to 5002:

```bash
sudo vim /etc/tomcat/server.xml
```

change the Connector port from 8080 to 5002:

```xml
port="5002"
```

Enable and start

```bash
sudo systemctl enable --now tomcat
```

Change ownership and move the ROOT.war file to the webapps directory:

```bash
sudo chown tomcat:tomcat /tmp/ROOT.war
sudo mv /tmp/ROOT.war /var/lib/tomcat/webapps/
```

## Validation

still inside App server 2, check that the application is running:

```bash
curl http://localhost:5002
```

We should get the HTML content of the webpage.  Also check from back in the Jump host:

```bash
curl http://stapp02:5002
```

## Insights

I've never installed or used tomcat before, so this was a good learning experience.  A couple quick searches led me to the correct package name and the location of `server.xml` which is where the port is configured.

Then I had to find where the webapps directory is, which is where the `ROOT.war` file needed to be moved to.  Before moving it, I changed the ownership to tomcat, which was necessary for it to be able to read and deploy the application.
