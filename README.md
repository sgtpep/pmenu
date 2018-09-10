# pmenu

A dynamic terminal-based menu inspired by dmenu written in Python without dependencies with an optional [MRU](https://en.wikipedia.org/wiki/Most_Recently_Used) ordering which could also be used as an application launcher and CtrlP alternative.

Discussion: https://bbs.archlinux.org/viewtopic.php?id=201674.

## Dependencies

Python 3.3+.

## Usage examples

Display some menu items:

```bash
echo -e "foo\nbar\nbaz" | pmenu
pmenu foo bar baz
echo -e "foo\nbar" | pmenu baz qux
```

![screencast 1](https://raw.githubusercontent.com/sgtpep/pmenu/master/screencasts/1.gif)

Pick some file from the current directory:

```bash
command ls /usr/bin/ | pmenu
find -maxdepth 3 -type f ! -path "./.git/*" ! -path "./.svn/*" -printf '%P\n' | LC_COLLATE=C sort | pmenu
```

![screencast 2](https://raw.githubusercontent.com/sgtpep/pmenu/master/screencasts/2.gif)

Pick some file from the current directory for editing in VIM using Ctrl-P shortcut (a la the [CtrlP](http://kien.github.io/ctrlp.vim/) plugin):

```vim
function! Pmenu()
  let item_command = "find -maxdepth 3 -type f -regextype posix-egrep ! -regex '.*/(__pycache__|\.git|\.svn|node_modules)/.*' -printf '%P\\n'"
  if isdirectory("./.git")
    let item_command = "git ls-files"
  endif
  let cache_name = fnamemodify(getcwd(), ":t")
  let items = sort(systemlist(item_command))
  let current_item = expand("%:.")
  if !empty(current_item)
    let items = filter(copy(items), "v:val != " . shellescape(current_item))
  endif
  let selected_items = systemlist("pmenu -n " . shellescape(cache_name), items)
  if !empty(selected_items)
    execute "edit " . fnameescape(selected_items[0])
  endif
  redraw!
endfunction
nnoremap <silent> <C-P> :call Pmenu()<CR>
vnoremap <silent> <C-P> :call Pmenu()<CR>
```

![screencast 3](https://raw.githubusercontent.com/sgtpep/pmenu/master/screencasts/3.gif)

Pick a title from the markdown file and jump to it:

```vim
function! PmenuMarkdownTitle()
  let titles = filter(getline(1, '$'), "v:val =~ '^#\\+\\s'")
  let selected_paths = systemlist('pmenu', titles)
  if !empty(selected_paths)
    call search('^#\+\s' . selected_paths[0])
  endif
  redraw!
endfunction
nnoremap <silent> <C-T> :call PmenuMarkdownTitle()<CR>
```

Pick and show a definition from the WordNet dictionary on the dict server (dict.org by default) using either the curl or dict command:

```bash
pmenu -c "m={} && curl -s \"dict://dict.org/m:\${m:-a}:wn:prefix\" | awk -F \\\" '! array[tolower(\$2)]++ { print \$2 }'" | xargs -I '{}' curl -s "dict://dict.org/d:{}:wn" | grep -vP "^(\d+ |\.)" | less
pmenu -c "dict -fm -d wn -s prefix -- {} | awk '! array[tolower(\$4)]++ { print \$4 }'" | xargs dict -d wn | less
```

Pick and start a VirtualBox virtual machine:

```bash
vboxmanage startvm "`vboxmanage list vms | sed 's/^"\(.*\)".*/\1/' | pmenu`"
```

Pick and jump to a subdirectory in ~/Projects:

```bash
cd "`cd ~/Projects; pmenu *`"
```

## pmenu-run

The script `pmenu-run` is an example of an application launcher built with `pmenu` similar to `dmenu_run`, `gmrun` and `bashrun`. It builds the menu from system \*.desktop files and launches the selected item in the current terminal or detached from it depending on the application type.

Bind any shortcut using your desktop preferences to one the following commands depending of what terminal emulator you use:

```bash
xterm -e pmenu-run
urxvt -e pmenu-run
```

`pmenu-run` passes all provided additional options to `pmenu`. This could be used to add more items to the launcher menu, like `pmenu-run command1 command2 command3`.

## Installation

Copy `pmenu` (and optionally `pmenu-run`) to any location from your `$PATH`, say `/usr/local/bin`.

There is the AUR package for Arch Linux: https://aur.archlinux.org/packages/pmenu/.

## Command-line interface

```
usage: pipe newline-separated menu items to stdin and/or pass them as positional arguments

positional arguments:
  item                  the menu item text

optional arguments:
  -h, --help            show this help message and exit
  -c COMMAND, --command COMMAND
                        the shell command which output will populate the menu
                        items on every keystroke ({} will be replaced by the
                        current input text)
  -n NAME, --name NAME  the cache file name with the most recently used items
  -p PROMPT, --prompt PROMPT
                        the prompt text
  -v, --version         show program's version number and exit
```

## Keyboard shortcuts

- `Ctrl-C`, `Ctrl-G`, `Ctrl-[`, `Escape`: quit without selecting an item
- `Ctrl-H`, `Backspace`: delete the character before the cursor
- `Ctrl-I`, `Tab`: complete the selected item
- `Ctrl-J`, `Ctrl-M`, `Enter`: quit and output the selected item
- `Ctrl-N`, 'Down': select the next item
- `Ctrl-P`, 'Up': select the previous item
- `Ctrl-U`: delete the entire line
- `Ctrl-W`: delete the word before the cursor

## Alternatives

### dmenu-like menus

- [Heatseeker](https://github.com/rschmitt/heatseeker) `Rust` `terminal`
- [Icepick](https://github.com/felipesere/icepick) `Rust` `terminal`
- [PathPicker](https://facebook.github.io/PathPicker/) `Python` `terminal`
- [Selecta](https://github.com/garybernhardt/selecta) `Ruby` `terminal`
- [dmenu2](https://bitbucket.org/melek/dmenu2) `C` `X11`
- [dmenu](http://tools.suckless.org/dmenu/) `C` `X11`
- [fzf](https://github.com/junegunn/fzf) `Go` `terminal`
- [fzy](https://github.com/jhawthorn/fzy) `C` `terminal`
- [gof](https://github.com/mattn/gof) `Go` `terminal`
- [gpicker](http://www.nongnu.org/gpicker/) `C` `X11`
- [happyfinder](https://github.com/hugows/hf) `Go` `terminal`
- [hmenu](https://github.com/sulami/hmenu) `Haskell` `terminal`
- [peco](https://github.com/peco/peco) `Go` `terminal`
- [percol](https://github.com/mooz/percol) `Python` `terminal`
- [pick](https://github.com/thoughtbot/pick) `C` `terminal`
- [rofi](https://github.com/DaveDavenport/rofi) `C` `X11`
- [selector](http://fleuret.org/cgi-bin/gitweb/gitweb.cgi?p=selector) `C` `terminal`
- [slmenu](https://bitbucket.org/rafaelgg/slmenu) `C` `terminal`
- [smenu](https://github.com/p-gen/smenu) `C` `terminal`
- [tmenu](https://github.com/dhamidi/tmenu) `C` `terminal`
- [tpick](https://github.com/smblott-github/tpick) `C` `terminal`
- [vis-menu](http://martanne.github.io/vis/man/vis-menu.1.html) `C` `terminal`

### dmenu wrappers

- [Yeganesh](http://dmwit.com/yeganesh/) `Haskel`
- [xboomx](https://github.com/victorhaggqvist/xboomx) `Python`
- [yegonesh](https://github.com/klowner/yegonesh) `Go`

### Application launchers

- [Albert](https://github.com/ManuelSchneid3r/albert) `C++` `X11`
- [Kupfer](http://engla.github.io/kupfer/) `Python` `X11`
- [bashrun2](http://henning-bekel.de/bashrun2/) `Bash` `terminal`
- [bashrun](http://bashrun.sourceforge.net/) `Bash` `terminal`
- [dmenu_run](http://tools.suckless.org/dmenu/) `Shell` `X11`
- [gmrun](http://sourceforge.net/projects/gmrun/) `C` `X11`
- [lighthouse](https://github.com/emgram769/lighthouse) `C` `X11`
- [rrun](https://github.com/buster/rrun) `Rust` `X11`
- [shellex](https://github.com/Merovius/shellex) `C` `X11`
- [xboomx](https://github.com/victorhaggqvist/xboomx) `Python` `X11`
- [xlauncher](https://github.com/vatriani/xlauncher) `C` `X11`

### Vim menus

- [CtrlP](http://kien.github.io/ctrlp.vim/)
- [FuzzyFinder](http://www.vim.org/scripts/script.php?script_id=1984)
- [LeaderF](https://github.com/Yggdroot/LeaderF)
- [LustyExplorer](http://www.vim.org/scripts/script.php?script_id=1890)
- [LycosaExplorer](http://www.vim.org/scripts/script.php?script_id=3659)
- [Unite](https://github.com/Shougo/unite.vim)
- [asyncfinder.vim](https://github.com/vim-scripts/asyncfinder.vim)
- [ku](http://www.vim.org/scripts/script.php?script_id=2337)

## License and copyright

The project is released under the General Public License (GPL), version 3.

Copyright © 2015, Danil Semelenov.
