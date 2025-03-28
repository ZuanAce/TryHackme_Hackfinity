# Dark Encryptor

## Challenge Description
> ![image](https://github.com/user-attachments/assets/2c4385ce-bddd-498c-a168-b7566fe8746d)

## Solution
Upon accessing the website, a simple encryption tool was presented. The interface allowed users to enter text and select a recipient for encryption.

![image](https://github.com/user-attachments/assets/735b3f4b-d80a-4132-a8ac-b3aa74887365)

To investigate its functionality, I tested it with various inputs and examined the outputs to understand how the application processes data.

While experimenting with different inputs, I noticed a peculiar behavior:

- Encrypting regular text (e.g., "test") worked as expected.
- Encrypting special characters resulted in different responses:
  - ` caused an encryption failure.
  - **!** was encrypted successfully.
  - **#** returned an unexpected error: `Usage: python3 enc.py -r <recipient> -t <text>`

This error suggested that user input might be directly passed to a command-line function, hinting at a command injection vulnerability. Refer to https://github.com/payloadbox/command-injection-payload-list for the payloads.

To confirm the vulnerability, I attempted executing system commands using `;id`:
```
Encrypted Output:
Usage: python3 enc.py -r <recipient> -t <text>
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),117(netdev),118(lxd)
```

![image](https://github.com/user-attachments/assets/3a9b0e97-9e9f-42cf-a443-712af9f20ee2)

The output confirmed that command execution was possible.

Next, I listed the files in the directory using `;ls`:
```
Encrypted Output:
Usage: python3 enc.py -r <recipient> -t <text>
app.py
app.wsgi
enc.py
flag.txt
keys
static
templates
uploads
```

![image](https://github.com/user-attachments/assets/8bfbcd34-e980-4229-9faa-61d61a0cd376)

Using ;cat flag.txt, I successfully retrieved the flag: `THM{pgp_cant_stop_me}`

![image](https://github.com/user-attachments/assets/ee48ecaa-94d0-42f1-8920-01e2b9a2aac4)

## Conclusion

This challenge demonstrated a command injection vulnerability, where user input was unsafely executed in a system command. By leveraging this flaw, I successfully extracted the flag and completed the challenge.

## Flag: 
THM{pgp_cant_stop_me}


   
