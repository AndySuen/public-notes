[Oh My Zsh](https://github.com/AndySuen/public-notes/issues/3)

# Oh My Zsh

## Z shell 

[zsh.org](https://www.zsh.org/)

> Z shell（Zsh）是一款可用作交互式登录的shell及脚本编写的命令解释器。Zsh对Bourne shell做出了大量改进，同时加入了Bash、ksh及tcsh的某些功能。
>
> 自2019年起，macOS的默认Shell已从Bash改为Zsh。 （from [wikipedia](https://zh.wikipedia.org/wiki/Z_shell)）

Zsh 相比 Bash 提供了一些实用的功能，如命令补全、文件名提示、多行编辑、模块扩展等，对日常开发更加友好。

因为 Zsh 强大的扩展性，管理 Zsh 的配置和插件对于初学者来说是件困难的事。因此，有了`Oh My Zsh`。

## Oh My Zsh

[ohmyz.sh](https://ohmyz.sh/)

> Oh My Zsh is a delightful, open source, community-driven framework for managing your Zsh configuration. It comes bundled with thousands of helpful functions, helpers, plugins, themes, and a few things that make you shout...
> 
> "Oh My ZSH!"

`Oh My Zsh`提供了管理 Zsh 的配置和插件的功能，并且并预先内置了一些很棒的配置、插件和主题，开箱即用。

我目前在用的一些插件：
```sh
plugins=(git extract z zsh-autosuggestions zsh-osx-autoproxy)
```

其中推荐大家使用的是：
- `git`: 在命令提示符区域显示当前 git 状态，并提供了几十个实用的 alias
- `z`: 快速跳转目录
- `zsh-autosuggestions`: 历史命令提示补全

<img width="696" alt="image" src="https://user-images.githubusercontent.com/9132641/128998528-04a18cac-7706-483f-b293-e7572b2dc304.png">

（主题：[powerlevel10k](https://github.com/romkatv/powerlevel10k)）

下面推荐几个常用的 `git alias`:

|  alias | command             |
|-------:|---------------------|
|  `gss` | `git status -s`     |
|  `gcb` | `git checkout -b`   |
|  `gco` | `git checkout`      |
|   `gd` | `git diff`          |
|   `ga` | `git add`           |
|   `gc` | `git commit`        |
|   `gl` | `git pull`          |
|   `gp` | `git push`          |
|   `gm` | `git merge`         |
| `grbm` | `git rabase master` |

完整的alias列表请看官方wiki [oh-my-zsh cheatsheet](https://github.com/ohmyzsh/ohmyzsh/wiki/Cheatsheet)

善用上面的10个alias可以节约80%使用git的时间。

---

附：最近一段时间我常用的一些 git alias 有关的命令

```sh
~ ❯ awk -F ';' '{print $2}' .zsh_history | egrep '^g' | sort | uniq -c | sort -nr | head -30
 346 gss
 180 gco -
 168 ga .
 163 gp
 161 gp -f
 149 glgg
 133 gco master
 129 gc --amend
 113 gd
  86 gl
  70 grbm -i
  58 gl --prune
  53 glg
  52 gcam 'fix'
  51 gd --name-only master | grep .py | xargs black
  33 gd master --name-only | grep .py | xargs black
  31 gm --no-edit --no-ff feature/xxx
  27 gd --name-only | grep .py | xargs black
  20 gm --no-edit --no-ff feature/xxx
  20 gd master
  17 gm --no-edit --no-ff feature/xxx
  13 gcam 'update'
  12 gm --continue
   9 gp -u origin fix/xxx
   9 gd @^
   9 g rebase --continue
   8 gstl
   8 gm --no-edit --no-ff feature/xxx
   8 glgp
   8 gd --cached
```

---

(完)
