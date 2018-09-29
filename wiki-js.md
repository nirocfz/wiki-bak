<!-- TITLE: Wiki.js -->
<!-- SUBTITLE: Notes of Wiki Js -->

当前的 1.0 版本不支持中文搜索，2.0 版本将会支持，计划在 2018 年第四季度发布。

## 部署

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