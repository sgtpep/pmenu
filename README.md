# pmenu
Dynamic menu like dmenu written in Python without dependencies with optional sorting by usage.

## Usage

    usage: pipe menu items to stdin or pass them with as positional arguments

    positional arguments:
      item                  menu item text

    optional arguments:
      -h, --help            show this help message and exit
      -n NAME, --name NAME  name of the usage cache
      -p PROMPT, --prompt PROMPT
                            prompt text

Display some arbitrary menu items:

    echo -e "foo\nbar\nbaz" | pmenu
    pmenu foo bar baz
    echo -e "foo\nbar" | pmenu baz qux

Pick some file from current directory:

    find -maxdepth 3 -type f ! -path "./.git/*" ! -path "./.svn/*" -printf '%P\n' | LC_COLLATE=C sort | pmenu

Pick some file from current directory for editing from VIM by Ctrl-P (ala CtrlP):

    function! Pmenu()
      let mru_name = fnamemodify(getcwd(), ":t")
      if isdirectory("./.git")
        let filelist_command = "git ls-files"
        if !empty(expand('%'))
          let filelist_command .= " | grep -vF " . shellescape(expand("%:."), 1)
        endif
      else
        let filelist_command = "find -maxdepth 3 -type f " . shellescape("./" . expand("%:."), 1) . " ! -path '*/.git/*' ! -path '*/.svn/*' -printf '%P\n' | LC_COLLATE=C sort"
      endif
      let selected_paths = split(system(filelist_command . " | pmenu -n " . shellescape(mru_name, 1)), '\n') . " -p " . shellescape(mru_name . "> ", 1)
      if !empty(selected_paths)
        execute ":edit " . selected_paths[0]
      endif
      redraw!
    endfunction
    nnoremap <silent> <Leader>o :call Pmenu()<CR>
    vnoremap <silent> <Leader>o :call Pmenu()<CR>

## pmenu-run

The script `pmenu-run` is an example of application launcher built with `pmenu` similar to `dmenu_run` and `gmrun`. It scans for the aplication commands in \*.desktop files, detects if they are intended for terminal and automatically run them attached or detached to the it.

## Alternatives
- [dmenu](http://tools.suckless.org/dmenu/), C/X11
- [rofi](https://github.com/DaveDavenport/rofi) C/X11
- [slmenu](https://bitbucket.org/rafaelgg/slmenu), C/terminal
- [percol](https://github.com/mooz/percol), Python/terminal
- [fzf](https://github.com/junegunn/fzf), Go/terminal
- [Pick](https://github.com/thoughtbot/pick), C/terminal
- [Selecta](https://github.com/garybernhardt/selecta), Ruby/terminal
- [PathPicker](https://facebook.github.io/PathPicker/), Python/terminal
- [happyfinder](https://github.com/hugows/hf), Go/terminal
- [Heatseeker](https://github.com/rschmitt/heatseeker), Rust/terminal
- [CtrlP](http://kien.github.io/ctrlp.vim/), Vimscript/Vim
- [Unite](https://github.com/Shougo/unite.vim), Vimscript/Vim
