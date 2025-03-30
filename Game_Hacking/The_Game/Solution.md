# The Game 

## Challenge Description
> ![image](https://github.com/user-attachments/assets/862ea051-ab05-4f01-b9f2-86f6ad4137ef)

## Solution
This is my first time tackling a game hacking challenge, and I was excited to explore new techniques. After launching the game, I noticed a crucial condition—reaching a score of 999999 might trigger an event (prolly printing the flag).

![image](https://github.com/user-attachments/assets/a04c57f5-a79e-4a36-b414-c8cd8eb12add)

Since achieving such a high score manually would be impractical, I decided to explore the game’s inner workings to modify the score directly.

The game was built using Godot, a popular open-source game engine. To reverse-engineer it, I used the [gdsdecomp tool](https://github.com/GDRETools/gdsdecomp) to extract the entire project from the given EXE file.

![image](https://github.com/user-attachments/assets/0ce22f76-72bb-438c-b705-0afb36a58ca6)

Next, I downloaded the [Godot Engine](https://godotengine.org/) and opened the extracted project.godot file.

Inside the project, I explored various scripts and game logic files. After some searching, I found `GUI.gd`, a script responsible for handling the game’s graphical interface and scoring system.

![image](https://github.com/user-attachments/assets/f4c4d83d-f089-4387-ae5e-3c2f97a32f71)

The script contained a conditional check that triggered an event when the score reached 999999. This was a clear indicator that something important (likely the flag) would appear at this threshold.

To speed things up, I modified the condition to trigger at 0 or 1 instead.

![image](https://github.com/user-attachments/assets/f09f58a9-2655-4fda-8653-6dbcb3dc7f28)

After making the change, I saved the file and re-ran the project.

Upon launching the modified game, the flag was immediately revealed on the screen after scoring 100:
```
THM{I_CAN_READ_IT_ALL}
```

![image](https://github.com/user-attachments/assets/58edf205-b49f-4cfb-8116-c9a6291ae655)

## Conclusion
This challenge provided valuable insight into game hacking and reverse engineering Godot projects. By extracting and modifying the game logic, we successfully bypassed the scoring requirement and retrieved the flag.

 ## Flag
THM{I_CAN_READ_IT_ALL}
