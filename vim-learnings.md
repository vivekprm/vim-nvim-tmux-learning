- H, J, K, L are used for navigation.
- CTRl + r to redo.
- Press CTRL+O in insert mode to run normal mode commands.
- From insert mode press Ctrl+O, 0 beginning of line, Ctrl+O, $ end of line
- To search for a word under our cursor press *
- SHIFT+N to look for the previous match & N to go forward.
- CIW to change the word under cursor. Then press n to go to the next match and press .
- Another easier way to change all the occurrences of a word ```:%s/post/poops/g```, if we want confirmation before changing use ```:%s/post/poops/gc```
- VIW visually highlight a word and take any action, e.g. press Y to yank. Go somewhere else in the file and press P to paste it.
- To run any command from vim prefix it with ```:!<cmd>```
- CTRL+D and CTRL+U to scroll half the page.
- Un-commenting code:
  - Put your cursor on the first # character, press Ctrl + V, and go down until the last commented line and press x, that will delete all the # characters vertically.
- Commenting code:
  - First, go to the first line you want to comment, press Ctrl + V. This will put the editor in the VISUAL BLOCK mode.
  - Then using the arrow key and select until the last line
  - Now press Shift + I, which will put the editor in INSERT mode and then press #. This will add a hash to the first line.
  - Then press Esc (give it a second), and it will insert a # character on all other selected lines.


# Registers
In case we yanked mltiple times we can see all those using ```:reg``` command. It shows all the registers and their contents. These regsiters have all the actions that we perform. So to paste from register 3 we press ```"3P```. Similarly we can yank into specific register using ```"7Y```

If we want to get the content of a register in Insert mode, we can press the **“Ctrl-r”** followed by the register’s name.

# Special Registers
Special registers do specific things. E.g. * register (in MAC) + register (in Linux) is the system clipboard. So if we copy something into this register, it allows us to copy things into other programs. So if we highlight a code block and press ```"+Y``` we copied things into + register.

we should keep in mind that Vim must be compiled with a +clipboard feature to support system clipboard access. Also, in order to use “* to talk to the X Windows selection, the +x11-selection feature should be enabled.

Only available if clipboard provider is available:
```sh
sudo apt-get install -y xclip
```

% register always holds the name of the file you have currently open. So if we do ```"%p"``` we pasted the name of the file.

# Macros
So let's say we copied below data from somewhere and we want it to convert it into valid ruby array:

```
days =  [
  Monday
  Tuesday
  Wednesday
  Thursday
  Friday
  Saturday
  Sunday
]
```
For that we need to add quotes around each word and put comma. It will be a lot of work if we do it line by line. So using macro we can record and replay an action over and over. To start recording a macro we type ```q``` followed by the register we want to record into. e.g. ```qh``` will record the macro in register h. Now we perform the action on one line in such a way, so that it can we replayed. To replay recorded macro ```@h```. To apply the macro in next 5 lines ```5@h```

# References:
https://www.baeldung.com/linux/vim-registers
