---
title: Metactf 2021 - Crypto 
description: Beginner Level Challenges from MetaCTF #5
author: cyberpj
date: 2021-12-19
categories: ctftime
tags: [cryptography]
math: true
mermaid: true
---

## CTF Name : MetaCTF 2021 (Dec 5)
![image](https://user-images.githubusercontent.com/72292872/148796808-c2147a50-ef52-46e1-9794-a04eebe3b8a0.png)

### Category : Cryptography #5
----
> This Writeup involves Easy level challenges (Beginner Friendly)
>Pure Beginner Friendly Challenges

## 1. Size Matters 
![image](https://user-images.githubusercontent.com/72292872/146650731-7c408104-53eb-4727-93dd-d46bc6105255.png)

 ```
 Ciphertext: 0x2526512a4abf23fca755defc497b9ab
e: 257
n: 0x592f144c0aeac50bdf57cf6a6a6e135
```
 - RSA Decoder in dcode.fr
 - ![image](https://user-images.githubusercontent.com/72292872/146650963-81c9486f-54f8-47fa-9139-e79279174077.png)
### Flag : MetaCTF{you_broke_rsa!}
-----

## 2. A to Z
![image](https://user-images.githubusercontent.com/72292872/146651012-1825e84d-47c2-4b79-a629-9689a9b64e7b.png)
- Just an Alphabetical substitution 
![image](https://user-images.githubusercontent.com/72292872/146651051-e1a5c792-5156-4325-80db-38bb04e46b5d.png)
## Flag : bashful_is_my_fav_dwarf

-----
## 3. Wrong Way on a One Way Street

![image](https://user-images.githubusercontent.com/72292872/146651106-b35be025-a24e-4b7e-87f2-d98f25572662.png)
`cb78e77e659c1648416cf5ac43fca4b65eeaefe1`

- crackstation.net
![image](https://user-images.githubusercontent.com/72292872/146651161-fee04a21-fc7b-4698-8c79-a937bd0329ab.png)
## Flag : babyloka13
-----
## 4. Unbreable Encryption

![image](https://user-images.githubusercontent.com/72292872/146651501-1db7070d-6412-4e86-b88b-47877ab78a6d.png)

 ```
 Ciphertext ( c1): 4fd098298db95b7f1bc205b0a6d8ac15f1f821d72fbfa979d1c2148a24feaafdee8d3108e8ce29c3ce1291

Plaintext (p1): hey let's rob the bank at midnight tonight!

Ciphertext (c2) : 41d9806ec1b55c78258703be87ac9e06edb7369133b1d67ac0960d8632cfb7f2e7974e0ff3c536c1871b
key = 
```
```
so 
key = c1 xor p
p2 = c2 xor key
p2=MetaCTF{you're_better_than_steve!}
```
Thats all 
![image](https://user-images.githubusercontent.com/72292872/146651484-4bbf6a93-e4be-4b91-8b43-a74f7d530ec9.png)

----

## 5. Tq For The pwd 
![image](https://user-images.githubusercontent.com/72292872/146651683-b1caab66-e230-4892-a14b-3e3441ed8f26.png)
Just a Base64 

```echo TWV0YUNURntlbmNvZGluZ19pc19OMFRfdGhlX3NhbWVfYXNfZW5jcnlwdGlvbiEhfQ==|base64 -d ```
                                                                                                              
## Flag : MetaCTF{encoding_is_N0T_the_same_as_encryption!!}  

------
Thanks For reading - Team `TamilCTF`

------
 
