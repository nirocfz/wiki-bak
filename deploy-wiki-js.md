<!-- TITLE: Deploy Wiki Js -->
<!-- SUBTITLE: A quick summary of Deploy Wiki Js -->

记得把 github 上项目的 deploy key 对应的私钥添加到 ssh-agent
或者在 ~/.ssh/config 里配置好 github.com 对应的私钥文件，wiki.js 配置里的地址要做相应改动。

比如 `.ssh/config` 里做了这个配置

```
Host wiki
    Hostname github.com
    User git
    IdentityFile ~/github/id_rsa
```

wiki.js 的 `config.yml` 里 github 仓库地址就由

```
url: 'git@github.com:nirocfz/wiki.git'
```

改成


```
url: 'wiki:nirocfz/wiki.git'
```

其中 `wiki` 就是 `~/.ssh/config` 那段配置里第一行 `Host` 对应的值。