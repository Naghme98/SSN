# Introduction:

Select Snort, Suricata or Zeek as your IDS/IPS of your choice. 

Create an small network of three VMs. 1 attacker, 1 Server and 1 as IDS/IPS. The remaining part of the topology is of your selection. 

Install and configure IDS/IPS on its specific vm and update its signatures/rules if required. 

## Tasks: 

1. Create a policy/rule to block all incoming ping packets.
2. Verify that newly created rule/policy can correclty detect and block incoming ping packets from attacker machine.
3. Write 5 more rules/policies of your own that can detect different attacks ( possible attacks). 
4. Show the new 5 rules and explain how the attacks would work and how the rule/policy would be able to detect it.
5. Describe how you triggered the alert for all newly created rules.
6. Attach the alert itself for each given rule/policy to the report in readable text.
7. Create one rule/policy which would be triggered if a server vm / IDS/IPS vm would browse to microsoft.com website.
8. Answer the following questions:
   1. What is a zero-day attack?
   2. Can IDS/IPS detect zero-day attack? if yes why? if no why?
   3. Given a network that has 1 million connections daily where 0.1% are attacks. If the IDS has a true positive rate of 95% what false positive alarm rate do we need to achieve to ensure the probability of an attack, given an alarm is 95%?



### Implementation:

I installed the snort IDS/IPS tool using apt.
And, changed the $HOME_NET variable to my server vm address inside snort.conf. 10.211.55.2 is attacker machine address

1. Insert the following rule in /etc/snort/rules/local.rules.

```
alert icmp 10.211.55.2 any -> $HOME_NET any (msg:"ICMP connection attempt"; sid:1000002; rev:1;)
```
That means alert if any ICMP packets from any address and port went to server VM. But now I saw the word "block" so, I should change the rule:

```
reject icmp 10.211.55.2 any -> $HOME_NET any (msg:"ICMP connection attempt"; sid:1000002; rev:1;)
```

That says to block the packet, log, and then send a TCP reset if the protocol is TCP or an ICMP port unreachable message if the protocol is UDP.

2. You can see that ping packets were successfully blocked



![](https://i.imgur.com/C1JrB4L.png)
<p align = "center">
  <i>Figure 1: Snort log/i>
 </p>

![](https://i.imgur.com/AgRp3lr.png)
<p align = "center">
  <i>Figure 2: test the blocking ICMP/i>
 </p>




3. & 4. & 5. & 6. I did the following attacks and rules:

    - **DOS attack**: For this one, I tried the the bellow rule:
        
```
alert icmp any any -> $HOME_NET any (msg:"ICMP flood"; sid:1000005; rev:1;classtype:icmp-event; detection_filter:track by_dst, count 500, seconds 3;)
```


* alert – Rule action. Snort will generate an alert when the set condition is met.
* any – Source IP. Snort will look at all sources.
* any – Source port. Snort will look at all ports.
* -> – Direction. From source to destination.
* any – Destination port. Snort will look at all ports on the protected network Rule Options
* msg:”ICMP flood” – Snort will include this message with the alert.
* sid:1000001 – Snort rule ID. 
* rev:1 – Revision number. This option allows for easier rule maintenance.
* classtype:icmp-event – Categorizes the rule as an “icmp-event”, one of the predefined Snort categories. This option helps with rule organization.
* detection_filter:track by_dst -  Snort tracks the destination IP address for detection.
* seconds 3 - sampling period is set to 3 seconds
* count 500 - if during the sampling period Snort detects more than 500 requests then we will receive the alert

For creating the attack I used the hping3 tool you can see in figure.




![](https://i.imgur.com/4QB2XQg.png)
<p align = "center">
  <i>Figure 3: Create ICMP flood using hping3/i>
 </p>

![](https://i.imgur.com/sI33itx.png)
<p align = "center">
  <i>Figure 4: Snort alert for DOS attack/i>
 </p>



As you can see it will create a flood to destination with the destination's IP as the source address.

- *Port scanning attack:* One of the know attacks is scanning the open ports of the victim machine using Nmap. Let’s assume the attacker may choose TCP scanning for network enumeration then in that situation we can apply the following rule in the snort local rule file. The rule is only applicable for port 22.

```
alert tcp any any -> $HOME_NET 22 (msg: "NMAP TCP Scan";sid:10000004; rev:2;)
```

Then to test it:

```
nmap -sT -p22 Server IP
```



![](https://i.imgur.com/JXM7el1.png)
<p align = "center">
  <i>Figure 5: TCP Port scanning/i>
 </p>

![](https://i.imgur.com/1EinjRQ.png)
<p align = "center">
  <i>Figure 6: Snort alert for port scanning/i>
 </p>




- **Error Based SQL Injection**: In Error based SQL injections the attacker use single quotes (‘) or double quotes (“) to break down SQL query to identify its vulnerability. So, I add the line in the rules:

```
alert tcp any any -> $HOME_NET 80 (msg: "Error Based SQL Injection Detected"; content: "%27" ; sid:10000008; )
alert tcp any any -> $HOME_NET 80 (msg: "Error Based SQL Injection Detected"; content: “%22" ; sid:10000009; )
```
It would search in the content for quotes (‘) or double quotes (“) and if there was one, create an alert.


- **Boolean Based SQL Injection**: In Boolean based SQL injections the attacker use AND /OR  operators where the attacker will try to confirm if the database is vulnerable to Boolean SQL Injection by evaluating the results of various queries return either TRUE or FALSE. Here I had applied a filter for content “and” & “or” to be captured. Here nocase denotes not case sensitive it can be as AND/and, OR/or.

```
alert tcp any any -> $HOME_NET 80 (msg: "AND SQL Injection Detected"; content: "and" ; nocase; sid:10000006; )
alert tcp any any -> $HOME_NET 80 (msg: "OR SQL Injection Detected"; content: "or" ; nocase; sid:10000007; )
```

For testing the both Boolean and Error based SQL injections I used the next request inside the browser:

```
#Error based:
http://10.211.55.14/index.php?id=1'
http://10.211.55.14/index.php?id=1"

#Boolean based:
http://10.211.55.14/index.php?id=1' AND 1=1 --+

```

And the alerts would be like this:



![](https://i.imgur.com/MKJr6bh.png)
<p align = "center">
  <i>Figure 7: Snort alerts for SQL injections/i>
 </p>




7. For this one, I did create the rule:

```
alert tcp any any <> any any (content:"microsoft.com ";sid:361000000;rev:1;msg:”microsoft.com detected";)
```

It would search inside the content for "microsoft.com" and if any match happen, will create an alert:



![](https://i.imgur.com/NJXOa84.png)

![](https://i.imgur.com/SLKkMo5.png)
<p align = "center">
  <i>Figure 8: Snort alert for accessing to microsoft page from snort vm and server vm/i>
 </p>


8. When Softwares have vulnerabilities that are unknown to its developers and there are no security patches for them and an attacker finds it and attacks it, would call it zero day attack.
9. I think they cannot cause they work base on signatures and rules and when the developer does not know there is such a vulnerability inside his work, how can he create such a signature to make the IDS tool detect the attack? 
10. The answer:


![](https://i.imgur.com/2thOS3C.jpg)
![](https://i.imgur.com/6WEiHo9.jpg)

<p align = "center">
  <i>Figure 9: Q10 answer/i>
 </p>




