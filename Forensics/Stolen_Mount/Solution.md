# Stolen Mount

## Challenge Description
> ![image](https://github.com/user-attachments/assets/5dd0933c-964b-48d6-83f0-654519740881)

## Solution
Since analyzing files locally is more convenient, I transferred the PCAP file from the TryHackMe machine to my local system.

On the TryHackMe machine:
```
cd ~/Desktop
python3 -m http.server 8000
```

![image](https://github.com/user-attachments/assets/b711b3e9-d366-4fe3-87ca-b405431a6985)

On my  local machine:
```
wget http://<TryHackMe_IP>:8000/challenge.pcapng
```

![image](https://github.com/user-attachments/assets/a0a09895-c9c2-4f64-856b-1a63aaedf290)

Using Wireshark, I opened challenge.pcapng and immediately noticed a large amount of NFS traffic.

![image](https://github.com/user-attachments/assets/3ff8e33b-3d90-425d-9c6c-07baefff4bca)

NFS is a distributed file system protocol that allows a client to access files over a network as if they were on the local machine. It commonly runs over TCP port 2049.

Attackers often exploit NFS vulnerabilities to:
- Mount unauthorized NFS shares
- Steal sensitive data
- Launch denial-of-service (DoS) attacks

By following TCP streams in Wireshark, I found several key pieces of information:
- A hidden_stash.zip file
- A secrets.png file
- A hashed string: 90eb7723a657b6597100aafef171d9f2 (MD5 hash)

I used [CrackStation](https://crackstation.net/) to crack the MD5 hash.

![image](https://github.com/user-attachments/assets/a7795d71-377b-4c8c-b360-aaf99b6b0e6a)

The password was revealed as: **avengers**

Based on research, I found a useful method to extract raw ASCII data from the TCP stream and save it as a file via this [writeup](https://ctftime.org/writeup/33169). Using this approach, I saved the extracted data as hidden_stash.zip.

I then attempted to unzip it:
```
unzip hidden_stash.zip
```
When prompted for a password, I entered avengers, which successfully unlocked the file.

Inside the archive, I found secrets.png, which turned out to be a QR code.

![image](https://github.com/user-attachments/assets/f6194219-46b0-4f19-b15d-37a327c4b3d1)

Using an online [QR code Scanner](https://scanqr.org/#scan), I scanned the extracted image and retrieved the flag.
```
THM{n0t_s3cur3_f1l3_sh4r1ng}
```

![image](https://github.com/user-attachments/assets/34073719-503b-40bc-9bf8-713273e84f47)

## Conclusion
This challenge demonstrated the risks associated with insecure NFS configurations. Attackers can exploit misconfigured NFS shares to gain unauthorized access to sensitive files.

## Flag: 
THM{n0t_s3cur3_f1l3_sh4r1ng}


   
