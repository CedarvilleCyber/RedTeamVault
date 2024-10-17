TMUX is a tool that allows you to multiplex your terminal. That's what tmux stands for - "terminal multiplexer." This means that it allows you to switch between as many terminal windows as you want, like how you can switch between different tabs in a browser. It is one of the most useful tools in existence for Linux CLI users. 

The command prefix is Ctrl B (pressing the 'Ctrl' and 'B' characters simultaneously). Once you enter the prefix, you can enter a tmux command by pressing the corresponding letter.

Helpful commands (all preceded by the prefix):
- c - create a new window
- s - displays all of your active tmux sessions. (A session is a group of windows)
- : - this enters tmux command mode, which allows you to enter helpful tmux commands. For example, `set -g mouse` turns on your mouse in tmux, allowing you to scroll through the terminal.
- x - kills the current pane.
- " - splits the current window into two panes, one on top of the other.
- % - splits the current window into two panes, one on the left and one on the right.
- n - next window
- p - previous window
- 0 to 9 - takes you to the corresponding window (navigates through windows 0-9)

Many people remap the tmux prefix to either `Ctrl A` or `Ctrl Space`, depending on their preferences. Be aware of this if using a different operator's tmux session. 