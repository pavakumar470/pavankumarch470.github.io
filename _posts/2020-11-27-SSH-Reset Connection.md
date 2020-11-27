---
published: true
---
ssh(secure shell connection) is a tool which helps us in connecting to the linux machine remotely where for each shell we will have the tty which will be used for the input/output to shell.<br/>
<br/>
But with this everyone can login which will make the ssh vulnerable.<br/>
<br/>
we can perform the below to secure the ssh connections:<br/>
1.we can changed the port of the ssh where the default port is 22.<br/>
2.limit the max number of ssh connections for the user with ulimit values.<br/>
<br/>
But recently i faced an issue where even after making sure that the max number of allowed ssh connections are high enough while running few of the scripts for running the commands remotely on the machines i have observed some of the ssh commands in the script are running fine but where as the most of them where failing with the "ssh connection refused error".<br/>
<br/>
After running the script multiple time i found that it is failing while running the 4 or 5th ssh command in a row when i have debugged little further i found that while running the ssh commands the packets are getting rejected/dropped when we have seen the tcpdump on the ssh port i didn't find much of an information.<br/>
<br/>
but when checked the iptables rules for the 22 port I found the below rules:<br/>
```java
/usr/sbin/iptables -I INPUT -p tcp --dport 22 -i eth0 -m state --state NEW -m recent --set
/usr/sbin/iptables -I INPUT -p tcp --dport 22 -i eth0 -m state --state NEW -m recent  --update --seconds 60 --hitcount 4 -j REJECT --reject-with tcp-reset
```
<br/>
the above rules tells us that if there more than 3 to 4 ssh connection is less than a 60 seconds then reject/drop those packets. where this rules are mainly set to make sure to prevent brute-force attacks using the ssh.<br/>

