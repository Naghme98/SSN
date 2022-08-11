# Introduction:
In this assignment, we will look at classical methods of encoding/decoding.
You can download the [WinXP virtual machine with the Codebook.](https://disk.yandex.ru/d/81Yj3EbMy71KhA)

## Task 1:

Open the Codebook and look at everything up to and including Vigenere ciphers: choose “Main Contents” and go through the first three chapters of the “Birth of cryptography” up to and including “Mechanising secrecy”.

### Answer:

In this Codebook, there was information about different ciphers during the times.
The list of the ciphers explained there is as the following.

1. Transposition ciphers:
    * The Railfence: It would break the plaintext into different lines and then change the style of writing of the text to create the ciphertext
    * Latin Square: It uses a highly symmetric square and changes the positions and creates a T shape with them.
    * Scytale: It uses a wooden staff or scytale and letter. Writing on the stripped leather around the scytale.

2. Substitution ciphers:
    * Ceasar: change every letter with (x) places' letter before or after the current one.
    * Kama-Sutra: This one will group the letters into 13 groups, and when we see a letter, we will substitute it with the other one in the same group
    * Pigpen: square and dot shapes :)
    * Atbash: Substitute every letter with the same offset as it is from the other side (for example A -> Z) 
    * Affine: Create equivalent number (from 0 to 25) for alphabets and then multiply the number with x and add y to it and do the modular arithmetic on it (%26) and then see the result is the same as which letter in the alphabet.
    * General monoalphabetic: create a random ciphertext alphabet and when we see a letter, substitute it with its equivalent inside the ciphertext alphabet.
3. Vigenere cipher:
    * It will use a square. Each line will be the alphabet shifted one letter to the left indifference with the above line. Also, there is a need for a keyword that is repeated until the end of our plaintext. For creating the ciphertext, we will check the row corresponding to the equivalent letter of the key for the position and the column of our plain text, and the conjunction place will be our ciphertext letter.
4. Other ciphers are as follow:
    * Digraph substitution
    * Playfair cipher
    * Homophonic ciphers
    * Book ciphers



## Task 2:

Encrypt an English text of at least 80 words using the Vigenere cipher and exchange it with one of your fellow students.


### Answer:

I used the codebook Vigenere Tool to encrypt my text.

My text is as follow:


> computer security also called cybersecurity the protection of computer systems and information from harm theft and unauthorized use. Computer hardware is typically protected by the same means used to protect other valuable or sensitive equipment namely serial numbers doors and locks and alarms. The protection of information and system access on the other hand is achieved through other tactics some of them quite complex. Computer security has become increasingly important since the late when modems devices that allow computers to communicate over telephone lines were introduced


