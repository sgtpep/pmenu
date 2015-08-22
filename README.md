# pmenu
Dynamic menu like dmenu written in Python without dependencies with optional MRU list.

## Alternatives
- [dmenu](http://tools.suckless.org/dmenu/), C
- [slmenu](https://bitbucket.org/rafaelgg/slmenu), C
- [percol](https://github.com/mooz/percol), Python
- [fzf](https://github.com/junegunn/fzf), Go
- [Pick](https://github.com/thoughtbot/pick), C
- [Selecta](https://github.com/garybernhardt/selecta), Ruby
- [PathPicker](https://facebook.github.io/PathPicker/), Python
- [happyfinder](https://github.com/hugows/hf), Go
- [Heatseeker](https://github.com/rschmitt/heatseeker), Rust
- [CtrlP](http://kien.github.io/ctrlp.vim/), VIM
- [Unite](https://github.com/Shougo/unite.vim), VIM

## Usage

Pick some file from current directory:

    find -maxdepth 3 -type f ! -path "./.git/*" ! -path "./.svn/*" -printf '%P\n' | LC_COLLATE=C sort | pmenu

Pick some file from current directory for editing from VIM by Ctrl-P (CtrlP plugin replacement):

    function! Pmenu()
      let mru_name = fnamemodify(getcwd(), ':t')
      if isdirectory(".git")
        let filelist_command = "git ls-files"
      else
        let filelist_command = "find -maxdepth 3 -type f ! -path '*/.git/*' ! -path '*/.svn/*' -printf '%P\n' | LC_COLLATE=C sort"
      endif
      try
        let selected_paths = split(system(filelist_command . " | pmenu -n " . shellescape(mru_name, 1)), '\n')
      catch /Vim:Interrupt/
        redraw!
        return
      endtry
      if !empty(selected_paths)
        execute ":edit " . selected_paths[0]
      endif
      redraw!
    endfunction
    nnoremap <silent> <C-P> :call Pmenu()<CR>
    vnoremap <silent> <C-P> :call Pmenu()<CR>