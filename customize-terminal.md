# To make terminal good looking
Set default terminal to zsh. Install oh-my-zsh

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Install [Hack nerd font](https://www.nerdfonts.com/font-downloads) using nerd font installer

```sh
bash -c  "$(curl -fsSL https://raw.githubusercontent.com/officialrajdeepsingh/nerd-fonts-installer/main/install.sh)"
```

Update font cache and search the installed font:
```sh
fc-cache -fv
fc-list | grep Hack
```

Install powerline10k
```sh
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Update .zshrc to use this theme as below
```
ZSH_THEME="powerlevel10k/powerlevel10k"
```

To update vscode to use this font:
update font as "Hack Nerd Font"

# Add Cool Functionalities
Let's install [warp](https://www.warp.dev) terminal. It comes with lots of cool features, autocompletion, warp ai etc.

Install starship for good looking prompt.
```sh
curl -sS https://starship.rs/install.sh | sh
```

Add below in ~/.zshrc
```
eval "$(starship init zsh)"
```

Use predefined presets available [here](https://starship.rs/presets/)

And use a preset as below:
```sh
starship preset gruvbox-rainbow -o ~/.config/starship.toml
```

Use [this](https://github.com/typecraft-dev/dotfiles/blob/master/starship.toml) toml file for catpuccin colour theme with starship.
```sh
curl https://raw.githubusercontent.com/typecraft-dev/dotfiles/master/starship.toml -o ~/.config/starship.toml            
```