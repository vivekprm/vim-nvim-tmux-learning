# Default Key Bindings

- By default CTRL + b is leader key.
- Create new window: <Leader Key> + c
  - It creates a new entry in the bottom bar.
  - To toggle between windows press <Leader Key> + n
  - We can also switch between windows by pressing <Leader Key> + <window number>
- To split window horizontally <Leader Key> + %
- To split window vertically <Leader Key> + "
- To cycle between panes use <Leader Key> + <arrow keys>
- To move a pane from one position to another <Leader Key> + }
- To move between different layout <Leader Key> + <Space>
- To rename a window switch to command mode with <Leader Key> + :
  - Then run rename-window window1 in the command mode.s
- To list tmux session `tmux ls` and from tmux we can run <Leader Key> + s
- To list windows in a session <Leader Key> + w
- To attach to previous session `tmux attach`
- To create new session `tmux`
- To detach from a session <Leader Key> + d
- To list currently open tmux sessions <Leader Key> + s
- To rename session switch to command mode <Leader Key> + :
  - Then run `rename-session <session name>`

To see the effect of above changes:
- Go to command mode by <Leader Key> + :
- Type special command: ```source ~/.tmux.conf```

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

```sh
bind-key h select-pane -L
bind-key j select-pane -D
bind-key k select-pane -U
bind-key l select-pane -R
```

or just use
```sh
setw -g mode-keys vi
```

Run this config
```sh
tmux source ~/.tmux.conf
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

Another plugin that we will install is https://github.com/christoomey/vim-tmux-navigator. It allows us to navigate between tmux and vim navigations opened in multiple panes seamlessly.

For status bar install "dracula/tmux" plugin. And add below configurations
```conf
set -g @dracula-show-powerline true
set -g @dracula-fixed-location "Mohali"
set -g @dracula-plugins "weather"
set -g @dracula-show-flags true
set -g @dracula-show-left-icon session

set -g status-position top
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

## Managing multiple TMUX sessions 
We will use https://github.com/jimeh/tmuxifier to manage multiple tmux sessions

Another one we can use is: https://github.com/tmuxinator/tmuxinator

Let's create new tmuxifier layout using:
```sh
tmuxifier new-session go-dev
```

Now it creates a file at ~/.tmuxifier/layouts/go-dev.session.sh

Open and edit this file as below:
```sh
# Set a custom session root path. Default is `$HOME`.
# Must be called before `initialize_session`.
session_root "/home/vivek/workspace/src/github.com/vivekprm"

# Create session with specified name if it does not already exist. If no
# argument is given, session name will be based on layout file name.
if initialize_session "go-dev"; then

  # Create a new window inline within session layout definition.
  new_window "personal"
  split_v 70
  run_cmd "ls -ltr"
  split_h 50
  run_cmd "ls"
  select_pane 0
  run_cmd "vim"
  # Load a defined window layout.
  #load_window "example"

  # Select the default active window on session creation.
  #select_window 1

fi

# Finalize session creation and switch/attach to it.
finalize_and_go_to_session

```

Now to use this layout run
```sh
tmuxifier load-session go-dev
```

We see that vertical split is quite small to fix that.
```sh
tmuxifier edit-session go-dev
```

and change it as below:
```sh
new_window "personal"
split_v 20
run_cmd "ls -ltr"
split_h 50
run_cmd "ls"
select_pane 0
run_cmd "vim"
```

Now let's create another layout session for frontend development.
```sh
tmuxifier new-session frontend
```

Add below configurations:
```sh
# Set a custom session root path. Default is `$HOME`.
# Must be called before `initialize_session`.
session_root "~/workspace/src/Projects/node-projects/nextjs-projects/whatsapp-clone"

# Create session with specified name if it does not already exist. If no
# argument is given, session name will be based on layout file name.
if initialize_session "frontend"; then

  # Create a new window inline within session layout definition.
  new_window "whatsapp-clone"
  split_v 20
  run_cmd "yarn && yarn dev"
  select_pane 0
  run_cmd "vim"

  # Load a defined window layout.
  #load_window "example"

  # Select the default active window on session creation.
  #select_window 1

fi

# Finalize session creation and switch/attach to it.
finalize_and_go_to_session
```

Now start this session with:
```sh
tmuxifier load-session frontend
```

Now to switch between these two app sessions use <C-s> + s
