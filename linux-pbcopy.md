<!-- TITLE: Linux 里的 pbcopy/pbpaste -->
<!-- SUBTITLE: pbcopy/pbpaste 是 Mac 系统带的剪贴板命令，在 Linux 里可以用 xclip 达到同样的效果 -->

安装 xclip 之后，在 `$HOME/.zshrc` 里添加这两个 `alias` 命令：

```
alias pbcopy='xclip -selection clipboard'
alias pbpaste='xclip -selection clipboard -o'
```