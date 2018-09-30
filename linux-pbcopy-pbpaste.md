<!-- TITLE: Linux -->

## pbcopy/pbpaste

安装 xclip 之后，在 `$HOME/.zshrc` 里添加这两个 `alias` 命令：

```
alias pbcopy='xclip -selection clipboard'
alias pbpaste='xclip -selection clipboard -o'
```