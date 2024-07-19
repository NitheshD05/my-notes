**Install Tmux**
```
sudo apt install tmux
```

**Initiate a Tmux Session**
```
tmux
```
Just like that...!

**Initiate a Tmux Session with name**
```
tmux new -s <Session_Nmame>
```
Example, To create session named Test
```
tmux new -s Test
```

**Detach from a session**
```
Ctrl+b then D
```

**Attach to the recent Session**
```
tmux a
```
To attach to particular Session or Window
```
tmux a -t <session_or_window_name>
```
Example, to attach to session named Test
```
tmux a -t Test
```

**Kill a Session**
```
tmux kill-session -t <session_Name>
```
Example, Kill session named **Test**
```
tmux kill-session -t Test
```

**List Tmux Sessions**
```
tmux ls
```

**Create a Window and a Session together**
```
tmux new -t <window_name>
```
Example, will create a Window Named **win1**
```
tmux new -t win1
```

**Create a pane Horizontal and Vertical**
```
Ctrl+b then %   \\this will create a Vertical Pane |
```
```
Ctrl+b then "   \\this will create a Horizontal Pane -
```
Move between Panes
```
Ctrl+b then use arrow keys 
```
Alternate
```
Ctrl+b then q   \\this will show you index number of panes
Ctrl+b then q+index_number  \\this get you to the pane that has the index value
Ctrl+b then q+2   \\this will get you to pane 2
```

**Change the Size of your Pane**
```
Ctrl+b then Ctrl+arrow_keys  \\this will help you to adjust your current Pane Size
```

**Create  multiple Window in a Session**
```
Ctrl+b then c  \\this will cretae a new window within a Session
```

**Move around Windows**
```
Ctrl+b then 0
Ctrl+b then 1
Ctrl+b then 2
Ctrl+b then 3

The number denotes Window Index, this will get you to the chosen index.
```
Alternate, But Powerful
```
Ctrl+b then w  \\this will show you the entire tmux session list, we can Navigate easily through the Sessions and Windows.
```

**Rename a Window**
```
Ctrl+b then ,   \\this will help you to rename a Window
```

**To kill a Window**
```
Ctrl+b then x  \\this kill kill a window which has no pane, if it has multiple panes, it will kill the pane you are currently in
Ctrl+b then &  \\this will kill entire window if it all mutiple pane

it will prompt for y/n to kill the  window, press y if you are sure to kill or n take to step back

You can either list sessions and windows you have created by Ctrl+b then w
and navigate to the window you want to kill OR navigate and get into the window and use the above killing keys
```

**Copy Mode**
Open a tmux config file 
```
nano ~/.tmux.conf
```

Enter the text in to the config file and save it
```
set -g mouse on
setw -g mode-keys vi
```
Get into your tmux session, now you can copy by just selecting the text you want
Another way
```
Ctrl+b then [  \\this will enable copy mode and move your cursor to from where you want to copy and press Spacebar and move arrow keyy to select the conenten and Press Enter, it will be Copied and you can paste it by Ctrl+b then ] to paste the content
```

To Copy the Content from tmux to kali or host machine
```
Select the content to copy as same as above, Press y intead of ENTER
Now the content will be copied to system Keyboard
```

To Paste the content that is copied outside the tmux Session
```
Just normaly copy the content Ctrl+c
Ctrl+Shift+v \\to paste the content that is in system Clipboard
```

Config file for tmux
```
set -g mouse on
setw -g mode-keys vi
set-option -g set-clipboard on
bind-key -T copy-mode-vi y send-keys -X copy-pipe-and-cancel 'xclip -selection clipboard -in'
bind-key -T copy-mode y send-keys -X copy-pipe-and-cancel 'xclip -selection clipboard -in'
```

To search for a window
```
Ctrl+b then . \\this will ask you to enter the window name and pree ENTER
It will tell you the index number of the Window
```

