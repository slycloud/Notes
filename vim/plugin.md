# Plugins

## Markdown Preview

### Install Steps

add plugins to `.vimrc.bundles.local`

    Bundle 'plasticboy/vim-markdown'
    Bundle 'kannokanno/previm'
    Bundle 'tyru/open-browser.vim'

install plugins (execute in terminal command line)

    vim +BundleInstall! +BundleClean +q

### How To Use
To preview in browser

    :PrevimOpen

### Trouble Shooting

Error during installing plugins, caused by theme "solarized" cannot be used.

    Could not resolve airline theme "solarized". Themes have been migrated to github.com/vim-airline/vim-airline-themes.

Solution (Refer to https://github.com/spf13/spf13-vim/issues/880)

    Bundle 'vim-airline/vim-airline-themes'
