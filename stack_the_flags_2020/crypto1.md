# Crypto 1 (Can COViD steal Bob's idea?)

`Bob wants Alice to help him design the stream cipher's keystream generator base on his rough idea. Can COViD steal Bob's "protected" idea?`

1. For this challenge, we were given a pcapng file to examine. This file is essentially a packet capture format. We can open up this file with WireShark to examine the contents. 

2. Upon analysis, we can see that there exists a TCP (transmission control protocol) between two IP addresses, 192.168.1.57 and 192.168.1.251.

3. By following the TCP stream in wireshark, we can find the following information:

    ```txt
    p = 298161833288328455288826827978944092433
    g = 216590906870332474191827756801961881648
    g^a = 181553548982634226931709548695881171814
    g^b = 64889049934231151703132324484506000958
    ```

    Hi Alice, could you please help me to design a keystream generator according to the file I share in the file server so that I can use it to encrypt my 500-bytes secret message? Please make sure it run with maximum period without repeating the keystream. The password to protect the file is our shared Diffie-Hellman key in digits. Thanks.

4. From this, we know that the flag is likely to be the secret Diffie-Hellman key that is shared between Bob and Alice.

5. Essentially what we need to find is the result of g^ab mod p, where we already have g^a, g^b, g and p.

6. To solve this, I looked at either finding the value of a or b, but that proves to be difficult complexity wise as g^a and g^b are modded and we are faced with the discrete logarithm problem.

7. I found this cool tool online that can easily solve the discrete logarithm problem with great efficiency (<https://www.alpertron.com.ar/DILOG.HTM>) which allowed me to compute the values of a and b.

    ```txt
    a =  211 631375 588570 729261 040810 141700 746731
    b =  164 628728 413072 046550 711086 534226 032119
    ```

8. Then we just have to find the value of g^ab mod p which is equivalent to taking (g^a mod p)^b mod p or (g^b mod p)^a mod p.

9. Therefore the flag is:
 `govtech-csg{246544130863363089867058587807471986686}`

As for solving the discrete logarithm problem without the given calculator, there are many algorithms out there that I have yet to explore, might try understanding them one day lol.
