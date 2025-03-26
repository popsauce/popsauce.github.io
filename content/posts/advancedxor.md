---
author: "popsauce"
title: "Advanced XOR"
date: ""
description: "This article details a script based solution for a challenge called Advanced XOR."
plotly: false
tags: [
    "",
]
categories: [
    "scripts",
]
series: ["Themes Guide"]
aliases: ["migrate-from-jekyl"]
---

Advanced XOR is a XOR encryption based problem in the form of a python script.
This problem had the notoriety of being the hardest problem in the whole problemset that was given as a test,
as only a few people had been able to solve it at my university.
<!--more-->

<!--{{< plotly json="/plotly/helix_line8.json" height="500px" >}}-->

## Understanding advanced_xor.py

The main script advanced_xor.py contains 2 main functions, one to generate a key and the other which runs the encryption function.



    from os import urandom
    from ctypes import CDLL
    import string
    
    key = ""
    ciphertext = ""
    def get_key():
        global key
        c = urandom(1)
        if len(key)!=5::
            if c not in string.ascii_lowercase and c not in string.ascii_uppercase :
                get_key()
            else:
                key += c
                get_key()
        
    def xor_encrypt(x):
        global key
        global ciphertext
        get_key()
        
        hex_key = key.encode("hex")
        key_list = [hex_key[i]+hex_key[i+1] for i in range(0,len(hex_key),2)]
        print key
        for i in xrange(len(x)):
            ciphertext += chr(ord(x[i]) ^ int(key_list[i%5], 16))
    
        
    if __name__ == "__main__":
        plaintext = raw_input("Enter the plaintext to be encrypted: ")
        xor_encrypt(plaintext)
        print ciphertext.encode("hex")


The script prompts for a string called plaintext, which can have any length.
After getting the plaintext it generates a key and encrypts the plaintext. 
    

### def get_key()

This function generates a key of length 5 which contains both upper case and lower case ascii alphabets.

    def get_key():
        global key
        c = urandom(1)
        if len(key)!=5::
            if c not in string.ascii_lowercase and c not in string.ascii_uppercase :
                get_key()
            else:
                key += c
                get_key()

### def xor_encrypt()

This is the main function that is used to encrypt the plaintext.

    def xor_encrypt(x):
        global key
        global ciphertext
        get_key()
        
        hex_key = key.encode("hex")
        key_list = [hex_key[i]+hex_key[i+1] for i in range(0,len(hex_key),2)]
        print key
        for i in xrange(len(x)):
            ciphertext += chr(ord(x[i]) ^ int(key_list[i%5], 16))

The encryption algorithm first encodes the key into hexadecimal form based on the unicode number corresponding to each character in the key. The range of this encoding lies from a-z ~ 61-7A , A-Z ~ 41-5A.
## Solving advanced_xor.py

So after messing around with the xor_encrypt function i tested a few ideas. The most important one is the that instead of generating the key and testing if it decrypts the whole ciphertext we divide the cipher text and match for that.

    print "Enter the ciphertext you want to decrypt."
    a=raw_input()
    a=a.decode("hex")
    l=len(a)
    loflist=int(l/5)
    extravalue=l-loflist
    
    lofcipone=loflist
    lofciptwo=loflist
    lofcipthree=loflist
    lofcipfour=loflist
    lofcipfive=loflist
    
    if extravalue==1 or extravalue==2 or extravalue==3 or extravalue==4:
        lofcipone=lofcipone+1
    if extravalue==2 or extravalue==3 or extravalue==4:
        lofciptwo=lofciptwo+1
    if extravalue==3 or extravalue==4:
        lofcipthree=lofcipthree+1
    if extravalue==4:
        lofcipfour=lofcipfour+1
    
    cipone=[]
    ciptwo=[]
    cipthree=[]
    cipfour=[]
    cipfive=[]
    
