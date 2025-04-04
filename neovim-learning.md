Install neovim and run ```nvim``` command to start.

- To close Neovim tab ```:bd```

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
-- https://github.com/nvim-neo-tree/neo-tree.nvim/blob/main/doc/neo-tree.txt
-- https://www.lazyvim.org/extras/editor/neo-tree
return {
  "nvim-neo-tree/neo-tree.nvim",
  branch = "v3.x",
  dependencies = {
    "nvim-lua/plenary.nvim",
    "nvim-tree/nvim-web-devicons", -- not strictly required, but recommended
    "MunifTanjim/nui.nvim",
  },
  opts = {
    sources = { "filesystem", "buffers", "git_status" },
    open_files_do_not_replace_types = { "terminal", "Trouble", "trouble", "qf", "Outline" },
    filesystem = {
      bind_to_cwd = true,
      follow_current_file = { enabled = true },
      use_libuv_file_watcher = true,
    }
  },  
  config = function(_, opts)
    require("neo-tree").setup(opts)
    vim.keymap.set('n', '<C-n>', ':Neotree filesystem reveal left<CR>', {})
    vim.keymap.set('n', '<C-g>', ':Neotree float git_status<CR>', {})
    -- vim.keymap.set('n', '<C-/>', ':Neotree toggle current reveal_force_cwd<CR>', {})
  end
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

Modified ```init.lua``` file:
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
require("vim-options")
local opts ={}
require("lazy").setup("plugins", opts)
```

Now adding a new plugin is pretty easy. Let's try to add a new plugin called [lualine](https://github.com/nvim-lualine/lualine.nvim).
Create a new file called lualine.lua with below content.

```lua
return {
    "nvim-lualine/lualine.nvim",
    dependencies = {"nvim-tree/nvim-web-devicons", "catppuccin/nvim"},
    config = function()
        require("lualine").setup({options = {theme = "catppuccin"}})
    end
}
```

# LSP Integration & Configuration
LSP stands for Local Server Protocol, it's open json RPC standard that allows communication between text editors and language servers running 
on our machine that give language intelligence feature. Originally this was developed by Microsoft for C# integration with Visual studio code.

Mason is the Neovim plugin that installs and manages LSPs on our system.

Let's create lsp-config.lua file in plugins directory with below content:
```lua
return {
  "williamboman/mason.nvim",
  config = function()
    require("mason").setup()
  end
}
```

Now we have access to set of commands with prefix ```Mason```. If we type ```:Mason``` we see Mason UI with list of LSPs.

We wiil be installing another package ```mason lspconfig```. It bridges the gap between ```mason``` and ```lspconfig``` plugin.
It has ensure install functionality this tells neovim and it tells mason that we need to install these language servers before we do anything else.

```lua
return {
  {
    "williamboman/mason.nvim",
    config = function()
      require("mason").setup()
    end
  },
  {
    "williamboman/mason-lspconfig.nvim",
    config = function()
      require("mason-lspconfig").setup({
        ensure_installed = {
          "lua_ls", "clangd", "gopls", "java_language_server", "tsserver"
        }
      })
    end
  }
}
```

Look at [this](https://github.com/williamboman/mason-lspconfig.nvim?tab=readme-ov-file#available-lsp-servers) list of LSP servers available.
We have installed the language server but Neovim is not hooked up to this Language server yet. For that we are going to install another plugin called [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig)

```lua
return {
  {
    "williamboman/mason.nvim",
    config = function()
      require("mason").setup()
    end
  },
  {
    "williamboman/mason-lspconfig.nvim",
    config = function()
      require("mason-lspconfig").setup({
        ensure_installed = {
          "lua_ls", "clangd", "gopls", "java_language_server", "tsserver"
        }
      })
    end
  },
  {
    "neovim/nvim-lspconfig",
    config = function()
      local lspconfig = require("lspconfig")
      lspconfig.lua_ls.setup({})
      lspconfig.clangd.setup({})
      lspconfig.gopls.setup({})
      lspconfig.java_language_server.setup({})
      lspconfig.tsserver.setup({})
    end
  }
}
```

```:LspInfo``` command gives detail of Lsp connected to current buffer.

To check what functionality Neovim gives to connect with language server is to look for the help docs as
```:h vim.lsp.buf```

And then we can setup some keymaps.

```lua
return {
  {
    "williamboman/mason.nvim",
    config = function()
      require("mason").setup()
    end
  },
  {
    "williamboman/mason-lspconfig.nvim",
    config = function()
      require("mason-lspconfig").setup({
        ensure_installed = {
          "lua_ls", "clangd", "gopls", "java_language_server", "tsserver"
        }
      })
    end
  },
  {
    "neovim/nvim-lspconfig",
    config = function()
      local lspconfig = require("lspconfig")
      lspconfig.lua_ls.setup({})
      lspconfig.clangd.setup({})
      lspconfig.gopls.setup({})
      lspconfig.java_language_server.setup({})
      lspconfig.tsserver.setup({})

      -- setup keymap
      vim.keymap.set('n', 'K', vim.lsp.buf.hover, {})
      vim.keymap.set('n', 'gd', vim.lsp.buf.definition, {})
      vim.keymap.set({ 'n', 'v' }, '<leader>ca', vim.lsp.buf.code_action, opts)

    end
  }
}
```

We can add more functionality by referring to that help documentation but it's much more easier to refer [sample config](https://github.com/neovim/nvim-lspconfig?tab=readme-ov-file#suggested-configuration).

We can use ```LspAttach``` to attach these configurations when a LSP attachs to a buffer.

We see that neovim complains about undefined variable vim. if we hit '<leader>ca' we can see suggested actions. We can install another plugin called telescope-ui-select, to show suggested actions in cool ui. Modify telescope plugin as below:

```lua
return {
  {
    'nvim-telescope/telescope.nvim', 
    tag = '0.1.6', 
    dependencies = { 'nvim-lua/plenary.nvim' },
    config = function()
      local builtin = require('telescope.builtin')
      vim.keymap.set('n', '<C-p>', builtin.find_files, {})
      vim.keymap.set('n', '<leader>fg', builtin.live_grep, {})
    end
  },
  {
    'nvim-telescope/telescope-ui-select.nvim',
    config = function()
      -- This is your opts table
      require("telescope").setup {
        extensions = {
          ["ui-select"] = {
            require("telescope.themes").get_dropdown {}
          }
        }
      }
      require("telescope").load_extension("ui-select")
    end
  }
}
```

CTRL+x followed by CTRL+o autocompletes.

# Linting & Formatting
NULL-LS is an amazing plugin that essentially wraps commandline tools (such as Rubocop, EsLint, Black etc.) into a generalized LSP enabling easy communication between these tools and NeoVim's builtin client. So instead of installing different plugins for linting and formatting, we could just use NULL-LS to wrap our tools in a generalized language server. However, NULL-LS was deprecated. It ties very closely with NeoVim's internals leaving it open to breaking from changes within NeoVim.

A great alternative has been created called [NONE-LS](https://github.com/nvimtools/none-ls.nvim) which is a community managed fork of NULL-LS.

None-LS doesn't rewrite any of the variables of Null-ls, so we are going to be calling null-ls instead of none-ls.

```lua
return {
  "nvimtools/none-ls.nvim",
  config = function()
    local null_ls = require("null-ls")
    null_ls.setup({
      sources = {
        null_ls.builtins.formatting.stylua,
        null_ls.builtins.diagnostics.eslint,
        null_ls.builtins.completion.spell,
      },
    })
  end
}
```

We can use Mason to install stylua by typing ```:Mason``` command, search (/ or ?) for stylua and press i to install.

Final thing that we want is setup a keymap for lua formatting.

```lua
return {
	"nvimtools/none-ls.nvim",
	config = function()
		local null_ls = require("null-ls")
		null_ls.setup({
			sources = {
				null_ls.builtins.formatting.stylua,
			},
		})
		vim.keymap.set("n", "<leader>gf", vim.lsp.buf.format, {})
	end,
}
```

Let's add linter formatter for ruby. We are going to use [Rubocop](https://github.com/rubocop/rubocop)

First let's configure treesitter for ruby.
```lua
return {
  "nvim-treesitter/nvim-treesitter", 
  build = ":TSUpdate",
  config = function() 
   local configs = require("nvim-treesitter.configs")
   configs.setup({
      ensure_installed = { "c", "lua", "vim", "vimdoc", "query", "elixir", "heex", "javascript", "html", "css", "java", "go", "ruby" },
      sync_install = false,
      highlight = { enable = true },
      indent = { enable = true },  
    })

    -- Keymaps for Treesitter
    vim.keymap.set('n', '<C-n>', ':Neotree filesystem reveal left<CR>', {})
  end
}
```
Instead of adding our language in ensure_installed section we can add another flag auto_install = true, which installs language support as we open a file which we haven't configured.

Now let's go to none-ls configuration and add few entries.
```lua
return {
	"nvimtools/none-ls.nvim",
	config = function()
		local null_ls = require("null-ls")
		null_ls.setup({
			sources = {
				null_ls.builtins.formatting.stylua,
				null_ls.builtins.diagnostics.rubocop,
				null_ls.builtins.formatting.rubocop,
			},
		})
		vim.keymap.set("n", "<leader>gf", vim.lsp.buf.format, {})
	end,
}
```

Now next thing is we go to Mason and install Rubocop.
Now let's add eslint (eslint_d in mason) and prettier for javascript projects.
We can use Black (formatter) and isort for python.

# Autocompletion & Snippets
nvim-cmp: This plugin is completion engine for NeoVim.
luasnip: Snippet engine for NeoVim, written in lua.
cmp-luanip: luasnip completion source for nvim-cmp. It's responsible for supplying nvim-cmp with list of possible snippets while we type.
Friendly Snippets: Collection of vscode like snippets, loaded by luasnip.
cmp-nvim-lsp: nvim-cmp source for neovim's builtin language server client. 

Add new file completions.lua in plugin directory by clicking 'a' and add below content.
```lua
return {
  {
    "L3MON4D3/LuaSnip",
    dependencies = {
      "saadparwaiz1/cmp_luasnip",
      "rafamadriz/friendly-snippets",
    },
  },
  {
    "hrsh7th/nvim-cmp",
    config = function()
      local cmp = require("cmp")
      require("luasnip.loaders.from_vscode").lazy_load()
      cmp.setup({
        snippet = {
          -- REQUIRED - you must specify a snippet engine
          expand = function(args)
            require("luasnip").lsp_expand(args.body) -- For `luasnip` users.
          end,
        },
        window = {
          completion = cmp.config.window.bordered(),
          documentation = cmp.config.window.bordered(),
        },
        mapping = cmp.mapping.preset.insert({
          ["<C-b>"] = cmp.mapping.scroll_docs(-4),
          ["<C-f>"] = cmp.mapping.scroll_docs(4),
          ["<C-Space>"] = cmp.mapping.complete(),
          ["<C-e>"] = cmp.mapping.abort(),
          ["<CR>"] = cmp.mapping.confirm({ select = true }), -- Accept currently selected item. Set `select` to `false` to only confirm explicitly selected items.
        }),
        sources = cmp.config.sources({
          -- { name = "nvim_lsp" },
          { name = "luasnip" }, -- For luasnip users.
        }, {
          { name = "buffer" },
        }),
      })
    end,
  },
}
```

Now we have main snippet functionality in our neovim now we can add more sources to our snippet completion configuration. The main one we want to add is [nvim-lsp](https://github.com/hrsh7th/cmp-nvim-lsp). It's nvim-cmp source for neovim's built-in language server client.

It reaches out to any LSPs attached in our current buffer and will ask it for completion recommendation. That LSP will give the recommendation and then cmp-lsp will also expand those completions. So we add another plugin as below:

```lua
return {
	{
"hrsh7th/cmp-nvim-lsp"
		},
  {
    "L3MON4D3/LuaSnip",
    dependencies = {
      "saadparwaiz1/cmp_luasnip",
      "rafamadriz/friendly-snippets",
    },
  },
  {
    "hrsh7th/nvim-cmp",
    config = function()
      local cmp = require("cmp")
      require("luasnip.loaders.from_vscode").lazy_load()
      cmp.setup({
        snippet = {
          -- REQUIRED - you must specify a snippet engine
          expand = function(args)
            require("luasnip").lsp_expand(args.body) -- For `luasnip` users.
          end,
        },
        window = {
          completion = cmp.config.window.bordered(),
          documentation = cmp.config.window.bordered(),
        },
        mapping = cmp.mapping.preset.insert({
          ["<C-b>"] = cmp.mapping.scroll_docs(-4),
          ["<C-f>"] = cmp.mapping.scroll_docs(4),
          ["<C-Space>"] = cmp.mapping.complete(),
          ["<C-e>"] = cmp.mapping.abort(),
          ["<CR>"] = cmp.mapping.confirm({ select = true }), -- Accept currently selected item. Set `select` to `false` to only confirm explicitly selected items.
        }),
        sources = cmp.config.sources({
          -- { name = "nvim_lsp" },
          { name = "luasnip" }, -- For luasnip users.
        }, {
          { name = "buffer" },
        }),
      })
    end,
  },
}
```

And add below line in our lsp-config.
```lua
local capabilities = require('cmp_nvim_lsp').default_capabilities()
```

```lsp-config.lua
return {
  {
    "williamboman/mason.nvim",
    config = function()
      require("mason").setup()
    end
  },
  {
    "williamboman/mason-lspconfig.nvim",
    config = function()
      require("mason-lspconfig").setup({
        ensure_installed = {
          "lua_ls", "clangd", "gopls", "java_language_server", "tsserver"
        }
      })
    end
  },
  {
    "neovim/nvim-lspconfig",
    lazy = false,
    config = function()
      local capabilities = require('cmp_nvim_lsp').default_capabilities()
      local lspconfig = require("lspconfig")
      lspconfig.lua_ls.setup({
	capabilities = capabilities
      })
      lspconfig.clangd.setup({
	capabilities = capabilities
      })
      lspconfig.gopls.setup({
	capabilities = capabilities
      })
      lspconfig.java_language_server.setup({
	capabilities = capabilities
      })
      lspconfig.tsserver.setup({
	capabilities = capabilities
      })

      -- setup keymap
      vim.keymap.set('n', 'K', vim.lsp.buf.hover, {})
      vim.keymap.set('n', 'gd', vim.lsp.buf.definition, {})
      vim.keymap.set({ 'n', 'v' }, '<leader>ca', vim.lsp.buf.code_action, opts)

    end
  }
}
```

# Neovim Dashboard
```lua
return {
    'goolord/alpha-nvim',
    dependencies = {
        'nvim-tree/nvim-web-devicons',
        'nvim-lua/plenary.nvim'
    },
    config = function ()
        require'alpha'.setup(require'alpha.themes.theta'.config)
    end
};
```
# Configure Debuggers
Debug adapter protocol has very similar story as Language Server Protocol. It's an open protocol which was developed by Microsoft for vscode.

We need to install [nvim-dap](https://github.com/mfussenegger/nvim-dap) which is a dap client built for neovim. Refer [this](https://github.com/mfussenegger/nvim-dap/blob/master/doc/dap.txt) link for more config options. Add plugin file called debuggers.lua with below content:

```lua
return {
	"mfussenegger/nvim-dap",
	config = function()
		local dap = require("dap")
		vim.keymap.set('n', '<Leader>dt', dap.toggle_breakpoint, {})
		vim.keymap.set('n', '<Leader>dc', dap.continue, {})
	end
}
```
Next plugin we need to install is [nvim-dap-ui](https://github.com/rcarriga/nvim-dap-ui) and add it as dependency.

```lua
return {
	"mfussenegger/nvim-dap",
	dependencies = {
		"rcarriga/nvim-dap-ui"
	},
	config = function()
		local dap, dapui = require("dap"), require("dapui")
		dap.listeners.before.attach.dapui_config = function()
		  dapui.open()
		end
		dap.listeners.before.launch.dapui_config = function()
		  dapui.open()
		end
		dap.listeners.before.event_terminated.dapui_config = function()
		  dapui.close()
		end
		dap.listeners.before.event_exited.dapui_config = function()
		  dapui.close()
		end
		vim.keymap.set('n', '<Leader>dt', dap.toggle_breakpoint, {})
		vim.keymap.set('n', '<Leader>dc', dap.continue, {})
	end
}
```

Now we need to configure debug adapter. There is a [wiki](https://github.com/mfussenegger/nvim-dap/wiki/Debug-Adapter-installation) dedicated to that only.
For Go debugger setup we need to install delve & https://github.com/leoluz/nvim-dap-go.

```lua
return {
	"mfussenegger/nvim-dap",
	dependencies = {
		"nvim-neotest/nvim-nio",
		"rcarriga/nvim-dap-ui",
		"leoluz/nvim-dap-go"
	},
	config = function()
		local dap, dapui = require("dap"), require("dapui")
		require("dap-go").setup()
		require("dapui").setup()
		dap.listeners.before.attach.dapui_config = function()
		  dapui.open()
		end
		dap.listeners.before.launch.dapui_config = function()
		  dapui.open()
		end
		dap.listeners.before.event_terminated.dapui_config = function()
		  dapui.close()
		end
		dap.listeners.before.event_exited.dapui_config = function()
		  dapui.close()
		end
		vim.keymap.set('n', '<Leader>dt', dap.toggle_breakpoint, {})
		vim.keymap.set('n', '<Leader>dc', dap.continue, {})
	end
}
```

# GO Setup
We will use below plugin:
https://github.com/ray-x/go.nvim

Create go-setup.plugin file with below content:

```lua
return {
  "ray-x/go.nvim",
  dependencies = {  -- optional packages
    "ray-x/guihua.lua",
    "neovim/nvim-lspconfig",
    "nvim-treesitter/nvim-treesitter",
  },
  config = function()
    require("go").setup()
    vim.keymap.set('n', '<leader>gr', ':cd %:p:h <BAR> :GoRun -F<CR>', {})
  end,
  event = {"CmdlineEnter"},
  ft = {"go", 'gomod'},
  build = ':lua require("go.install").update_all_sync()' -- if you need to install/update all binaries
}
```

# Install lazygit for git integration
Install [lazygit](https://github.com/jesseduffield/lazygit) and then [lazygit.nvim](https://github.com/kdheepak/lazygit.nvim?tab=readme-ov-file) pluging using the package manager you are using with nvim. I am using lazy here:

https://www.youtube.com/watch?v=CPLdltN7wgE

```lua
-- nvim v0.8.0
return {
    "kdheepak/lazygit.nvim",
    lazy = true,
    cmd = {
        "LazyGit",
        "LazyGitConfig",
        "LazyGitCurrentFile",
        "LazyGitFilter",
        "LazyGitFilterCurrentFile",
    },
    -- optional for floating window border decoration
    dependencies = {
        "nvim-lua/plenary.nvim",
    },
    -- setting the keybinding for LazyGit with 'keys' is recommended in
    -- order to load the plugin when the command is run for the first time
    keys = {
        { "<leader>lg", "<cmd>LazyGit<cr>", desc = "LazyGit" }
    }
}
```

Staging files quickly: Use arrow key to choose files and hit space to add them for staging. 
Commit: Press c for commiting and type commit message.
Push: SHIFT+ P to push the changes.
Pull: p to pull the changes.
