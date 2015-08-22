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

## Usage

Pick some file from current directory:

`find -maxdepth 3 -type f ! -path "./.git/*" ! -path "./.svn/*" -printf '%P\n' | LC_COLLATE=C sort | pmenu`
