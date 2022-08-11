## Task 1: TLS Handshake

1. Using openssl , establish a TLS connection to facebook.com . Look at the parameters of your connection and briefly describe them in your report.
2. Establish the connection again and intercept TLS handshake using tcpdump/wireshark. Describe all packets involved in the TLS connection in your report.
3. What version of TLS is used? Describe which versions are not safe to use and under which conditions.
4. What ciphersuite is used? Make a small list of ciphersuite that you would recommend to use and why.


### Implementation:

1. I used the command to establish a TLS connection:

```
openssl s_client -connect facebook.com:443

```

I received the following infromation:

Information about server certificate (Root, intermediate and server certs):


    
![](https://i.imgur.com/NjWtTZJ.png)
<p align = "center">
  <i>Figure 1: server certificate info</i>
 </p>   


Then, all the certificates presented by the server in the order in which they were delivered (Intermediate certificate and server certificate):
The first line is Subject and second line is Issuer for each certificate.


    
![](https://i.imgur.com/1LwBhHI.png)
<p align = "center">
  <i>Figure 2: certificate chain</i>
 </p>


Next, server certificate:


    
![](https://i.imgur.com/0paUOWo.png)
<p align = "center">
  <i>Figure 3: server certificate</i>
 </p>


Information about TLS connect. We can see the TLS version is 1.2 and cipher ECDHE-RSA-AES128-GCM-SHA256.



![](https://i.imgur.com/QIUAyVc.png)
<p align = "center">
  <i>Figure 4: TLS connection information</i>
 </p>


2. Facebook is blocked in Iran and I should use VPN for it and I cant see the the TLS handshake. Only client hello is sending to my VPN server and all the information is comming from the server. So, tls handshake packets were not available there.
Due to this problem, I tryed the intercepting for google.com.



![](https://i.imgur.com/hN2lCtU.png)
<p align = "center">
  <i>Figure 5: Curl google.com</i>
 </p>
    
![](https://i.imgur.com/fq2bH4r.png)
<p align = "center">
  <i>Figure 6: Use wireshark to intercept tls handshake  </i>
 </p> 
    
![](https://i.imgur.com/bHZ4vuI.png)    
<p align = "center">
  <i>Figure 7: tls handshake packets</i>
 </p>


I was expecting to see all the steps like it is available in figure 6.

First our system will send client hello request. The client sends a message to the server saying that “I’d like to set up an encrypted session. Here is a list of cipher suites and the SSL/TLS versions I am willing to use. I am also sending my public key which can be used by you at a later point in time”

Then, Server Hello.
The Server responds with “Hey there! Let’s use this particular cipher suite from your list. I also checked that I can use your TLS version, so we’re good to go. Now here’s is my certificate, including my public key.”

After the server and client agress on the SSL/TLS version and cipher suite, then server sends two things. The first is its SSL/TLS certificate to the client. The second thing the server sends is its public key and signature (figure 8)

Now the client receives the server’s public key and generate a new session key (aka pre-master key) encrypted with the public key and sends it to the server (figure 9)

The change cipher spec message is sent by both the client and server to notify the receiving party that subsequent records will be protected under the just-negotiated CipherSpec and keys.
    
![](https://i.imgur.com/sLhLJMK.png) 
<p align = "center">
  <i>Figure 8: Certificate and server key exchange details</i>
 </p>
    
![](https://i.imgur.com/pBLLQmh.png)
<p align = "center">
  <i>Figure 9: Client key exchange</i>
 </p>    


3. TLS v 1.2.
TLS 1.0 is an old and outdated protocol. TLS 1.1 dates back to 2006, and shortly after, TLS 1.2 was developed to address numerous security concerns in TLS 1.0 and TLS 1.1. TLS 1.2 is still secure to this day, TLS 1.3 has been released with improvement to both security and performance.

4. Cipher Suite: 


## Task 2: Man In The Middle

1. Setup a TLS proxy - mitmproxy
a. There are several modes it can operate. You should use transparent mode.
b. Describe setup steps and show that are able to read transmitted data.
2. Intercept TLS handshake before proxy and after proxy and describe how MITM is performed.
a. When do you think TLS proxy is used for security purposes in the industry?
b. What are techniques that are used to protect applications (desktop, web,
mobile, etc.) from TLS proxying?
3. Proxy allows you not only to read data, but also modify it on the fly. Think of a
scenario when it can be dangerous and try to demonstrate that.



### Implementation:

1. To create the transparent proxy I followed the following lines:

```
#Enable IP forwarding:
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv6.conf.all.forwarding=1

#Disable ICMP redirects
sysctl -w net.ipv4.conf.all.send_redirects=0

#Because I wanted to use it on a same machine:
#Create a user for the proxy and install the proxy for that specific user:

sudo useradd --create-home mitmproxyuser
sudo -u mitmproxyuser -H bash -c 'cd ~ && pip install --user mitmproxy'


#Creating the IP table rules to redirect packets to the proxy:
iptables -t nat -A OUTPUT -p tcp -m owner ! --uid-owner mitmproxyuser --dport 80 -j REDIRECT --to-port 8080
iptables -t nat -A OUTPUT -p tcp -m owner ! --uid-owner mitmproxyuser --dport 443 -j REDIRECT --to-port 8080
ip6tables -t nat -A OUTPUT -p tcp -m owner ! --uid-owner mitmproxyuser --dport 80 -j REDIRECT --to-port 8080
ip6tables -t nat -A OUTPUT -p tcp -m owner ! --uid-owner mitmproxyuser --dport 443 -j REDIRECT --to-port 8080

##Finally running the mitmproxy:
sudo -u mitmproxyuser -H bash -c '$HOME/.local/bin/mitmproxy --mode transparent --showhost --set block_global=false'

```


    
![](https://i.imgur.com/UkLSzQ9.png)
<p align = "center">
  <i>Figure 10: MITM proxy running</i>
 </p>


2. Before runnin the proxy everything was like before (Figure 11).



![](https://i.imgur.com/oqO5LD8.png)
<p align = "center">
  <i>Figure 11: Before running the proxy</i>
 </p>


After that I tried and I got connection insecure warning.

I tried adding the certificate inside browser certificate authorities but no change happened.



![](https://i.imgur.com/F9OlyOv.png)    
![](https://i.imgur.com/S6qrcla.png)    
<p align = "center">
  <i>Figure 12: After proxy</i>
 </p>
    
![](https://i.imgur.com/yPRx4S4.png)    
<p align = "center">
  <i>Figure 13: MITM-proxy certificate  </i>
 </p>  


a. A TLS proxy server protects against denial-of-service (DoS) attacks and other security threats.A connection is being intercepted by a TLS proxy when it inspects incoming traffic to block malicious connections. A high speed redundant network is used to run the TLS proxy to protect against distributed DoS (DDoS) attacks.

b. A client does not blindly trust a CA send within TLS connection. It trusts only the root CA stored locally and any certificate/CA has to be directly or indirectly issued by these trusted CA. That means it will not work if a proxy just creates a new certificate and sends its own root CA together with the certificate. Instead this proxy CA has to be explicitly installed into the browser/OS.

Thus while you cannot make a proxy to not intercept and modify the TLS connection this modification will be detected immediately and the connection will be stopped before any data are transferred. Only if you explicitly trust the proxy by importing the proxy CA the TLS interception is possible.


## Task 3: x509

1. Obtain a cert chain for www.facebook.com (f.e. using openssl):
a. Does the cert chain contain the root cert? Where are trusted certs stored?
b. Extract certs and place: root CA in root.crt, intermediate CA in int_ca.crt, and
server cert in server.crt.
2. Using the contents of obtained certificates, create your own chain that mimics the
original one, they should look almost identical.
a. Document your steps and describe the meaning of certificates attributes.
b. Include created certificate chain into the report as appendix - both in base64
and human readable form.



### Implementation:


To get the chain:

```
openssl s_client -connect facebook.com:443 -showcerts
```
No, it doesnt have root certificate cause it is inside our systems localy.

The root certificate was in "/etc/ssl/certs"
After that I got the server certificate and intermediate certificate from the result of the command and save them inside two files (They are inside the folder I sent)

2. I used the commands below to create the Root certificate, self sign it. Then create intermediate certificate and sign it with root key.

```
#Create key:
 openssl genrsa -out rootCAKey.pem 2048

#Create root cert:
openssl req -x509 -sha256 -new -nodes -key rootCAKey.pem -days 2555 -out rootCACert.pem


#generate key and generate intermediate certificate waiting for signing
 openssl genrsa -out interCAKey.pem 2048
 openssl req -sha256 -new -key interCAKey.pem -days 3650 -out interCACert.pem

to sign the intermediate:
sudo openssl ca -config openssl.cnf -days 3650 -notext -in interCACert.pem -out inter-signed-cert.pem -extensions ca_ext


#before signing:
 touch crt.srl
 echo 1000 > crt.srl 
 touch ./db.attr
 touch ./db

create the chain:
cat inter-signed-cert.pem rootCACert.pem > ca-chain-bundle.cert.pem
```
CN: CommonName
OU: OrganizationalUnit
O: Organization
L: Locality
S: StateOrProvinceName
C: CountryName

To create somehow similar ones, I used the same names as Organization, etc.




![](https://i.imgur.com/6DGp6hm.png)
<p align = "center">
  <i>Figure 14: Chain</i>
  </p>


---
## References:

1. [SSL/TLS Handshake Explained With Wireshark Screenshot](https://www.linuxbabe.com/security/ssltls-handshake-process-explained-with-wireshark-screenshot)
2. [Deep dive into TLS/SSL Handshake using WireShark](https://blog.devgenius.io/deep-dive-into-tls-ssl-handshake-using-wireshark-5abfe83560ce)
3. [TLS Proxy Definition](https://avinetworks.com/glossary/tls-proxy/)
4. 
