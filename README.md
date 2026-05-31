# Blots Language Support

This extension adds support for the [Blots](https://blots-lang.org) programming language to VSCode and other VSCode-based editors (such as Cursor).

Currently, this support consists of basic syntax highlighting.

## Install

This extension is available on both the [VSCode Marketplace](https://marketplace.visualstudio.com/items?itemName=Blots.blots-syntax) and the [Open VSX Registry](https://open-vsx.org/extension/blots/blots-syntax). You should be able to install it from within your editor like other extensions, but you can also download the VSIX file directly from either directory.

## Syntax files

VS Code uses `syntaxes/blots.tmLanguage.json`. [bat](https://github.com/sharkdp/bat) and other Sublime Text / syntect-based tools use `syntaxes/blots.sublime-syntax`. When you change highlighting rules, update both files (see below).

## bat

[bat](https://github.com/sharkdp/bat) can highlight `.blots` and `.blot` files using the grammar in this repository. There is no separate bat plugin; you add the syntax to your local bat config once.

From a clone of this repo:

```bash
mkdir -p "$(bat --config-dir)/syntaxes"
ln -sf "$(pwd)/syntaxes/blots.sublime-syntax" "$(bat --config-dir)/syntaxes/blots.sublime-syntax"
bat cache --build
bat --list-languages | rg -i blots
```

You should see `Blots:blots,blot`. Then:

```bash
bat path/to/file.blots
```

If a filename is not recognized, set the language explicitly (`bat -l Blots file.blots`) or add to your bat config:

```bash
--map-syntax '*.blots:Blots'
--map-syntax '*.blot:Blots'
```

### Regenerating `blots.sublime-syntax`

After editing `blots.tmLanguage.json`, regenerate the Sublime syntax (requires Ruby):

```bash
cd syntaxes
python3 -c "
import json, plistlib
with open('blots.tmLanguage.json') as f:
    plistlib.dump(json.load(f), open('blots.tmLanguage', 'wb'))
"
gem install sublime_syntax_convertor   # once, if needed
sublime_syntax_convertor blots.tmLanguage
rm blots.tmLanguage
```

Then restore `file_extensions` (`blots`, `blot`) at the top of `blots.sublime-syntax` if the converter dropped them, and run `bat cache --build` again.

To propose built-in bat support upstream, see [bat’s assets guide](https://github.com/sharkdp/bat/blob/master/doc/assets.md). That is optional; the local setup above is enough for daily use.
