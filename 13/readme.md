# Day 13

## Task

We have one of our websites up and running on our Nautilus infrastructure in Stratos DC. Our security team has raised a concern that right now Apache’s port i.e 3004 is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:

1. Install iptables and all its dependencies on each app host.

2. Block incoming port 3004 on all apps for everyone except for LBR host.

3. Make sure the rules remain, even after system reboot.

## Solution

ssh into each host and install and enable iptables:

```bash
sudo yum install iptables -y
sudo systemctl enable --now iptables
```

then get the ip of the load balancer and allow it in iptables

```bash
getent hosts stlb01
sudo iptables -I INPUT -p tcp --dport 3004 -s 10.244.189.205 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 3004 -j DROP
sudo iptables-save | sudo tee /etc/sysconfig/iptables
```

## Validation

From the load balancer:

```bash
curl -I http://stapp01:3004
```

And inside the app hosts:

```bash
sudo cat /etc/sysconfig/iptables
```

to see that they're persisted

## Insights

More iptables.  The biggest issue was needing the ip for the load balancer, which I was able to do with just `getent hosts stlb01` and then put the IP in the iptables commands.

Persisting iptables was fun as well.  I swear there was a command like `iptables persist` but that might have been a debian thing.  A quick google led me to the nice little `tee` to persist them though.
