# Day 12

## Task

Our monitoring tool has reported an issue in Stratos Datacenter. One of our app servers has an issue, as its Apache service is not reachable on port 8089 (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue.

Use tools like telnet, netstat, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using command curl http://stapp03:8089 command from jump host.

Note: Please do not try to alter the existing index.html code, as it will lead to task failure.

## Solution

First I had to find the server that wasn't functioning.  I initially checed stapp03 like it said in the task, but it was working fine.  Then I found that stapp01 was the one with the issue, so I SSHd in there and checked out what was going on:

```bash
systemctl status httpd
```

Its saying the bind address is already in use, so I checked what was using it with:

```bash
sudo ss -tulnp
```

 was using port 8089, so I stopped and disabled it:

```bash
sudo systemctl stop sendmail
sudo systemctl disable sendmail
```

Then I restarted httpd and it was working fine.

```bash
sudo systemctl restart httpd
```

Back at the jump host I ran the curl command, but found that there was also a firewall issue.

So I SSHd back into stapp01 and checked the firewall rules with:

```bash
sudo iptables -L -n
```

Everything is being rejected, so I added a rule to allow traffic on port 8089:

```bash
sudo iptables -A INPUT -p tcp --dport 8089 -j ACCEPT
```

## Validation

Back at the jump host I ran the curl command against all 3 servers and they all worked fine:

```bash
curl http://stapp01:8089
curl http://stapp02:8089
curl http://stapp03:8089
```

## Insights

The task says to use things like telnet and netstat, but I've been using ss instead of netstat for a while now, so I used that instead.  Checking the status of httpd and then checking what was using the port was pretty simple, and I was able to quickly find and fix the issue.

I haven't used iptables since getting the LFCS certification the other year, so this was a good refresher.  Fortunately I spent a lot of time studying them for the exam, so I was able to pretty quickly get the issue fixed.

I could have persisted the iptables rule, but it wasn't necessary for the task, so I just left it as it is.