![](https://i.imgur.com/Ul9GlMD.png)
<p align = "center">
  <i>Figure 1: Encryption using Vigenere Tool<\i>
</p>

The encrypted text:

> EWBWYKGZHLGLTQIFECUWRHPCGLRFFVTATJYIKBNALVRZDAITVQDUSWEWBWYKGZHFWKGUHHRUKVUVVDCBXVRWTWBOEIOBWLJKCVSBRRWBWVVZBMSBWVEWBWYKGZWHVUYIGLMJVGEPGRNTNWVFVMRAIUDGIOIJCUTTIRPAJZIUVWEYSKGKIVXYGZKHPLCJALSIUMCZMKKDTLULKXBLRKPIBLPPUMGPECPCBIIIULDVVJCVSSSTMAPUHRNIGTWKJMEYSKGKIPSEQNXUJFTUPAMFPICKWPUBTTETEMHZSEVPTVXYGZWHRUKAPJLZGDTKXYTWJNLFVPTYXREBXJWJQUTVJKJMBXYZVMRVQGNMMJSDRCILVJGKJYMKAPPZFVEWBLMEEZTHWZPOAFMDRWGAEEVAXUGVVPTSEKGEWLRDQLTTWUGDXJIJVPPAECNWLJSDRCILVJVWRVQDWVXJEKGWKLVKGTTWLFPMAPRVUETYIZPBGVHLEMS

Then I sent the encrypted text to Ifeany.

## Task 3:

Crack the encrypted text of your fellow student using the Vigenere cipher tool.


### Answer:

I received my teammate's encrypted text. After that, I used the Codebook Vigenere cracking tool. It would calculate the frequency of repeated 3 or more letters together inside the ciphertext and write the distance to the other repeated letters (Figure 2) After that, we can see the most common length probable key for our ciphertext. Here in the picture, the most common one is a key length of 3. So, I will proceed using the 3 letter key.
The other step is to calculate the frequency of letters for each position cause the same key is used repeatedly we can observe for positions that are using the same key letter.
The next step is observing the frequency and comparing it to the English alphabet frequencies and trying to create some meaningful text out of the ciphertext. So, I played a lot with the positions and come to notice that it would be "RUS". As you can see, the result cracked text is not meaning full (Figure 4).
Hence, I change the key length to the second most frequent key length that is 6. Because the first three letters represent "The" and it makes sense to have it at the start of the sentence, I used the "RUS" as the first three letters of the 6 length key. (Actually at this moment I guess it should be something about Russia)
After that, I checked the frequencies and tried to create somehow the same as the usual English letters' frequencies and you can see the result in figure 5.
Finally, in figure 6, you can see the whole cracked text with the key "RUSSIA"


p.s: I found a nice website that by specifying the key length, it will produce different probable combinations for the key (Figure 7).

![](https://i.imgur.com/NgX1VTe.png)
<p align = "center">
  <i>Figure 3: Vigenere repeated distances<\i>
</p>

![](https://i.imgur.com/zXiR14k.png)
<p align = "center">
  <i>Figure 4: Vigenere cracking tool for key length 3.<\i>
</p>

![](https://i.imgur.com/ooeCBop.png)
<p align = "center">
  <i>Figure 5: Vigenere cracking tool for key length 6 and the 4'th letter of the key.<\i>
</p>

![](https://i.imgur.com/dOtwXOY.png)
<p align = "center">
  <i>Figure 6: Cracked cipher text<\i>
</p>

![](https://i.imgur.com/D6vRDur.jpg)
<p align = "center">
  <i>Figure 7: Using the site to crack the cipher text.<\i>
</p>

## Task 4:
Go through the previous two steps again, this time using a cipher of your own choosing. Do not tell your fellow student what cipher you used!


### Answer:

For this task, I decide to use Afferi cipher with the following configurations and text:

> The Affine cipher is a type of monoalphabetic substitution cipher wherein each letter in an alphabet is mapped to its numeric equivalent encrypted using a simple mathematical function and converted back to a letter The formula used means that each letter encrypts to one other letter and back again meaning the cipher is essentially a standard substitution cipher with a rule governing which letter goes to which The whole process relies on working modulo m the length of the alphabet used

The result ciphertext:
> NFQ WVVKJQ GKTFQD KI W NMTQ OV EOJOWZTFWBQNKG ISBINKNSNKOJ GKTFQD CFQDQKJ QWGF ZQNNQD KJ WJ WZTFWBQN KI EWTTQL NO KNI JSEQDKG QYSKXWZQJN QJGDMTNQL SIKJA W IKETZQ EWNFQEWNKGWZ VSJGNKOJ WJL GOJXQDNQL BWGU NO W ZQNNQD NFQ VODESZW SIQL EQWJI NFWN QWGF ZQNNQD QJGDMTNI NO OJQ ONFQD ZQNNQD WJL BWGU WAWKJ EQWJKJA NFQ GKTFQD KI QIIQJNKWZZM W INWJLWDL ISBINKNSNKOJ GKTFQD CKNF W DSZQ AOXQDJKJA CFKGF ZQNNQD AOQI NO CFKGF NFQ CFOZQ TDOGQII DQZKQI OJ CODUKJA EOLSZO E NFQ ZQJANF OV NFQ WZTFWBQN SIQL

![](https://i.imgur.com/4pq55EE.png)
<p align = "center">
  <i>Figure 8: Affine cipher<\i>
</p>

After that, I received a ciphertext from my teammate and I tried to decrypte it.

The given ciphertext:

> XLMW GMTLIV XIBX LEW FIIR IRGVCTXIH ERH WIRX XS REKLQIL  XS QEOI MX IEWMIV JSV LIV  M LEZI EHHIH WSQI IBXVE ASVHW XS MX  JVSQ ALEX M PIEVR JVSQ TVIZMSYW XSTMGW SJ GVCTXSKVETLC  XLI PSRKIV XWLI WIRXIRGI  XLI LMKLIV XLI TVSFEFMPMXC SJ HIGMTLIVMRK MI GVEGOMRK  WS QEOMRK WYGL GSRWMHIVEXMSRW KMZI W LIV E FIXXIV GLERGI SJ ORSAMRK XLI QIWWEKI  AIPGSQI XS VYWWME 

First of all, I used the Frequency Analysis tool of the Codebook to see how is the frequency of the letters in this ciphertext.
The result was like the following:

![](https://i.imgur.com/hOnuwnt.png)
<p align = "center">
  <i>Figure 9: Frequency analysis result<\i>
</p>


So, I noticed that the frequency of "I" was the highest one and the second highest one was "X". The difference between the position of "I" and "E" (the most frequent letter in the English alphabet) was the same as "X" and "T" (The second frequent one) and equal to 4. Hence, it made me think about Ceasar. Another thing that I noticed, was two-letter words like XS, MX exists, and Also there is one letter "M" this should be either "A" or "I". 

The result plain text came out of the given ciphertext.

> this cipher text has been encrypted and sent to naghmeh  to make it easier for her  i have added some extra words to it  from what i learn from previous topics of cryptography  the longer tshe sentence  the higher the probability of deciphering ie cracking  so making such considerations give s her a better chance of knowing the message  welcome to russia 

![](https://i.imgur.com/bBKqttc.png)
<p align = "center">
  <i>Figure 10: Ceasar Decription<\i>
</p>

---
## References:

1. [Online decoder site](https://www.dcode.fr/vigenere-cipher)
2. [Affine Cipher](https://www.geeksforgeeks.org/implementation-affine-cipher/)
3. [Online Vigenere calculator](https://cryptii.com/pipes/vigenere-cipher)
4. [Cryptography - Breaking the Vigenere Cipher](https://www.youtube.com/watch?v=P4z3jAOzT9I)

