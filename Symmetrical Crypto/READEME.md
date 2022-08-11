
## Task 1: DES 

1. Use the DES simulator(launch.jarfile). Step through the process of encrypting your name with the key 0x0101010101010101 and write the internal state of the device at the 8th round.
2. Inspect the key schedule phase for the given key and explain how the subkeys are generated for each of the 16 steps.
3. Comment on the behavior of DES when using the given key.


### Implementation:

1. I used the given file with the Input Data as "Naghmeh!" that its Hex would be "0x4E6167686D656821" and the given key "0x0101010101010101" the result is shown in figure 1.

In round 8 we will have the following information from different steps of running the algorithm:
(I wrote the code to show the different parts one by one. The code is available in the folder)
* Result of E-bit selection table: 0x9A28528A3DFA
* Result of xor the selection with round 8'th round key: 0x9A28528A3DFA
* Result of s-box: 0x8E1223F3
* Result of permutation of the s-box output: 0x46FA2C91
* Left half data value: 0x310911BD
* Right half data valeu: E9087546
* 8'th round key: 0x0

    
![](https://i.imgur.com/66moItY.png)
<p align = "center">
  <i>Figure 1: DES block cipher calculator</i>
 </p>


2. Cause the key's pattern is 01 and in binary it will become like "00000001", in the first permutation it will become all zeros. 
In the algorithm we have the following steps for key generation in each round:
    * key -> pc-1 = C and D (each as 28 bits):  With the following permutation arrays (Figure 2) and as you can see in these tables we do not have 8,16,.. positions which are ones in our key example so, the ones will have no use and create all zero result.
    * Left circular shift: Each round by looking at figure 3's table will find the number of shifts that should perform. For the all-zero key, the shift will have no results.
    * PC_2: Permutation with figure 3's array. Again, for the all-zero key, there is no change.
    

![](https://i.imgur.com/ljZbNDN.png)
<p align = "center">
  <i>Figure 2: C and D permutation tables</i>
 </p>
    
![](https://i.imgur.com/Rx00pgo.png)    
<p align = "center">
  <i>Figure 3: PC_2 permutation array</i>
 </p>    

    
3. When we Xor Y with zero, the result will be the Y itself. That means, in this example, the key will have no effect and we are all permuting the input text itself.


## Task 2: AES

4. Identify the Shannon diffusion element(s).
5. Also identify the Shannon confusion element(s).



### Implementation:

First I should define what is diffusion and confusion.

* Diffusion: The modification of one bit of the plaintext should affect approximately half of the plaintext bits (Provided by permutation box)
* Confusion: Each bit of the ciphertext should depend on the key in a way that the relationship between the ciphertext and the key stays hidden (Provided by substitution box).

AES algorithm has 4 steps in each round (except the last round that doesn't have Mix columns)

* Add round key: XORs a round key to the state.
* Substitute bytes: a substitution (or mapping) based on the AES S-box.
* Shift rows: Applies some rotations on the state.
* Mix columns: Divides the state into multiple words and mix them.

Based on the information, I think:

* Add round key: This is CONFUSION cause this is the step that mixes the key in our cipher and makes it dependent on the key. 
* Substitute bytes: This is a CONFUSION cause this round will hide the relationship between the ciphertext and key.
* Shift rows and Mix columns: Are DIFFUSION cause they will change the positions, but they are also necessary to obtain overall confusion. Indeed, if you wouldn't mix columns (or rows), then the equations describing the AES would be easy to solve.



## Task 3: RC4

Follow these instructions, identify the URL and your personal archive accordingly,download it and inspect its contents. There are two files encrypted with the RC4 cipher.
One of the files was encrypted using a 40 bit key that when represented in ASCII starts with
the character a and contains only lowercase letters while the other uses a 48 bit key that can
be written only with digits. Identify the encrypted files and using the brute force tool from
this gist find the keys and decrypt the file. (Most likely you will have to install the pycrypto and numpy libraries first.)

6. * a. How did you identify the encrypted files?
    * b. What is the effective key strength for each of the keys?
    *  c. Instrument the code to find out how many decryption attempts you can perform in one second. Where is the most time spent ?
    *  d. Modify the code to support parallel execution and calculate the speedup.
    *  e. If the same message would be encrypted with a key of length 48 bits but which uses all the printable characters, how much time would it take to explore the full key space ?



### Implementation:

6. * a. For this one, I tried the command "file" for all the files inside the folder and one of them gave me this output:

![](https://i.imgur.com/X3NodHn.png)
<p align = "center">
  <i>Figure 4: file command's output</i>
 </p> 


So, I tried the code for the file "09167342.in" and I got the below output for it. And that shows I found the first encrypted file with a 40-bit key length.


![](https://i.imgur.com/CXApj1y.png)    
<p align = "center">
  <i>Figure 5: Brutforce the first file</i>
 </p>

I couldn't distinguish the other encrypted file. I even checked the assembly file for all of them but I couldn't find a pattern.
So, I tried brute force for each of the remaining files for the 48-bit key and the following result came out.

![](https://i.imgur.com/NouH4wy.png)
<p align = "center">
  <i>Figure 6: Brutforce the second file</i>
 </p>


* b. I didn't get what this question mean but:
For 46 bit key as it represents 6 digits and for each one we have 10 possibilities (0..9) breaking the key (creating all the possible strings) would need 10^6 operations.
And,
For the 40 bit key that is 5 chars, and each char would have 26 possibilities (a-z), we would need 26^5 = 11,881,376 operations. That is 11 times more than the other state.
Also, we know that computers can perform 10^6 operations in a second. But, for breaking a key we need to examine the created key to see if it is the correct one or not. So, each calculated time would increase.

* c. I used the timer object to see how many decryptions were performed in a second. And inside the def check_num_dec, I printed the NUM_DEC.
And as you can see in figure 9, there were about 2000 attempts for decryption. I think the most time-consuming part is the decryption process cause I commented all that lines for decryption and in figure 10 you can see that in a second we have 10^6 operations.

![](https://i.imgur.com/6ClAMNF.png)
<p align = "center">
  <i>Figure 7: Using timer class</i>
 </p>
    
![](https://i.imgur.com/56Kk4p7.png)
<p align = "center">
  <i>Figure 8: check_num_dec function</i>
 </p>
    
![](https://i.imgur.com/t4iggPH.png)
<p align = "center">
  <i>Figure 9: Number of attemptions for decryption RC4 in a second.</i>
 </p>
    
![](https://i.imgur.com/6UhgwW4.png)
<p align = "center">
  <i>Figure 10: Number of attemptions without decryption process</i>
 </p>
    
* d. In the code, there was a function "Parallel" that would create a pool for several threads and run the program in different threads. So, I enabled using this function instead of calling "worker(tuple())" and modified the CPU_COUNT to 5 and 10, and compared the time with the state of not using parallel programming. 
For 5 threads find the key would take about 50s and for 10 threads about 30 seconds and without thread about 150s or 2:30 minutes.

* e. Cause we have 95 printable ASCII characters and 48 bits mean 6 characters, we need to create 95^6 strings and check them. 

    95^6 = 735,091,890,625 and each 10^6 operation can be done in a second. So, 735091 seconds or 12251 minutes or 204 hours or 8 days to create all possible keys.



## Task 4: AES-2

Modify the code to support AES brute-force in CBC mode. How many keys can you test per second? Julian Assange has released an insurance file encrypted with AES256. Assuming that no disruptive technological breakthrough will take place in the future and the performance of CPUs will double every 18 months, when will it be possible to brute-force the file in reasonable time, i.e. less than 1year, using a single computer?



### Implementation:

To modify the code, we need to change the decryption algorithm to the code below:

```
decr = (AES.new(key, AES.MODE_CBC, data[:AES.block_size])).decrypt(data[AES.block_size:])

#instead of: 

decr = ARC4.new(key).decrypt(data)
```

After that, I checked the and the number of key testing was about 39083 for a text I encrypt with online tools with key 'aaaaaaaabbbbbbbb'. Cause I suspect that the number should not be more than RC4 decryption time, I searched the Internet and found the figure 12 diagram that says AES is slower than RC4. So, I ran the AES with the same file I used for RC4 to break and I got the figure 13 output that is more logical.

![](https://i.imgur.com/qHUIDSN.png)
<p align = "center">
  <i>Figure 11: Number of attemptions for decryption AES in a second</i>
 </p>
    
![](https://i.imgur.com/RU4IvhY.png)
<p align = "center">
  <i>Figure 12: AES vs RC4</i>
 </p>  
    
![](https://i.imgur.com/GNiOx9v.png)
<p align = "center">
  <i>Figure 13: Number of attemptions for decryption AES in a second with a same input file as RC4 </i>
 </p>    

“insurance.aes256” is a file with size of 1.4GB. If we want to crack a 256-bit password with no knowledge of anything, trying to just enter every conceivable combination of 0s and 1s, you’d have a “time” of 2^256. Nobody measures the time it would take to crack one of these codes in hours, months, years, or centuries–it’s too big for all of that. 
So, most likely it would be impossible to crack within realistic time frames with the current computer's performance.

We know that todays computer's are equal to 10^6 operations in a second. 

I assumed that by doubling the performance, number of operarions can be done per second would get doubled.

![](https://i.imgur.com/wgUzV6W.jpg)
<p align = "center">
  <i>Figure 14: Calculation for AES breaking.</i>
 </p>
