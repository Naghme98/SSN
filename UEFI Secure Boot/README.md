# Introduction:

UEFI Secure Boot ensures by means of digital signatures that the code that loads the operating system and the operating system itself have not been tampered with. It is used by Microsoft to guarantee safe startup, but also by Ubuntu and Fedora.
In this assignment you will learn how Ubuntu implements secure booting of the Linux kernel.
You will need access to a Ubuntu machine that was installed with UEFI Secure Boot enabled (like your desktop PC).
Ubuntu provides the following description of its implementation of UEFI Secure Boot at https://wiki.ubuntu.com/UEFI/SecureBoot:

> “In order to boot on the widest range of systems, Ubuntu uses the following chain of trust:
>  1. Microsoft signs Canonical's “shim” 1st stage bootloader with their “Microsoft Corporation UEFI CA”.When the system boots and Secure Boot is enabled, firmware verifies that this 1st stage bootloader (from the “shim-signed” package) is signed with a key in DB (in this case “Microsoft Corporation UEFI CA”)
>  2. The second stage bootloader (grub-efi-amd64-signed) is signed with Canonical's “Canonical Ltd. Secure Boot Signing” key. The shim 1st stage bootloader verifies that the 2nd stage grub2 bootloader is properly signed.
>  3. The 2nd stage grub2 bootloader boots an Ubuntu kernel (as of 2012/11, if the kernel (linux-signed) is signed with the “Canonical Ltd. Secure Boot Signing” key, then grub2 will boot the kernel which will in turn apply quirks and call ExitBootServices. If the kernel is unsigned, grub2 will call ExitBootServices before booting the unsigned kernel)
>  4. If signed kernel modules are supported, the signed kernel will verify them during kernel boot”



The overall task for this assignment is to verify that Ubuntu actually uses the following chain of trust and that the signatures are correct. The above steps will be referred to as “Step 1”, “Step 2”, etc. in the text that follows


## Task 1: Firmware Database

1. Extract the Microsoft Certificate that belongs to the key reffered to in Step 1 from the UEFI firmware, and show its text representation.
2. Is this certificate the root certificate in the chain of trust? What is the role of the Platform Key(PK) and other vairables?


### Answers:

1. This key is stored in DB and we need to first export all the certificates inside the DB and then get the right one from it the steps would be like:

```
## get the whole certificates inside DB and save the output as "shim.esl"

efi-readvar -v db  -o shim.esl

## extract every key inside the DB and save the files with the name of db-*

sig-list-to-certs shim.esl db

## use openssl to read the content of this certificate:

openssl x509 -inform der -in db-2.der -noout -text > shim-db-2.txt

```
We can see that Microsoft Corporation Third Party Marketplace Root signed the subject that is for Microsoft Corporation UEFI CA 2011

2. No it is not. As I understand, the chain of trust should be a self-signed certificate but it is not. Also, the process of signing will start with PK. After that, PK will sign KEK (The Key Exchange Key). The KEK is used to sign keys so that the firmware accepts them as valid when entering them into the database. Then we have DB is a database of keys that each key is signed with KEK. DBX contains keys and hashes that correspond to known malware or otherwise undesirable software.

So, we can say that this certificate is not the root and itself is signed with KSK.


