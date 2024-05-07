#2. Creating simple bash scripts

##2.1. The vi editor

There are an unlimited number of editors available for writing shell scripts in Linux and Mac. A few are installed by default; some are used through the terminal and some others use a graphical interface (GUI). They all have pros and cons and the decision to use one or another is totally personal. In this section I will explain how to use the editor vi, which is installed by default in both Linux and MacOS and can be accessed and used through the terminal window.

These are some of the advantages of vi:

- It is available in all Unix systems and any type of terminal.
- It doesn't require a lot of memory to run. So, if you are running many heavy programs in your computer, vi is a good option because it won't slow down the machine anymore and will still load very fast.
- Even though there are a lot of commands that you must learn to become skilled in using this program, once you learn them you can use very short and fast commands to accomplish a lot of things. For example, you can delete all the characters from your current cursor location to the end of the line, just by pressing two times the d in your keyboard (while in command mode). Below you will find a chart with the most commonly used shortcuts in vi.
- You can use it to code in different languages (i.e. it also works for Python, HTML, etc.).
- Some editors add special characters to the text and when you run scripts written in those editors, they could fail. vi doesn't add any special characters, and if it did, they are visible in the editor.
- If you don't like using your mouse too much, or you don't have a mouse, or your mouse doesn't work properly, then vi editor is a good choice because you rarely need to use the mouse. Most things are accomplished using the keyboard.

However, there are some disadvantages too:

- The learning curve can be steep, especially for people who are new to programming and not very comfortable with computers. If you're new to bash, you not only need to learn the language, but also a whole set of commands specific to this program.
- Not being able to use your mouse can end up wasting your time while you learn all the commands that are used to scroll around the file or jump from one line to another.
- It doesn't give you any error messages or explanation of why it's not doing what you want. If you type the wrong command, it will just do nothing (or do the wrong thing).

Generally, many people with relatively good experience programming love this editor. In my opinion, it's a good tool to use once you are familiar with shell and have been programming for a while. However, it might be a great option for you if you like challenges or if you are already somehow familiar with shell.

To create a new file, type (on the command line) vi <filePath>/<filename>. For example, if you want to create a file called test.sh in the desktop, you should type vi ~/Desktop/test.sh. The vi editor will open in the current terminal.

This program runs in two modes, the command mode and the typing mode. By default, it opens in command mode. What this means is that anything you type is not actually being registered in the file but are commands. For example, if you type :q! as soon as you open the file, it will quit without saving (because :q! is the command for ignoring any modifications and exiting. If you type :w it will save changes to the file (or if you haven't write anything, it will just create an empty file). To change to typing mode, type a (a is the command for entering typing mode). After you type a (in lower case), you can start editing your file. To go back to command mode (for example to save changes), press the key esc (top left corner of the keyboard).

Using the vi editor for the first time Open the vi editor, create a script that prints "Hello Word", and save it with the file name helloword.sh

###Step 1: 
Open the vi editor

![image1](./vi_1.png)

###Step 2: 
Type a to start editing the file. You will see that an **--Insert--** message in the bottom of the terminal appears. This means that now you are in typing mode.

![image2](./vi_2.png)

###Step3:
Start typing commands. The simplest command, to print a message such as "Hello World" (or any other) is echo. The syntax of this command is the word ```echo``` followed by the message you want to print out between quotation marks (in this case "Hello World!").

![image3](./vi_3.png)

###Step4:
Once you finish your script, press the esc key to enter command mode. You will see that the **--Insert--** message at the bottom of the terminal disappears (this means that now you are in command mode).

![image4](./vi_4.png)

###Step5:
Save changes and exit the vi editor. In order to do this, type ```:wq``` (to write (w) and quit (q) at the same time). The table below will show you the most commonly used commands for vi.

![image5](./vi_5.png)

The following table shows a **list of vi commands** that can be used in command mode and the action that will happen in each case. In general, a number preceding any vi command will tell vi to repeat the command that number of times. For example, ```p``` is the command for pasting. If you write (in command mode) ```2p```, then vi will paste whatever you copied two times where the cursor is currently located.

|Key/command | Action |
|-----------| ------- |
| [ESC] | Switch to command mode |  
| [ctrl] ```b``` | 	Scroll backward one screen |  
| [ctrl] ```d``` | Scroll down half screen | 
| [ctrl] ```f``` | Scroll forward one screen |
| [ctrl] ```f``` | 	Scroll forward one screen |
| [ctrl] ```u``` | Scroll up half screen |
| ```.``` | Repeat last command |
| ```$``` | Go to end of line |
| ```? string``` | Search backward for ```string``` |
| ```/ string``` | Search forward for ```string``` |
| ```:0``` | Go to beginning of line |
| ```:N``` | Go to line ```N``` |
| ```:N,Md``` | Delete lines ```N``` to ```M``` |
| ```:N,MmP``` | Move lines ```N``` to ```M``` and paste them after line ```P``` |
| ```:N,MmP``` | Copy lines ```N``` to ```M``` and paste them after line ```P``` |
| ```:N,Mw file``` | Save lines ```N``` to ```M``` to ```file``` |
| ```:q``` | Quit (does not save any changes) |
| ```:q!``` | Ignore any modifications made and quit. Searches and replaces the string old by the string new in the entire file. |
| ```:%s/old/new/option``` | The following letters can be added in the field option: ```c``` to prompt for confirmation, ```g``` to replace all the occurrences of the string, Searches and replaces the string old by the string new in the line in which the cursor is located |
| ```:s/old/new/option``` | The following letters can be added in the field option: ```c``` to prompt for confirmation, ```g``` to replace all the occurrences of the string |
| ```:set ignorecase``` | Ignore case sensitivity during search |
| ```:set noignorecase``` | Restore case sensitivity during search |
| ```:set number``` | Turn on line numbering |
| ```:set nonumber``` | Turn off line numbering |
| ```:syntax on``` | Turn on syntax colors in the text |
| ```:syntax off``` | 	Turn off syntax colors in the text |
| ```:w``` | Save changes |
| ```:w file ```| Save changes to ```file``` |
| ```:wq``` | Save changes and quit |
| ```:x``` | Save changes and quit |
| ```a``` | Switch to editing mode and continue writing where the cursor is located |
| ```A``` | Switch to editing mode and continue writing at the end of the line where the cursor is located |
| ```i``` | 	Switch to editing mode and continue writing where the cursor is located |
| ```I``` | Switch to editing mode and continue writing at the beginning of the line where the cursor is located |
| ```cw``` | Delete the rest of the word in which the cursor is located (keeps the characters located before the cursor) |
| ```D``` | Delete the rest of the line in which the cursor is located (keeps the characters and words located before the cursor) |
| ```dd``` | Delete the entire line where the cursor is located |
| ```dw``` | Delete the whole word in which the cursor is located |
| ```J``` | Put the next line at the end of the line where the cursor is located |
| ```o``` | 	Open line below cursor |
| ```O``` | 	Open line above cursor |
| ```p``` | Paste below current line |
| ```P``` | 	Paste above current line |
| ```:u``` | Undo previous command |
| ```:U``` | Undo all changes to line |
| ```x``` | Delete text at cursor |
| ```X``` | 	Delete (backspace) text at cursor |
| ```yy``` | Copy line in which the cursor is located |