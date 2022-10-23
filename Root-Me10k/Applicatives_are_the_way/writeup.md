# CTF Root-Me 10k - Oct, 21 to Oct, 23 2022

## Applicatives are the way
```
Hey, someone told me you were called the network-king, can you find the precious flag I've hidden in this capture ?
```



For this challenge your were provided with a pcapng file filled with a lot of differents protocols (TCP, NBSS, DNS, FTP, ICMP, ...).

To solve it, I used Wireshark.

The first thing that caught my eyes is a FTP session, right clicked on it, Follow->TCP Stream: 
![image](https://user-images.githubusercontent.com/116515149/197409591-e24b317b-8fb5-4405-bbdf-c53b95a4bea3.png)

`STOR antho.txt` means that a file has been sent to the FTP.

Looking around for ftp-data, I found this: 
![image](https://user-images.githubusercontent.com/116515149/197409568-c27d58bc-9dd5-40c0-82d8-d7c90cda2faf.png)

To export the data, I did Follow->TCP Stream, then I selected Raw and copy/pasted the data in a file. 


I didn't spend much time on the file, but it looked like TLS secrets. So I went back in the beginning and scrolled past the FTP to find... TLS.
![image](https://user-images.githubusercontent.com/116515149/197409536-14dfe764-67a5-4618-8a47-93d11a74b6c7.png)

As we have the previous file containing TLS secrets, let's import them on Wireshark, Edit->Preferences->Protocols->TLS: 

![image](https://user-images.githubusercontent.com/116515149/197409727-841d64da-ed4a-42a7-8c64-a4365384c758.png)

Looking at it, there was a very suspicious file being requested: 
![image](https://user-images.githubusercontent.com/116515149/197409833-87c3b943-3c27-4880-a2bd-770e1f42aee7.png)

To get it, I searched the DATA Packet and expanded it, clicked on the actual data part: 
![image](https://user-images.githubusercontent.com/116515149/197409910-a9c46c9b-dafb-4285-8b14-6a8b424f87bf.png)

Then right-click, copy->Value (data in hex):
![image](https://user-images.githubusercontent.com/116515149/197409998-395f1105-a3fa-49f7-90f4-8eca82b5b0a4.png)

The zip archive had a password. I tried bruteforcing it but it didn't work.

So back to the capture, the next thing that caught my eyes was DNS queries mixed with ICMP packet. 

![image](https://user-images.githubusercontent.com/116515149/197410106-0379c3bc-e80c-493f-a479-bd125bab454b.png)

DNS and ICMP can both be used to exfiltrate data from a well protected network because those protocols are often overlooked when looking for suspicious activities, knowing that, I searched for suspicious data.
I saw something looking like base64 in the DNS queries, so I decrypted them all (yes, I should have scripted it):
![image](https://user-images.githubusercontent.com/116515149/197410260-32addcca-6193-4791-a6f2-84285d0652aa.png)

Whereas in ICMP, one way of hiding data is to include data in it, which can be seen here. 

From habits, I tried extracting all the data and combining it. To do so, I did File->Export Packet Dissections->As plain text (layout is important):
![image](https://user-images.githubusercontent.com/116515149/197410513-7f7cb393-6725-41b7-bc4f-4383a55c1f00.png)


Then filtered the data:
![image](https://user-images.githubusercontent.com/116515149/197410573-ee66d4b9-4f86-492a-adad-a4b6acd363a8.png)

As said, I tried combining it for far too much time, when I got the idea to tried each one of them as a password for the zip. 

![image](https://user-images.githubusercontent.com/116515149/197410646-f79bb3ed-0a03-485b-8393-4b27c2b46ff9.png)

And it did the trick.

`RM{W1r3Sh4rk_R3@lly_Is_Y0uR_Th1ng:)}`

A very interesting challenge, cheers to the creator.

