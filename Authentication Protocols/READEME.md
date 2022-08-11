
## Task 1: OpenIDConnect, OAuth

Follow the Lab instruction in the zip file located at OIDC-OAuth2/intro-labs/README.md.



### Implementation:
This instuctions are contains following parts:

1. Authorization Grant Requests: In this part I followed the instuctions and I'll explain what I got and undrestant.

First of all I had to start and configure keyclock. 

* Keyclock: 
Keycloak is a server that we manage to use it. Applications are configured to point to and be secured by this server. Keycloak uses open protocol standards like OpenID Connect to secure applications. It will make users completely isolated from applications and applications never see a user’s credentials. Applications instead are given an identity token or assertion. These tokens can have identity information like username, address, email, and other profile data. They can also hold permission data so that applications can make authorization decisions.

I did the keyclock setup as it was written in setup readme.md. I used IntelliJ IDE and then set up it on Docker. For this, I edited the run_keycloak_docker.sh file to point to the correct directory on my system. Then I ran it and everything was fine.




![](https://i.imgur.com/pqsE2QB.png)
![](https://i.imgur.com/jktTX1r.png)
<p align = "center">
  <i>Figure 1: Successfully start of keyclock</i>
 </p>


After that, I had to test different OAuth Grants.
The OAuth 2.0 specification is a flexible authorization framework that describes a number of grants (“methods”) for a client application to acquire an access token (which represents a user’s permission for the client to access their data) which can be used to authenticate a request to an API endpoint. 

- **Client Credentials Grant**:
As it is obvious in figure 2, the client will send a request to obtain the access token.

I used curl to do the task:

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&client_id=library-client&client_secret=9584640c-3804-4dcd-997b-93593cfb9ea7" http://localhost:8080/auth/realms/workshop/protocol/openid-connect/token
```
This command will create a post request to the Authorization server (keyclock server) with some information like grant_type this is set to client_credential, client_id, and client_secret.
Some information can be retrieved then (figure 4)
expires_in is an integer representing the TTL of the access token. Token_type with the value Bearer which is the predominant type of access token used with OAuth 2.0. Scope with a space-delimited list of requested scope permissions.



![](https://i.imgur.com/UvnSxRJ.png)
<p align = "center">
  <i>Figure 2: client credential grant</i>
 </p>
    
![](https://i.imgur.com/8PN2mgw.png)

<p align = "center">
  <i>Figure 3: Sending post requetst to get access_token.</i>
 </p>
    
![](https://i.imgur.com/Za5meQn.png)
<p align = "center">
  <i>Figure 4: Returned information</i>
 </p>
    


- **Password Credentials Grant**:
We can see the steps in figure 5. To test is I used the following command. In this command, we will create a post request and send the username and password along with the request.

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=password&username=bwayne&password=wayne&client_id=library-client&client_secret=9584640c-3804-4dcd-997b-93593cfb9ea7" http://localhost:8080/auth/realms/workshop/protocol/openid-connect/token
```





![](https://i.imgur.com/KY2Om6I.png)
<p align = "center">
  <i>Figure 5: Password grant process</i>
 </p>
    
    
![](https://i.imgur.com/SV0A3va.png)
<p align = "center">
  <i>Figure 6: Sending post requetst to get access_token</i>
 </p>
    
![](https://i.imgur.com/pzdwRh0.png)
<p align = "center">
  <i>Figure 7: Returned information</i>
 </p>
    
    



- **Authorization Code Grant**: The flow starts with the authorization request, this redirects to the authorization server. Here the user logs in using his credentials and approves a consent page.
After successfully logging in a 302 HTTP redirect request with the authorization code is being sent through to the browser which redirects to the callback entry point provided by the client application.
Now the client application sends a token request to the authorization server to exchange the authorization code into an access token.

To test, I pasted the following URL into my browser which would send an authorized code when I enter the username and password (figure 9). 


```
http://localhost:8080/auth/realms/workshop/protocol/openid-connect/auth?response_type=code&client_id=demo-client&redirect_uri=http://localhost:9095/client/callback
```

```
The output:

http://localhost:9095/client/callback?session_state=954acdd4-0923-4b40-ba3a-4e57f29c1794&code=59845f16-d2bc-4b66-b1d5-b2e37f36a2f6.954acdd4-0923-4b40-ba3a-4e57f29c1794.58f0efec-2e91-4c63-823f-85280996f8a5

```
Then I used the code value and curl to get the access_token.

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=authorization_code&code=59845f16-d2bc-4b66-b1d5-b2e37f36a2f6.954acdd4-0923-4b40-ba3a-4e57f29c1794.58f0efec-2e91-4c63-823f-85280996f8a5&redirect_uri=http://localhost:9095/client/callback&client_id=demo-client&client_secret=b3ec9d3f-d1ee-4a18-b4ba-05d832c15293" http://localhost:8080/auth/realms/workshop/protocol/openid-connect/token
```
That command will create a post request with code value that we retrieved before and set the redirect URI and cliend_id and secret and send it to the server. Then, I received the Access_token in figure 10.





![](https://i.imgur.com/5M7KWbr.png)
<p align = "center">
  <i>Figure 8: Authorization code grant process</i>
 </p>
    
![](https://i.imgur.com/QOhosvZ.png)
<p align = "center">
  <i>Figure 9: Phase 1 for authorization code grant test </i>
 </p>
    
![](https://i.imgur.com/GccqcUZ.png)    
<p align = "center">
  <i>Figure 10: Access_token </i>
 </p>   




2. Authorization Code Grant Flow Demo: First I had to update my JDK and configure it to be able to build and run the code for both this one and the Github API part. Then I ran the file "com/example/authorizationcode/client/AuthorizationCodeDemo.java"

Then I faced figure 12's page which had some information. After that inside the login page, I used the given username and password (figure 13). Finally, I could request for Access_token (Figure 14) and the result is in figure 15.




![](https://i.imgur.com/nRYqtpE.jpg)
<p align = "center">
  <i>Figure 11: Run the java program </i>
 </p>
    
    
![](https://i.imgur.com/ee8pRjB.png)
<p align = "center">
  <i>Figure 12: Step 1 authorization code grant demo </i>
 </p>   
    
![](https://i.imgur.com/9J4zEMY.png)    
<p align = "center">
  <i>Figure 13: User login information step 2  </i>
 </p>
    
![](https://i.imgur.com/zhxMAU7.png)
<p align = "center">
  <i>Figure 14: Request for Access_token  </i>
 </p>
    
    
![](https://i.imgur.com/l9OUIXP.jpg)
<p align = "center">
  <i>Figure 15: Access_token retrieved </i>
 </p>   
    


3. Connecting a client app to GitHub API using OAuth 2.0: I followed the instructions for this one too. First I got client_ID and client_secret from my GitHub account and put them inside the application.yml file. Then I ran the java file to start it.
When I load "localhost:9090" automatically I was redirected to the GitHub page (Figure 17). After getting authorized I was redirected to the page that showed some information.




![](https://i.imgur.com/RtRbscp.png)    
<p align = "center">
  <i>Figure 16: Get client_id and secret from github </i>
 </p>
    
![](https://i.imgur.com/VAE12Fb.png)    
<p align = "center">
  <i>Figure 17: Redirection to the github </i>
 </p>
    
![](https://i.imgur.com/4TTPMzY.png)
<p align = "center">
  <i>Figure 18: Returned information  </i>
 </p>  
    



## Task 2: LDAP

Setup a network which its domain authenticates computers based on LDAP Protocol. You need to have:
- One Linux Machine with OpenLDAP Server installed ( you can use any graphical interface that you like)
- Two Linux Machines with LDAP clients installed.
- Two users which are created on the LDAP Server and are able to login to any given client.



### Implementation:

I couldn't completely configure it.

First I setup the server:

```
#Installing:
sudo apt-get install slapd ldap-utils
asked me a administrative password : 1234

#configure it by myself:
sudo dpkg-reconfigure slapd

#Install phpLDAPadmin web interface:
sudo apt-get install phpldapadmin

```

Add base dn for Users and Groups:
and then running:

```
ldapadd -x -D cn=admin,dc=example,dc=com -W -f basedn.ldif
```


    
![](https://i.imgur.com/tsIeFSP.png)
<p align = "center">
  <i>Figure 19: create base dn    </i>
 </p>
    
![](https://i.imgur.com/0R5Dphm.png)    
<p align = "center">
  <i>Figure 20: phpLDAPadmin    </i>
 </p>


I couldn't work with phpLDAPadmin (couldn't load the section to add users)

So, I changed to LDAP Account Manager.


```
sudo apt -y install ldap-account-manager
```



![](https://i.imgur.com/o3q7R2W.png)
<p align = "center">
  <i>Figure 21: LDAP Account Manager</i>
 </p>


Then, first of all I tried to create a a group:



![](https://i.imgur.com/AAykzPL.png)
<p align = "center">
  <i>Figure 22: Group creation</i>
 </p>


Creating the users and setting passwords:



![](https://i.imgur.com/EObBGaj.jpg)
![](https://i.imgur.com/DEZYFJW.jpg)
![](https://i.imgur.com/l7hNK5F.png)
<p align = "center">
  <i>Figure 23: Create users    </i>
 </p>



server part done!


Now, start the client part:

Add LDAP server address to /etc/hosts:

```
sudo nano /etc/hosts
192.168.43.221 ldap.example.com

#install LDAP client utilities
sudo apt -y install libnss-ldap libpam-ldap ldap-utils

```

Next step was: edit /etc/nsswitch.confand add ldap authentication to passwd and group lines. After that, Modify the file /etc/pam.d/common-password. Remove use_authtok.

Add this inside the /etc/pam.d/common-session to create a home directory for users.


```
session required pam_mkhomedir.so skel=/etc/skel umask=0022

```

After that I tried to connect to the users but I kept getting the "No passwd entry for user". I searched a lot and reinstall everything and tried again with different instructions on the internet but I was not successful to make the connection correctly.

---
## References:

1. [Keycloak features and concepts](https://www.keycloak.org/docs/latest/server_admin/)
2. [A Guide To OAuth 2.0 Grants](https://alexbilbie.com/guide-to-oauth-2-grants/)
3. [Install OpenLDAP and phpLDAPadmin on Ubuntu 22.04|20.04|18.04](https://computingforgeeks.com/install-and-configure-openldap-phpldapadmin-on-ubuntu/)
4. [Configure LDAP Client on Ubuntu 22.04|20.04|18.04|16.04](https://computingforgeeks.com/how-to-configure-ubuntu-as-ldap-client/)
5. [Install LDAP Account Manager on Ubuntu 22.04|20.04|18.04|16.04](https://computingforgeeks.com/install-and-configure-ldap-account-manager-on-ubuntu/)
6. [Configure LDAP Client.](https://www.server-world.info/en/note?os=Ubuntu_16.04&p=openldap&f=3)
7. [How to Add LDAP Users and Groups in OpenLDAP on Linux](https://www.thegeekstuff.com/2015/02/openldap-add-users-groups/)
8. [How Install and Configure OpenLDAP on CentOS / RHEL Linux](https://www.thegeekstuff.com/2015/01/openldap-linux/)
9. 
