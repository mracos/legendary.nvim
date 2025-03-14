# Table Structures

The tables for keymaps, commands, and `augroup`/`autocmd`s are all similar.

Descriptions can be specified either in the top-level `description` property
on each table, or inside the `opts` table as `opts.desc = 'Description goes here'`.

For `autocmd`s, you must include a `description` property for it to appear in the finder.
This is a design decision because keymaps and commands are frequently executed manually,
so they should appear in the finder by default, while executing `autocmd`s manually with
`:doautocmd` is a much less common use-case, so `autocmd`s are hidden from the finder
unless a description is provided.

You can also run `:LegendaryApi`, then search for `/legendary.types` to read the full API
documentation on accepted types.

- [Keymaps](./KEYMAPS.md)
- [Commands](./COMMANDS.md)
- [Functions](./FUNCTIONS.md)
- [`augroup`/`autocmd`s](./AUTOCMDS.md)
