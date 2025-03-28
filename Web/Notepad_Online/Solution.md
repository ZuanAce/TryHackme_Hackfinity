# Notepad Online

## Challenge Description
> ![image](https://github.com/user-attachments/assets/bc3c0e62-5ba0-40ad-bd3e-68abcde85f4c)

## Solution
I accessed the website and logged in using the provided credentials. After logging in, I was redirected to the notes page.

![image](https://github.com/user-attachments/assets/0cf24214-cf4a-47fd-b12e-4c7af9d54f8c)

While browsing, I noticed the note_id parameter in the URL:

```
http://10.10.144.201/note.php?note_id=1
```

Hmm… note_id=1? What if I change that? 🤔

I tested different values for **note_id**, and inside `note_id=0`, I found the hidden flag: ```THM{i_can_see_your_notes}```

![image](https://github.com/user-attachments/assets/dc941ac2-0c4a-45bd-bbf0-03670b6e5566)

## Conclusion
This challenge demonstrated a classic case of Insecure Direct Object References (IDOR)—a vulnerability where an attacker can manipulate input parameters to access unauthorized data. 
By testing different values, I was able to view restricted information and retrieve the flag.

## Flag: 
THM{i_can_see_your_notes}




   
