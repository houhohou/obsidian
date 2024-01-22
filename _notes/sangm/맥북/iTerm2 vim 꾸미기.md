


https://www.slant.co/topics/480/~best-vim-color-schemes
여기들어가 괜찮은 거 클론해온다
```bash
git clone <address>
```

디렉터리를 만들고 클론한 폴더안에 colors를 .vim안으로 옮긴다. (아래는 gruvbox 라는 테마를 클론한거임)
```bash
mkdir -p ~/.vim
mv ./gruvbox/colors ~/.vim/
```

~/.vimrc안에 아래와 같이 내용을 적는다 ( 중간에 클론한 테마의 이름을 넣는다)
```bash
" Syntax Highlighting
if has("syntax")
    syntax on
endif

set autoindent
set cindent
set nu

colo gruvbox    #클론한 테마의 이름

set laststatus=2
set statusline=\ %<%l:%v\ [%P]%=%a\ %h%m%r\ %F\
```
