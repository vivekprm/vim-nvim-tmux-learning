Install neovim and run ```nvim``` command to start.

# Configure neovim
Create init.lua file to configure Neovim

```sh
vim ~/.config/nvim/init.lua
```

Now first thing we do is configure indents as below:
```lua
set expandtab
set tabstop=2
set softtabstop=2
set shiftwidth=2
```

Then run ```:source %``` to execute this file. But we get error because it's not vim script file. We can configure vim lower level apis using [meta-accessors](https://github.com/nanotee/nvim-lua-guide?tab=readme-ov-file#using-meta-accessors)

We can use vim.cmd("<vim command here>") to run any vim command.

Let's create a quick macro: press q followed by q, to record a q macro.
Go to next line and hit @q to apply the macro at each line.

Now if we do ```:source %``` will get no error.

# Install package manager
There are two popular package manager options:
- [Packer](https://github.com/wbthomason/packer.nvim)
- [Lazy](https://github.com/wbthomason/packer.nvim)

Lazy has better performance and we will use it. To install it add below in init.lua file:

```lua
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not (vim.uv or vim.loop).fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", -- latest stable release
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)
```

Next add below line, below above code:
```lua
local plugins = {}
local opts ={}
require("lazy").setup(plugins, opts)
```

After adding this run ```:source %```

Now we can access Lazy using ```:Lazy```.

First plugin that we need to install is [colorscheme](https://github.com/catppuccin/nvim). Add below line in init.lua, under lazy plugins.
```lua
{ "catppuccin/nvim", name = "catppuccin", priority = 1000 }
```

Now to setup catppuccin, add below configuration in init.lua:
```lua
require("catppuccin").setup()
-- setup must be called before loading
vim.cmd.colorscheme "catppuccin"
```

As soon as we open init.lua again, Lazy prompt opens and install the missing plugin.

Next plugin that we are going to install is telescope for fuzzyfinding your files.
```lua
{
    'nvim-telescope/telescope.nvim', tag = '0.1.6',
-- or                              , branch = '0.1.x',
      dependencies = { 'nvim-lua/plenary.nvim' }
    }
```
Now to use telescope, we need to initialize it as below:
```lua
local builtin = require('telescope.builtin')
vim.keymap.set('n', '<C-p>', builtin.find_files, {})
vim.keymap.set('n', '<leader>fg', builtin.live_grep, {})
--vim.keymap.set('n', '<leader>fb', builtin.buffers, {})
--vim.keymap.set('n', '<leader>fh', builtin.help_tags, {})
```

We can set our leader key to space as below:
```lua
vim.g.mapleader = " "
```

Next we are going to install a plugin which is great for highlighting and indenting, called [treesitter](https://github.com/nvim-treesitter/nvim-treesitter). The goal of nvim-treesitter is both to provide a simple and easy way to use the interface for tree-sitter in Neovim and to provide some basic functionality such as highlighting based on it.

It's a tool that takes snippets of code and generates and abstract syntax tree out of it, which is easy to use by parsers. People have written parsers on top of treesitter that then go through the AST and parse out the pieces that it needs to highlight the text that you see on the screen.

To install it refer [this link](https://github.com/nvim-treesitter/nvim-treesitter/wiki/Installation). Add below line in init.lua file.
```lua
require("lazy").setup({{"nvim-treesitter/nvim-treesitter", build = ":TSUpdate"}})
```

It has ```:TSUpdate``` command to update all the parsers. We can add below configuration to init.lua

```lua
local configs = require("nvim-treesitter.configs")
configs.setup({
    ensure_installed = { "c", "lua", "vim", "vimdoc", "query", "elixir", "heex", "javascript", "html", "css", "java", "go" },
    sync_install = false,
    highlight = { enable = true },
    indent = { enable = true },  
})
```

We can use ```:TSInstall``` command as well to install additional parsers.

# Modularizing out init.lua config
Before we do that we are going to install another plugin to visualize our files, called [neo tree](https://github.com/nvim-neo-tree/neo-tree.nvim)

```lua
{
    "nvim-neo-tree/neo-tree.nvim",
    branch = "v3.x",
    dependencies = {
      "nvim-lua/plenary.nvim",
      "nvim-tree/nvim-web-devicons", -- not strictly required, but recommended
      "MunifTanjim/nui.nvim",
      -- "3rd/image.nvim", -- Optional image support in preview window: See `# Preview Mode` for more information
    }
}
```

Now we can run some [commands](https://github.com/nvim-neo-tree/neo-tree.nvim?tab=readme-ov-file#the-neotree-command). E.g. below command opens file explorer on right side.
```
:Neotree filesystem reveal right
```

We can configure below keymaps for easier use:
```lua
vim.keymap.set('n', '<C-n>', ':Neotree filesystem reveal left<CR>', {})
```

Press 'a' to add new dir or file.

Now if we look at the lazy's documentation especially [structuing your plugins section](https://github.com/folke/lazy.nvim?tab=readme-ov-file#-structuring-your-plugins), lazy has really cool feature, which is, there is a special directory called ~/.config/nvim/lua and we can add a file called ~/.config/nvim/lua/plugins.lua and we can remove the plugins table from init.lua and change it as below:

```lua
vim.cmd("set expandtab")
vim.cmd("set tabstop=2")
vim.cmd("set softtabstop=2")
vim.cmd("set shiftwidth=2")
vim.g.mapleader = " "

-- Install Lazy package manager.
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not (vim.uv or vim.loop).fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable", -- latest stable release
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

local opts = {}
require("lazy").setup("plugins")

-- catppuccin
require("catppuccin").setup()
-- setup must be called before loading
vim.cmd.colorscheme "catppuccin"

-- Telescope
local builtin = require('telescope.builtin')
vim.keymap.set('n', '<C-p>', builtin.find_files, {})
vim.keymap.set('n', '<leader>fg', builtin.live_grep, {})


-- Treesitter
local configs = require("nvim-treesitter.configs")
configs.setup({
    ensure_installed = { "c", "lua", "vim", "vimdoc", "query", "elixir", "heex", "javascript", "html", "css", "java", "go" },
    sync_install = false,
    highlight = { enable = true },
    indent = { enable = true },  
})

-- Keymaps for Treesitter
vim.keymap.set('n', '<C-n>', ':Neotree filesystem reveal left<CR>', {})
-- Ends
```

Content of ~/.config/nvim/lua/plugins.lua file:
```lua
return {
  {"catppuccin/nvim", name = "catppuccin", priority = 1000},
  {'nvim-telescope/telescope.nvim', tag = '0.1.6', dependencies = { 'nvim-lua/plenary.nvim' }},
  {"nvim-treesitter/nvim-treesitter", build = ":TSUpdate"},
  {
    "nvim-neo-tree/neo-tree.nvim",
    branch = "v3.x",
    dependencies = {
      "nvim-lua/plenary.nvim",
      "nvim-tree/nvim-web-devicons", -- not strictly required, but recommended
      "MunifTanjim/nui.nvim",
    }
  }
}
```

We may want to split our plugin specs in multiple files. Instead of passing a spec table to setup(), we can use a Lua module. The specs from the module and any top-level sub-modules will be merged together in the final spec, so it is not needed to add require calls in your main plugin file to the other files.

The benefits of using this approach:

Simple to add new plugin specs. Just create a new file in your plugins module.
Allows for caching of all your plugin specs. This becomes important if you have a lot of smaller plugin specs.
Spec changes will automatically be reloaded when they're updated, so the :Lazy UI is always up to date.

So let's add a new directory ~/.config/nvim/lua/plugins and add our first plugin ~/.config/nvim/lua/plugins/catppuccin.lua.
```lua
return {"catppuccin/nvim", name = "catppuccin", priority = 1000}
```

But we still have few references left in init.lua.  we can move that inside a config function in catppuccin.lua file.

```lua
return {
  "catppuccin/nvim", name = "catppuccin", priority = 1000,
  config = function()
    vim.cmd.colorscheme "catppuccin"
  end
}
```

Now let's do the same with telescope.
```lua
return {
  'nvim-telescope/telescope.nvim', 
  tag = '0.1.6', 
  dependencies = { 'nvim-lua/plenary.nvim' },
  config = function()
    local builtin = require('telescope.builtin')
    vim.keymap.set('n', '<C-p>', builtin.find_files, {})
    vim.keymap.set('n', '<leader>fg', builtin.live_grep, {})
  end
}
```

and treesitter
```lua
return {
  "nvim-treesitter/nvim-treesitter", 
  build = ":TSUpdate"
  config = function() 
   local configs = require("nvim-treesitter.configs")
   configs.setup({
      ensure_installed = { "c", "lua", "vim", "vimdoc", "query", "elixir", "heex", "javascript", "html", "css", "java", "go" },
      sync_install = false,
      highlight = { enable = true },
      indent = { enable = true },  
    })

    -- Keymaps for Treesitter
    vim.keymap.set('n', '<C-n>', ':Neotree filesystem reveal left<CR>', {})
  end
}
```

and neo-tree
```lua
return {
  "nvim-neo-tree/neo-tree.nvim",
  branch = "v3.x",
  dependencies = {
    "nvim-lua/plenary.nvim",
    "nvim-tree/nvim-web-devicons", -- not strictly required, but recommended
    "MunifTanjim/nui.nvim",
  }
}
```

One last thing that we can do is move our vim settings to a separate file. Now add vim-options.lua file inside ~/.config/nvim/lua directory.

```lua
vim.cmd("set expandtab")
vim.cmd("set tabstop=2")
vim.cmd("set softtabstop=2")
vim.cmd("set shiftwidth=2")
vim.g.mapleader = " "
```

Now adding a new plugin is pretty easy. Let's try to add a new plugin called [lualine](https://github.com/nvim-lualine/lualine.nvim).
Create a new file called lualine.lua with below content.

```lua
return {
    'nvim-lualine/lualine.nvim',
    dependencies = { 'nvim-tree/nvim-web-devicons' },
    require('lualine').setup {
      config = function()
        options = { theme = 'dracula' }
      end
    }  
}
```