![](https://i.imgur.com/pkLOjso.png)
<p align = "center">
  <i>Figure 1: efi-readvar command output</i>
 </p>
    
![](https://i.imgur.com/6Y1lOFM.png)    
<p align = "center">
  <i>Figure 2: The output for the commands I explained</i>
 </p>
    
![](https://i.imgur.com/IMybzzY.png)    
<p align = "center">
  <i>Figure 3: Text representation of the Microsoft CA certificate</i>
 </p>


## Task 2: SHIM

3. Verify that the system indeed boots the shim boot loader in the first stage. What is the full path name of this boot loader?
4. Verify that the shim bootloader is indeed signed with the "Microsoft Corporation UEFI CA" key.
5. What is the exact name of the part of the binary where the actual signature is stored?
6. In what standard cryptographic format is the signature data stored?
7. Extract the signature data from the shim binary using dd. Add 8 bytes to the location as given in the data directory to skip over the Microsoft WIN CERTIFICATE structure header
8. Show the subject and issuer of all X.509 certificates stored in the signature data. Draw a digram relating these certificates to the "Microsoft Corporation UEFI CA"


### Answers:

3. For verifying, I used the command below and in the output we can see that it is indeed using shim in it's boot order (Figure 4)

```
efibootmgr -v
```
The full path for shim is: /boot/efi/EFI/ubuntu/shimx64.efi



![](https://i.imgur.com/SZkMztM.png)
<p align = "center">
  <i>Figrue 4: ubuntu boot order</i>
 </p>
    
![](https://i.imgur.com/ViCGjt1.png)
<p align = "center">
  <i>Figure 5: Full path to shim bootloader</i>
 </p>


4. We need to convert .DER file to PEM and then use sbsigntool to verify

```
openssl x509 -inform der -in db-2.der -out db-shim-pem.pem

## verify:
sudo sbverify - - cert db-shim-pem.pem shimx64.efi
```

![](https://i.imgur.com/NFTxj1V.png)
<p align = "center">
  <i>Figure 6: Verify shim is signed with that certificate</i>
 </p>


5. From the document, we can understand that the address for the exact signature is stored in Certificate Table that is inside the Data Directories. Then we have the signature stored in the bCertificate binary array.

![](https://i.imgur.com/a0PEy3Q.png)    
<p align = "center">
  <i>Figure 7: Overview of the Windows PE file format and the Authenticode signature format</i>
 </p>

6. Data is stored in PKCS#7 format which is is the specific standard used for the generation and verification of digital signatures and certificates managed by a PKI (Public Key Infrastructure).


7. I used the following command to extract the signature.

```
## pyew command to retrieve the exact size and address:

pyew.pe.OPTIONAL_HEADER.DATA_DIRECTORY[4].VirtualAddress
pyew.pe.OPTIONAL_HEADER.DATA_DIRECTORY[4].Size 

## 947152 is the starting point for this certificate (+8) and 8504 is length of it (-8)

sudo dd if=shimx64.efi skip=947152 of=sig.p7b bs=1 count=8504 
```

![](https://i.imgur.com/ahDhH7G.png)
    
![](https://i.imgur.com/ZejNtpQ.png)
    
![](https://i.imgur.com/Bgo5tdv.png)
<p align = "center">
  <i>Figure 8: using pyew for shimx64.efi</i>
 </p>

8. We should change the p7b file format to pem file:

```
openssl pkcs7 -print_certs -text -in sig.p7b -out cert.pem -inform der

```
In the file there were two certificates:

Figure 9: 
* Issuer: Microsoft Corporation UEFI CA 2011
* subject: Microsoft Windows UEFI Driver Publisher

Figure 10:
* Issuer: Microsoft Corporation Third Party Marketplace Root
* Subject: Microsoft Corporation UEFI CA 2011
 
![](https://i.imgur.com/DAjzyVW.png)
<p align = "center">
  <i>Figure 9: First certificate information from shim</i>
 </p>
 
![](https://i.imgur.com/28huIHx.png)
<p align = "center">
  <i>Figure 10: Second certificate information from shim</i>
 </p>
    
![](https://i.imgur.com/zJxdQTY.png)    
<p align = "center">
  <i>Figure 11: Diagram for certificate relations </i>
 </p>   


## Task 3: GRUB

9. Using your new knowledge about Authenticode binaries. extract the signing certificates from the GRUB boot loader, and show the subject and issuer.
10. Why is storing the certificate X in the shim binary secure?
11. What do you think is the subject CommonName (CN) of this X certificate?
12. Obtain the X certificate used by the shim to verify the GRUB binary.
13. Verify that this X certificat's corresponding private key was indeed used to sign the GRUB binary.


### Answers:

9. I did the same steps for grubx64.efi

```
sudo dd if=grubx64.efi skip=1708040 of=sig_grub.p7b bs=1 count=1912

openssl pkcs7 -print_certs -text -in sig_grub.p7b -out cert_grub.pem -inform der

```

Issuer: Canonical Ltd. Master Certificate Authority
Subject: Canonical Ltd. Secure Boot Signing

![](https://i.imgur.com/wZK3hyz.png)
<p align = "center">
  <i>Figure 12: Pyew finding the address and size for grub</i>
 </p>

![](https://i.imgur.com/ao9KD5h.png)    
<p align = "center">
  <i>Figure 13: Grub certificate</i>
 </p>     


10. I didn't find an exact answer for this question from the Internet but, I think that keeping the signature inside the shim is more secure than other ways of storing the signature. f.e storing it inside DB. Shim itself is signed and first is verified if it is corrupted or not. So, in this way, if an attacker wants to corrupt this certificate, the shim won't start in the first step cause it cannot be verified.

11. I think it would be "Canonical Ltd. Master Certificate Authority" cause the certificate inside the grub had the CN of "Canonical Ltd. Master Certificate Authority" for its Issuer and we need another Issuer signed the "Canonical Ltd. Master Certificate Authority" as its subject.

12. I used binwalk as a tool to find this certificate. Because of the length hint, I understood which one can be the certificate we are asking for. I used the address and with the dd command, I got it.

```
sudo dd if=shimx64.efi skip=689168 of canonical.der bs=1 count=1080

openssl x509 -inform der -in canonical.der  -text  -out shim-canonical-text.txt
```
Issuer: Canonical Ltd. Master Certificate Authority
Subject: Canonical Ltd. Master Certificate Authority

    
![](https://i.imgur.com/B5IwEW2.png)
<p align = "center">
  <i>Figure 14: binwalk running on shimx64.efi</i>
 </p>
    
![](https://i.imgur.com/iaNsjUA.png)    
<p align = "center">
  <i>Figure 15: Hidden cerificate inside shim </i>
 </p> 

13. It can be verified like this:

![](https://i.imgur.com/CN8A13c.png)
<p align = "center">
  <i>Figure 15: Verify grub with canonical certificate</i>
 </p>

## Task 4: The kernel

14. Verify the kernel you booted against the X certificate 
15. BONUS: Where does GRUB gets its trusted certificate from?
    BONUS: We have now verified Steps 1-3 of the Ubuntu chain of trust. Verifying Step 4 is left as a bonus
16. Draw a diagram that shows the chain of trust from the EFI PK key to the signed kernel. Show all certificates, binaries and signing relations involved. 


### Answer:
14. This Canonical named certificate is used to sign both GRUB and Kernel. We can verify it like below:
    
![](https://i.imgur.com/LollrRK.png)
<p align = "center">
  <i>Figure 16: Verify Kernel</i>
 </p>

15. I am not sure, may be it is using the certificate inside the DB cause it DB has the copy of this cert.


16. I wasn't able to find the whole relations but this picture is the result of this stage of knowledge I have.


![](https://i.imgur.com/AljHTLM.png)
<p align = "center">
  <i>Figure 17: Trust chain</i>
 </p>


**16. "Signs" relation is no correctly shown, intermediates are shown as siblings, f.e. GRUB is directly signed by "Canonical Ltd. Secure Boot Signing" privite key, and "Canonical Ltd. Secure Boot Signing" cert is signed by "Canonical Ltd. Certificate Authority" private key**

---
## References:

1. [What Is Secure Boot](https://www.trentonsystems.com/blog/what-is-secure-boot)
2. [Knowing Secure Boot Key Types](https://www.rodsbooks.com/efi-bootloaders/controlling-sb.html)
3. [UEFI Secure boot — Taking Control](https://medium.com/@ruchirkhatri/uefi-secure-boot-taking-control-b7a7da4c422b)
4. [Testing Secure Boot](https://wiki.ubuntu.com/UEFI/SecureBoot/Testing)
5. [How to sign your own UEFI binaries for Secure Boot](https://wiki.ubuntu.com/UEFI/SecureBoot/Signing)
6. [How to convert things](https://cheapsslsecurity.com/p/convert-a-certificate-to-pem-crt-to-pem-cer-to-pem-der-to-pem/)
7. [Windows Authenticode Portable Executable Signature Format](https://www.symbolcrash.com/wp-content/uploads/2019/02/Authenticode_PE-1.pdf)
8. [Extracting Digital Signatures from Signed Malware with pf](https://radareorg.github.io/blog/posts/extracting-digital-signatures-from-signed-malware/)
9. [How Shim verifies binaries in secure boot?](https://askubuntu.com/questions/951040/how-shim-verifies-binaries-in-secure-boot)
10. [SecureBoot in Ubuntu (Image signing)](https://wiki.ubuntu.com/UEFI/SecureBoot/KeyManagement/ImageSigning)
11. [How do I view the details of a digital certificate .cer file?](https://serverfault.com/questions/215606/how-do-i-view-the-details-of-a-digital-certificate-cer-file)
12. 
