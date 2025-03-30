## The Game v2

## Challenge Description
> ![image](https://github.com/user-attachments/assets/3915c142-e2f8-4d5d-afcf-48b723f9fad0)

## Solution
Having solved a similar challenge before (The Game), I decided to follow the same approach: decompiling the game, modifying the logic, and extracting the flag.

![image](https://github.com/user-attachments/assets/f6c05755-a33c-4e12-b34b-06179490a0bb)

Inside the project, I explored various scripts and game logic files. After some searching, I found `GUI.gd`, a script responsible for handling the game’s graphical interface and scoring system.

![image](https://github.com/user-attachments/assets/a5d7e31d-aae0-463c-abc1-a7e869a1e927)

The script contained a conditional check that triggered an event when the score reached 999999. This was a clear indicator that something important (likely the flag) would appear at this threshold.

To speed things up, I modified the condition to trigger at 0 or 1 instead.

![image](https://github.com/user-attachments/assets/94da9a6e-58d7-4ef2-bbc0-9dbbd98eb709)

After making the change, I saved the file and re-ran the project.

Upon launching the modified game, the flag was immediately revealed on the screen after scoring 100:
```
THM{MEMORY_CAN_CHANGE_4R34L$-$}
```

![image](https://github.com/user-attachments/assets/e1491ca5-556d-40c9-8d11-0baef67ad818)

## Conclusion
This challenge provided valuable insight into game hacking and reverse engineering Godot projects. By extracting and modifying the game logic, we successfully bypassed the scoring requirement and retrieved the flag.

 ## Flag
THM{MEMORY_CAN_CHANGE_4R34L$-$}
