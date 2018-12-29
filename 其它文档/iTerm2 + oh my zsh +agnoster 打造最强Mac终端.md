- [安装iTerm2](http://www.siguoya.name/pc/home/article/256#%E5%AE%89%E8%A3%85iTerm2)
- [安装Oh My Bash](http://www.siguoya.name/pc/home/article/256#%E5%AE%89%E8%A3%85Oh%20My%20Bash)
- 配置agnoster主题

![ITerm2+Oh My Zsh＋Solarized＋Meslo](http://statics.siguoya.name/img/pc/home/article/content/20160614/1465911437687474703a2f2f6f6936332e74696e797069632e636f6d2f6970637561782e6a7067.png)

## 安装iTerm2

iTerm2官方下载地址 <http://www.iterm2.com/downloads.html>

## 安装Oh My Bash

1.通过`cat /etc/shells`命令可以查看当前系统可以使用哪些shell；

```bash
# List of acceptable shells for chpass(1).# Ftpd will not allow users to connect who are not using# one of these shells./bin/bash/bin/csh/bin/ksh/bin/sh/bin/tcsh/bin/zsh
```

2.通过`echo $SHELL`命令可以查看我们当前正在使用的shell；

```bash
# Mac系统中默认的shell为bash shell/bin/bash
```

3.如果当前的shell不是zsh，我们可以通过`chsh -s /bin/zsh`命令可以将shell切换为shell之zsh，终端重启之后即可生效。

4.将shell切换为zsh之后，我们就可以安装Oh My ZSH了
官方推荐的安装方法为：

```bash
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

记住，爱翻墙的童鞋记得先关闭代理哦，不然没法下载成功的
出线以下提示，便是安装成功了^_^

![Oh My ZSH](http://statics.siguoya.name/img/pc/home/article/content/20160614/1465909863Screen%20Shot%202016-06-14%20at%208.08.14%20PM.png)

## 配置agnoster主题

Oh My Zsh提供的所有主题在线预览：
<https://github.com/robbyrussell/oh-my-zsh/wiki/Themes>

安装成功之后，我们可以通过`vi ~/.zshrc`，设置`ZSH_THEME="agnoster"`对主题进行修改。

注意，agnoster主题能否设置成功，还依赖于以下东西：

- [Solarized Dark配色方案](http://ethanschoonover.com/solarized)
  下载完成之后解压，在iTerm2的Preferences——Profiles——colors——Load Presets——Solarized Dark即可设置终端配色
- [特殊字体安装](https://github.com/powerline/fonts)
  下载完成之后解压，执行其中的install.sh文件，在iTerm2的Preferences——Profiles——Text中同时将Regular Font和Non—ASCII Font设置为Meslo LG M DZ Regular for Powerline即可