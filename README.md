# cder.nvim

A [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim) extension for quickly changing your working directory.

![preview](https://user-images.githubusercontent.com/6345012/172458716-fa9d61db-61d4-4e66-a312-cbecfbc5d792.gif)

## Dependencies

- [telescope.nvim](https://github.com/nvim-telescope/telescope.nvim)
- By default, the extension uses [fd](https://github.com/sharkdp/fd) to list directories and [bat](https://github.com/sharkdp/bat) to provide paging. You don't have to use these if you don't want to, but the dafault config will not work if you do not install them.

## Setup

Install using your favorite package manager:

[packer.nvim](https://github.com/wbthomason/packer.nvim):

```lua
use 'zane-/cder.nvim'
```

Load the extension in telescope:

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
dir_command = { 'fd', '--type=d', '.', os.getenv('HOME') },

-- The binary used to execute previewer_command | pager_command.
command_executer = { 'bash', '-c' },

-- The command used to preview directories, defaults to ls.
-- Example:
--   previewer_command = {'exa', '-a', '--icons'}
previewer_command = 'ls -a',

-- The command used to as a pager. Receives the output of
-- the previewer_command as input. Defaults to bat.
-- Example without bat:
--   pager_command = 'less -RS'
pager_command = 'bat --plain --paging=always --pager="less -RS"',

-- Function to create an entry in the results picker given
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
```

To change these, use telescope's `setup` function:

```lua
require('telescope').setup({
  extensions = {
    cder = {
      previewer_command = 'exa --color=always -a --icons',
    },
  },
})
```

## Usage

- Open the extension with `:Telescope cder`
- Search for a directory name, pressing enter on the selection will change Neovim's working directory

### Mappings

| Mapping    | Action                                         |
|------------|------------------------------------------------|
| `Ctrl + d` | Scroll the preview down                        |
| `Ctrl + u` | Scroll the preview up                          |
