# Keymaps

For keymaps you are mapping yourself (as opposed to mappings set by other plugins),
the first two elements are the key and the handler, respectively. The handler
can be a command string like `:wa<CR>` or a Lua function.

Example:

```lua
local keymaps = {
  { '<leader>s', ':wa<CR>', description = 'Write all buffers', opts = {} },
  { '<leader>fm', vim.lsp.buf.formatting_sync, description = 'Format buffer with LSP' },
}
```

For "anonymous" mappings (you want them to appear in the finder but don't want `legendary.nvim`
to handle creating them, e.g. for plugin keymappings), just omit the second entry (the "implementation"):

```lua
local keymaps = {
  { '<leader>s', description = 'Write all buffers', opts = {} },
  { '<leader>fm', description = 'Format buffer with LSP' },
}
```

If you want to include a description, but do _not_ want the item to appear in the finder:

```lua
local keymaps = {
  {
    '<leader>s',
    description = 'Write all buffers',
    opts = {},
    -- hide from finder
    hide = true,
  },
}
```

If you need to pass parameters to the Lua function or call a function dynamically from a plugin,
you can use the following helper functions:

```lua
local toolbox = require('legendary.toolbox')
local keymaps = {
  { '<leader>p', toolbox.lazy(vim.lsp.buf.formatting_sync, nil, 1500), description = 'Format with 1.5s timeout' },
  { '<leader>f', toolbox.lazy_required_fn('telescope.builtin', 'oldfiles', { only_cwd = true }) },
  -- you can use a dot in the 2nd parameter to access functions nested in tables
  { '<leader>tt', toolbox.lazy_required_fn('neotest', 'run.run') },
}
```

The keymap's mode defaults to normal (`n`), but you can set a different mode, or list of modes, via
the `mode` property:

```lua
local keymaps = {
  { '<leader>c', ':CommentToggle<CR>', description = 'Toggle comment', mode = { 'n', 'v' } }
}
```

Alternatively, you can map separate implementations for each mode by passing the second
element as a table, where the table keys are the modes:

```lua
local keymaps = {
  { '<leader>c', { n = ':CommentToggle<CR>', v = ':VisualCommentToggle<CR>' }, description = 'Toggle comment' }
}
```

If you need to pass separate opts per-mode, you can do that too:

```lua
local keymaps = {
  {
    '<leader>c',
    {
      n = { ':CommentToggle<CR>' opts = { noremap = true } },
      v = { ':VisualCommentToggle<CR>' opts = { silent = false } }
    },
    description = 'Toggle comment'
    -- if outer opts exist, the inner opts tables will be merged,
    -- with the inner opts taking precedence
    opts = { expr = false }
  }
}
```

If you want the per-mode mappings to be treated as separate keymaps,
you can specify a separate description per-mode:

```lua
local keymaps = {
  {
    '<leader>c',
    {
      n = {
        ':Something<CR>',
        description = 'Something in normal mode',
        opts = { noremap = true }
      },
      v = {
        ':SomethingElse<CR>'
        opts = {
          -- you can also specify description through opts.desc
          -- if you prefer
          desc = 'Something else in visual mode',
          silent = false,
        }
      }
    },
    description = 'Toggle comment'
    -- if outer opts exist, the inner opts tables will be merged,
    -- with the inner opts taking precedence
    opts = { expr = false }
  }
}
```

You can also pass options to the keymap via the `opts` property, see `:h vim.keymap.set` to
see available options.

```lua
local keymaps = {
  {
    '<leader>fm',
    vim.lsp.buf.formatting_sync,
    description = 'Format buffer with LSP',
    opts = { silent = true, noremap = true }
  },
}
```

If you want a keymap to apply to both normal and insert mode, use a Lua function.

The `legendary.toolbox` module has utilities for determining visual mode and getting marks. This allows you to create
mappings like:

```lua
local keymaps = {
  {
    '<leader>c',
    function()
      if require('legendary.toolbox').is_visual_mode() then
        -- comment a visual block
        vim.cmd(":'<,'>CommentToggle")
      else
        -- comment a single line from normal mode
        vim.cmd(':CommentToggle')
      end
    end,
    description = 'Toggle comment',
    mode = { 'n', 'v' },
  }
}
```

If you want to register keymaps with `legendary.nvim` in order to see them in the finder, but not bind
them (like for keymaps set by other plugins), you can just omit the handler element:

```lua
local keymaps = {
  { '<C-d>', description = 'Scroll docs up' },
  { '<C-f>', description = 'Scroll docs down' },
}
```

You can also organize keymaps, commands, and functions into groups that will show up
in the finder UI like a folder, selecting it will then trigger another finder for items
within the group. If groups are given the same name, they will be merged.

```lua
local keymaps = {
  {
    -- name, indicates that this table is an item group
    itemgroup = 'short ID',
    -- you can also customize the icon for item groups
    icon = '',
    -- you can also customize the description (first text column)
    description = 'A group of items, this can be a little longer...',
    keymaps = {
      -- regular keymaps here
    },
  },
}
```
