# Dark Matter

## Challenge Description
> ![image](https://github.com/user-attachments/assets/67be6cb5-bfd6-4698-aa2c-fe7a32c8c943)


## Approach
This challenge presents a ransomware scenario where critical files from Hackfinitiy high school have been encrypted by "DarkInjector's ransomware." Our task is to recover the RSA private key to decrypt two important documents: `student_report.docx` and `student_grades.docx`.

Upon starting the machine, we're presented with a ransomware note demanding a decryption key:

![image](https://github.com/user-attachments/assets/79af204d-3ba7-444c-a73c-e524d8e98857)

Following the hint about challenge information, we examined the `/tmp` directory and found a file named `public_key.txt`:

![image](https://github.com/user-attachments/assets/ccb60652-ffed-44b7-8a22-c67257c629aa)

Inside the `public_key.txt` file:
```
n=340282366920938460843936948965011886881
e=65537
```

This represents an RSA public key, where n is the modulus and e is the public exponent.

To compute the private key (`d`), we used the [online RSA tool](https://www.dcode.fr/rsa-cipher) allowing us to retrieve: `d = 196442361873243903843228745541797845217`

![image](https://github.com/user-attachments/assets/9fdf9f80-e740-4201-95f6-3120c01f10af)

Entering `d` as the decryption key in the ransomware prompt successfully decrypted the encrypted files:
- student_report.docx
- student_grades.docx

![image](https://github.com/user-attachments/assets/b3e56d4c-ed6a-408c-95b1-4dcd1adcf9ad)

Opening `student_grades.docx` reveals the flag: `THM{d0nt_l34k_y0ur_w34k_m0dulu5}`

![image](https://github.com/user-attachments/assets/72b9d791-e00c-4385-b747-1837a6efbb58)

## Conclusion
When the modulus (n) is small enough to be factorized, or when primes are too close together, the encryption can be broken. Also, leaving sensitive information (like public keys) in temporary directories can compromise security. Exploiting these two flaws allow us to retrive the encrypted files and ofc, the flag.

## Flag: 
THM{d0nt_l34k_y0ur_w34k_m0dulu5}





   