Here we can see that the ciphertext was divided into 5 lists corresponding to the key length which is also 5. For example if ciphertext is  "12345 12345 12345 12345 12345 12" (spaces are there to make it readable). cipone would contain 111111 ciptwo would contain 222222 ie 6 characters and the rest would contain 5 characters.


    i=0
    b=0
    while i<lofcipone:
        cipone.append(ord(a[b]))
        b=b+5
        i=i+1
    i=0
    b=1
    while i<lofciptwo:
        ciptwo.append(ord(a[b]))
        b=b+5
        i=i+1
    i=0
    b=2
    while i<lofcipthree:
        cipthree.append(ord(a[b]))
        b=b+5
        i=i+1
    i=0
    b=3
    while i<lofcipfour:
        cipfour.append(ord(a[b]))
        b=b+5
        i=i+1
    i=0
    b=4
    while i<lofcipfive:
        cipfive.append(ord(a[b]))
        b=b+5
        i=i+1
    
    print " These five lists represent 5 cipherlists :"
    
    print cipone
    print ciptwo
    print cipthree
    print cipfour
    print cipfive
    
    checkpoint=raw_input("Do you want to continue ? ...")
    
    br=0
    for aa in range(65,91)+ range(97,123):
        if br==1:
            break
        loopone=0
        plaone=""
        for i in cipone:
            x=aa^i
            if x>=32 and x<=126:
                plaone+=chr(x)
            else:
                plaone=""
                loopone=1
                break
        if loopone!=1:
            for ab in range(65,91)+range(97,123):
                if br==1:
                    break
                looptwo=0
                platwo=""
                for j in ciptwo:
                    x=ab^j
                    if x>=32 and x<=126:
                        platwo+=chr(x)
                    else:
                        platwo=""
                        looptwo=1
                        break
                if looptwo!=1:
                    for ac in range(65,91)+ range(97,123):
                        if br==1:
                            break
                        loopthree=0
                        plathree=""
                        for k in cipthree:
                            x=ac^k
                            if x>=32 and x<=126:
                                plathree+=chr(x)
                            else:
                                plathree=""
                                loopthree=1
                                break
                        if loopthree!=1:
                            for ad in range(65,91)+range(97,123):
                                if br==1:
                                    break
                                loopfour=0
                                plafour=""
                                for l in cipfour:
                                    x=ad^l
                                    if x>=32 and x<=126:
                                        plafour+=chr(x)
                                    else:
                                        plafour=""
                                        loopfour=1
                                        break
                                if loopfour!=1:
                                    for ae in range(65,91)+range(97,123):
                                        if br==1:
                                            break
                                        loopfive=0
                                        plafive=""
                                        for m in cipfive:
                                            x=ae^m
                                            if x>=32 and x<=126:
                                                plafive+=chr(x)
                                            else:
                                                plafive=""
                                                loopfive=1
                                                break
                                        if loopfive!=1:
                                            plaintext=""
                                            aaa=chr(aa)
                                            aab=chr(ab)
                                            aac=chr(ac)
                                            aad=chr(ad)
                                            aae=chr(ae)
                                            value=aaa+aab+aac+aad+aae
                                            print value
                                            for a in range(0,lofcipfive):
                                                plaintext+=plaone[a]+platwo[a]+plathree[a]+plafour[a]+plafive[a]
                                            
                                            if lofcipone>lofcipfive:
                                                plaintext=plaintext+plaone[lofcipfive]
                                            if lofciptwo>lofcipfive:
                                                plaintext=plaintext+platwo[lofcipfive]
                                            if lofcipthree>lofcipfive:
                                                plaintext=plaintext+plathree[lofcipfive]
                                            if lofcipfour>lofcipfive:
                                                plaintext=plaintext+plafour[lofcipfive]
                                            
                                            flag=plaintext
                                            print ""
                                            print flag
                                            print ""
                                            if checkervalue=="":
                                                if hashlib.md5(flag).digest().encode("hex") == "067abbb88d2201a393ba660728f83b84":
                                                    print "!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"
                                                    print "Yeah!....You are a genius!"
                                                    print ""
                                                    ans=value
                                                    fin=flag
                                                    br=1
                                            else:
                                                if flag==checkervalue:
                                                    print "!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"
                                                    print "Yeah!....You are a genius!"
                                                    print ""
                                                    ans=value
                                                    fin=flag
                                                    br=1
    print ans
    print ""
    print fin

## The result

🥁 and its done !! 

As it can be seen the ciphertext was "You completed Advanced XOR" and the key was "snMdh".

![alt text](/advanced_xor_1.png)

As it can be seen the final result shows the time it takes to decrypt the original ciphertext. The 3 terms real, user, sys calculate time elapsed differently. Real represents wallclock time to execute the function from start to finish, user + sys shows the actual CPU time used by this process ie ~11m30s, Interestingly since the process was running on more than one processor it exceeds the real time shown.
