# Default Key Bindings

- By default CTRL + b is leader key.
- Create new window: <Leader Key> + c
  - It creates a new entry in the bottom bar.
  - To toggle between windows press <Leader Key> + n
  - We can also switch between windows by pressing <Leader Key> + <window number>
- To split window horizontally <Leader Key> + %
- To split window vertically <Leader Key> + "
- To cycle between panes use <Leader Key> + <arrow keys>
- To rename a window switch to command mode with <Leader Key> + :
  - Then run rename-window window1 in the command mode.
- To detach from window <Leader Key> + d
- To list tmux session `tmux ls`
- To attach to previous session `tmux attach`
- To create new session `tmux`
- To list currently open tmux sessions <Leader Key> + s
- To rename session switch to command mode <Leader Key> + :
  - Then run `rename-session <session name>`

# Customizing Tmux

To reload config add below in ~/.tmux.conf

```
# To reload tmux config
unbind r
bind r source-file ~/.tmux.conf
```

## Changing Leader Key

CTRL + b is not very ergonomic. So will change it to CTRL + s

```
set -g prefix C-s
```

## Resize pane using mouse

```
set -g mouse on
```

## Changing the basic navigation

```
bind-key h select-pane -L
bind-key j select-pane -D
bind-key k select-pane -U
bind-key l select-pane -R
```

# Using TMUX Plugin Manager

Clone tpm repository

```
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

Add below config to ~/.tmux.conf

```
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'github_username/plugin_name#branch'
# set -g @plugin 'git@github.com:user/plugin'
# set -g @plugin 'git@bitbucket.com:user/plugin'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

# Make TMUX look beautiful

Install plugin: https://github.com/catppuccin/tmux

Add below configuration in .tmux.conf

```
set -g @plugin 'catppuccin/tmux'
# ...alongside
set -g @plugin 'tmux-plugins/tpm'
```

Then reload using <Leader Key> r and install using <Leader Key> I

## Setting TMUX status bar at top position

```
set-option -g status-position top
```
