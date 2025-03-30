# Sneaky Patch

## Challenge Description
> ![image](https://github.com/user-attachments/assets/f3ad712f-b007-4ece-a758-8e58581db76d)

## Solution
Since we suspect a kernel-level backdoor, the first step is to examine the kernel logs using `dmesg`:
```
sudo dmesg
```
In the output at the last few lines, we notice some suspicious activity:

![image](https://github.com/user-attachments/assets/736325b8-4f2f-4231-bca9-691b290a6a61)

- The module spatch failed verification and was loaded into the kernel.
- The [CIPHER BACKDOOR] logs indicate that this module provides a way to execute system commands.
- It directs us to the file `/proc/cipher_bd`, suggesting that commands can be written to this file for execution.

We can investigate the spatch module further using:
```
modinfo spatch
```

![image](https://github.com/user-attachments/assets/c3f0ffdc-2b61-4e73-9fb5-20b928f30d9d)

- The file path of the malicious kernel module is `/lib/modules/6.8.0-1016-aws/kernel/drivers/misc/spatch.ko`.
- The module description states "Cipher is always root", indicating it grants root access.

To find hidden information, we extract readable strings from the module using strings:
```
strings /lib/modules/6.8.0–1016-aws/kernel/drivers/misc/spatch.ko
```

![image](https://github.com/user-attachments/assets/f0ff5826-8347-4afa-ac25-80615526d5b7)

- "Here's the secret:" is followed by a long hex string: `54484d7b73757033725f736e33346b795f643030727d0a`.

To decode the hex value, copy and paste the secret into [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')&input=NTQ0ODRkN2I3Mzc1NzAzMzcyNWY3MzZlMzMzNDZiNzk1ZjY0MzAzMDcyN2QwYQ)

![image](https://github.com/user-attachments/assets/9708ff53-43d5-46f0-aec8-03f0778d155f)

Upon decoding, the flag was immediately revealed:
```
THM{sup3r_sn34ky_d00r}
```

## Conclusion
This challenge demonstrated how an attacker could use a kernel module to maintain deep persistence within a system. 
By analyzing the kernel logs, investigating suspicious modules, and extracting hidden strings, we successfully uncovered the backdoor and retrieved the flag.

## Flag
THM{sup3r_sn34ky_d00r}
