# 目录

[TOC]

## 安装zsh

```bash
# 安装zsh
yum install zsh
```

```bash
# 设置默认shell为zsh
chsh -s $(which zsh)
# 切换为bash
chsh -s /bin/bash
```

```bash
# 检查设置是否成功
echo $SHELL
```

```bash
# 检查是否安装了zsh(安装oh-my-zsh之前必须安装zsh)
zsh --version
```

```bash
# 安装oh-my-zsh(wget)
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

```bash
vim ~/.zshrc
```

## 设置主题

```bash
# 默认主题
ZSH_THEME="robbyrussel"
# mira agnoster duellj(推荐的主题)
# 设置随机主题
ZSH_THEME="random"
# oh-my-zsh默认自带的主题放置的位置
vim ~/.oh-my-zsh/themes
```

## 安装插件

```bash
# 安装语法高亮插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

```bash
# 安装自动补全插件
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

```bash
# 在~/.zshrc中配置插件
plugins=(git wd web-search history history-substring-search zsh-syntax-highlighting zsh-autosuggestions)
```

```bash
# 重新加载配置即可
source ~/.zshrc
```

