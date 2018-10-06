<!-- TITLE: Linux -->

# Tips and Tricks

## pbcopy/pbpaste

安装 xclip 之后，在 `$HOME/.zshrc` 里添加这两个 `alias` 命令：

```
alias pbcopy='xclip -selection clipboard'
alias pbpaste='xclip -selection clipboard -o'
```

## DNS flush

[How can I flush the DNS on Ubuntu 17.04?](https://askubuntu.com/questions/906476/how-can-i-flush-the-dns-on-ubuntu-17-04)


```
sudo systemd-resolve --flush-caches
```

Ubuntu 18.04 同样适用。

```
sudo systemd-resolve --statistics
```

这条命令可以查看 DNS 解析的统计信息。

至少有两种情况，可能会需要刷新系统 DNS 缓存：

1. 刚刚修改了自己某个域名的某条记录，希望在本机立刻生效
2. 配置好了翻墙工具，但是系统 DNS 缓存里某些域名的 IP 是被污染的错误结果