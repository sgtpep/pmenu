# pmenu
Dynamic menu like dmenu for terminal written in Python without dependencies with optional sorting by usage, application launcher and CtrlP alternative.

Discussion: https://bbs.archlinux.org/viewtopic.php?id=201674.

## Dependencies

Python 3.3+.

## Usage examples

Display some menu items:

    echo -e "foo\nbar\nbaz" | pmenu
    pmenu foo bar baz
    echo -e "foo\nbar" | pmenu baz qux

![screencast 1](https://raw.githubusercontent.com/sgtpep/pmenu/master/screencasts/1.gif)

Pick some file from current directory:

    command ls /usr/bin/ | pmenu
    find -maxdepth 3 -type f ! -path "./.git/*" ! -path "./.svn/*" -printf '%P\n' | LC_COLLATE=C sort | pmenu

![screencast 2](https://raw.githubusercontent.com/sgtpep/pmenu/master/screencasts/2.gif)

Pick some file from current directory for editing in VIM using Ctrl-P shortcut (a la [CtrlP](http://kien.github.io/ctrlp.vim/) plugin):

    function! Pmenu()
      let mru_name = fnamemodify(getcwd(), ":t")
      if isdirectory("./.git")
        let filelist_command = "git ls-files"
        if !empty(expand('%'))
          let filelist_command .= " | grep -vxF " . shellescape(expand("%:."), 1)
        endif
      else
        let filelist_command = "find -maxdepth 3 -type f " . shellescape("./" . expand("%:."), 1) . " ! -path '*/.git/*' ! -path '*/.svn/*' -printf '%P\n' | LC_COLLATE=C sort"
      endif
      let selected_paths = systemlist(filelist_command . " | pmenu -n " . shellescape(mru_name, 1))
      if !empty(selected_paths)
        execute "edit " . selected_paths[0]
      endif
      redraw!
    endfunction
    nnoremap <silent> <C-P> :call Pmenu()<CR>
    vnoremap <silent> <C-P> :call Pmenu()<CR>

![screencast 3](https://raw.githubusercontent.com/sgtpep/pmenu/master/screencasts/3.gif)

Pick the title from the markdown file and jump to it:

    function! PmenuMarkdownTitle()
      let titles = filter(getline(1, '$'), "v:val =~ '^#\\+\\s'")
      let selected_paths = systemlist('pmenu', titles)
      if !empty(selected_paths)
        call search('^#\+\s' . selected_paths[0])
      endif
      redraw!
    endfunction
    nnoremap <silent> <C-T> :call PmenuMarkdownTitle()<CR>

## pmenu-run

The script `pmenu-run` is an example of application launcher built with `pmenu` similar to `dmenu_run`, `gmrun` and `bashrun`. It scans for the aplication commands in \*.desktop files, detects if they are intended for terminal and automatically run them attached or detached to the it.

Bind some desktop shortcut to one the following commands depending of what terminal emulator you use:

    xterm -T run -e pmenu-run
    urxvt -title run -e bash -i -c "pmenu-run; :"

`pmenu-run` passes all provided options to `pmenu`. This could be used to add more items to application launcher, like `pmenu-run command1 command2 command3`.

## Installation

Copy `pmenu` (and optionally `pmenu-run`) to any location inside your `$PATH`, say `/usr/local/bin`.

On Arch Linux AUR package is available: https://aur.archlinux.org/packages/pmenu/.

## Command-line interface

    usage: pipe menu items to stdin or pass them as positional arguments

    positional arguments:
      item                  menu item text

    optional arguments:
      -h, --help            show this help message and exit
      -n NAME, --name NAME  name of the usage cache
      -p PROMPT, --prompt PROMPT
                            prompt text
      -v, --version         show program's version number and exit

## Keyboard shortcuts

- `Ctrl-C`, `Ctrl-G`, `Ctrl-[`, `Escape`: quit without selecting a match
- `Ctrl-H`, `Backspace`: delete the character before the cursor
- `Ctrl-I`, `Tab`: complete the selected item
- `Ctrl-J`, `Ctrl-M`, `Enter`: quit and output the selected item
- `Ctrl-N`, 'Down': select the next match
- `Ctrl-P`, 'Up': select the previous match
- `Ctrl-U`: delete the entire line
- `Ctrl-W`: delete the word before the cursor

## Alternatives

### dmenu-like menus

- [dmenu](http://tools.suckless.org/dmenu/), C, X11
- [dmenu2](https://bitbucket.org/melek/dmenu2), C, X11
- [rofi](https://github.com/DaveDavenport/rofi) C, X11
- [slmenu](https://bitbucket.org/rafaelgg/slmenu), C, terminal
- [percol](https://github.com/mooz/percol), Python, terminal
- [fzf](https://github.com/junegunn/fzf), Go, terminal
- [pick](https://github.com/thoughtbot/pick), C, terminal
- [Selecta](https://github.com/garybernhardt/selecta), Ruby, terminal
- [PathPicker](https://facebook.github.io/PathPicker/), Python, terminal
- [happyfinder](https://github.com/hugows/hf), Go, terminal
- [Heatseeker](https://github.com/rschmitt/heatseeker), Rust, terminal

### dmenu wrappers

- [Yeganesh](http://dmwit.com/yeganesh/), Haskel
- [yegonesh](https://github.com/klowner/yegonesh), Go
- [xboomx](https://github.com/victorhaggqvist/xboomx), Python

### Application launchers

- [dmenu\_run](http://tools.suckless.org/dmenu/), Shell, X11
- [gmrun](http://sourceforge.net/projects/gmrun/), C, X11
- [bashrun](http://bashrun.sourceforge.net/), Bash, terminal
- [bashrun2](http://henning-bekel.de/bashrun2/), Bash, terminal
- [xboomx](https://github.com/victorhaggqvist/xboomx), Python, X11
- [shellex](https://github.com/Merovius/shellex), C, X11
- [Kupfer](http://engla.github.io/kupfer/), Python, X11
- [lighthouse](https://github.com/emgram769/lighthouse), C, X11
- [xlauncher](https://github.com/vatriani/xlauncher), C, X11

### Vim menus

- [CtrlP](http://kien.github.io/ctrlp.vim/)
- [Unite](https://github.com/Shougo/unite.vim)
- [FuzzyFinder](http://www.vim.org/scripts/script.php?script_id=1984)
- [LeaderF](https://github.com/Yggdroot/LeaderF)
- [asyncfinder.vim](https://github.com/vim-scripts/asyncfinder.vim)
- [ku](http://www.vim.org/scripts/script.php?script_id=2337)
- [LustyExplorer](http://www.vim.org/scripts/script.php?script_id=1890)
- [LycosaExplorer](http://www.vim.org/scripts/script.php?script_id=3659)

## License and copyright

The project is released under the General Public License (GPL), version 3.

Copyright © 2015, Danil Semelenov.
