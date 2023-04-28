# cder.nvim

A [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) extension for quickly changing your working directory.

![preview](https://user-images.githubusercontent.com/6345012/172769523-8cc850d1-34d9-40ed-bc7a-d010fb877e0e.gif)

## Dependencies

- [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)
- By default, the extension uses [fd](https://github.com/sharkdp/fd) to list directories and [bat](https://github.com/sharkdp/bat) to provide paging. You don't have to use these if you don't want to, but the default config will not work if you do not install them.
- Additionally, `bash` is used to pipe the output of `fd` into `bat`, so if `bash` is not installed, the `command_executor` option will need to be changed.

## Setup

Install using your favorite plugin manager:

[packer.nvim](https://github.com/wbthomason/packer.nvim):

```lua
use 'zane-/cder.nvim'
```

Load the extension into telescope:

```lua
require('telescope').load_extension('cder')
```

Default config:

```lua
-- The title of the prompt.
prompt_title = 'cder',

-- The command used to generate a list of directories.
-- Defaults to fd on the home directory.
-- Example for showing hidden directories:
--   dir_command = { 'fd', '--hidden', '--type=d', '.', os.getenv('HOME') },
-- Example for excluding certain directories:
--   dir_command = { 'fd', '--exclude=Library', '--exclude=Pictures', '--type=d', '.', os.getenv('HOME') },  
dir_command = { 'fd', '--type=d', '.', os.getenv('HOME') },

-- The binary used to execute previewer_command | pager_command.
-- This is needed because termopen in Neovim does not support piping
-- multiple commands, so we get around this by just using bash -c.
command_executer = { 'bash', '-c' },

-- The command used to preview directories. Defaults to ls.
-- Example:
--   previewer_command = 'exa -a --icons'
previewer_command = 'ls -a',

-- A function to return an entry given an entry produced 
-- by dir_command. Returns the entry directly by default.
entry_value_fn = function(entry_value)
  return '"' .. entry_value .. '"'
end,

-- The command used to page directory previews. Defaults to bat.
-- Receives the output of the previewer_command as input.
-- Example without bat:
--   pager_command = 'less -RS'
pager_command = 'bat --plain --paging=always --pager="less -RS"',

-- Function to create an entry in the picker given
-- a line outputted from the dir_command.
--   value is used as the directory in the :cd command
--   display is what is actually displayed, so this can
--     be a function that trims a path prefix for example.
entry_maker = function(line)
  return {
    value = line,
    display = function(entry)
      return ' ' .. line:gsub(os.getenv('HOME') .. '/', ''), { { { 1, 3 }, 'Directory' } }
    end,
    ordinal = line,
  }
end,

-- A mapping should be a function that takes as parameter the selected
-- directory as a string.
mappings = {
  default = function(directory)
    vim.cmd.cd(directory)
  end,
  ['<C-t>'] = function(directory)
    vim.cmd.tcd(directory)
  end,
},
```

To change these, use telescope's `setup` function. Below is the config used in the demo gif above:

```lua
require('telescope').setup({
  extensions = {
    cder = {
      previewer_command =
        'exa '..
        '-a '..
        '--color=always '..
        '-T '..
        '--level=3 '..
        '--icons '..
        '--git-ignore '..
        '--long '..
        '--no-permissions '..
        '--no-user '..
        '--no-filesize '..
        '--git '..
        '--ignore-glob=.git',
    },
  },
})
```

## Usage

- Open the extension with `:Telescope cder`
- Search for a directory name, pressing enter on the selection will change Neovim's working directory

### Mappings

| Mapping    | Action                                    |
| ---------- | ----------------------------------------- |
| `Ctrl + d` | Scroll the preview down                   |
| `Ctrl + u` | Scroll the preview up                     |
| `Enter`    | Change directory                          |
| `Ctrl + t` | Change directory for the current tab page |
