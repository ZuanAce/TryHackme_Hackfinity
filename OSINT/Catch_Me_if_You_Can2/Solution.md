# Catch Me if You Can 2

## Challenge Description
![image](https://github.com/user-attachments/assets/e87ddb0e-53cf-43c1-af51-71cd1994747c)

## Files
[beco-batman-2-1741192876782](https://github.com/user-attachments/assets/b4f53a11-4390-43ed-b8a6-63e970398cdb)

## Solution
At first, I assumed this was an **OSINT challenge**, but after closely inspecting the provided image, I noticed strange symbols that stood out.

![image](https://github.com/user-attachments/assets/2bb46f36-dee6-4ad8-9987-7b6604e9ce78)

The symbols appeared to be encoded using the **Pigpen Cipher**, a simple substitution cipher that replaces letters with geometric symbols.

To decode the message, I used an online **[Pigpen Cipher decoder](https://www.dcode.fr/pigpen-cipher)**. After entering the symbols, the output revealed the hidden location.

![image](https://github.com/user-attachments/assets/12e69065-dbaa-4058-8133-9c31c847cab3)

The decoded text pointed to a specific location: `torii portal`. Following the given flag format, the final flag is:

```
THM{torii_portal}
```





