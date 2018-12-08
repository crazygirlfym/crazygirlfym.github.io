---
layout: post
title: 无root权限安装zsh
date: 2018-12-08 10:21:24.000000000 +09:00
tags: note, zsh
---

在很多情况下，我们安装是拿不到root权限的，比如公司的开发机，或者学校服务器的某一个账号。更糟糕的是，可能这台机器还是不允许连接
外网的。本文主要是记录下在zsh安装过程中踩的坑。

### step 1：安装依赖nurses

执行以下操作就可以了

```
# Download the ncurses gzipped tarball
wget ftp://invisible-island.net/ncurses/ncurses.tar.gz

# Extract gzipped tarball
tar -zxvf ncurses.tar.gz

# Move into root ncurses source directory
cd ncurses

# Set cflags and c++ flags to compile with Position Independent Code enabled
export CXXFLAGS=' -fPIC'
export CFLAGS=' -fPIC'

# Produce Makefile and config.h via config.status
./configure --prefix=$HOME/.local --enable-shared

# Compile
make

# Deduce environment information and build private terminfo tree
cd progs
./capconvert
cd ..
```

以上操作执行完，需要测试是否安装成功
```
./test/ncurses
```
如果界面上出现一些options，则说明编译没有问题，然后安装
```
make install
```

### step 2: 告诉zsh依赖的位置
```
INSTALL_PATH='$HOME/.local'
export PATH=$INSTALL_PATH/bin:$PATH
export LD_LIBRARY_PATH=$INSTALL_PATH/lib:$LD_LIBRARY_PATH
export CFLAGS=-I$INSTALL_PATH/include
export CPPFLAGS="-I$INSTALL_PATH/include" LDFLAGS="-L$INSTALL_PATH/lib"
```

此处可能有个坑，后面错误可能来源于此处。因为用了环境变量$HOME在命令行直接敲的时候，最好check下LD_LIBRARY的地址是不是对的，
如果不对，就不要用INSTALL_PATH这个变量，直接写绝对路径。



### step3: 下载并编译zsh

```
# Clone zsh repository from git
git clone git://github.com/zsh-users/zsh.git

# Move into root zsh source directory
cd zsh

# Produce config.h.in, needed to produce config.status from ./configure
autoheader

# Produce the configure file from aclocal.m4 and configure.ac
autoconf

# Give autotools a timestamp for recompilation
date < stamp-h.in

# Produce Makefile and config.h via config.status
./configure --prefix=$HOME/.local --enable-shared

# Compile
make

# Install
make install
```

此处遇到一个错误"You may
need to install a package called 'curses-devel' or 'ncurses-devel' on your
system."

这个错误产生的原因，可能是因为依赖没安装好，另一个原因就是前面step 2说到的路径问题。

### step 4: oh-my-zsh
oh-my-zsh 是一个社区驱动用于管理zsh的框架，囊括了140多个主题，200多个可选插件（如rails, git, OSX, hub, brew, ant, php, python etc.）。

```
curl -L http://install.ohmyz.sh | sh

git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh

# Copy template file into home directory
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

把默认的shell设置为chsh -s $HOME/.local/bin/zsh

此处可能会出现一个错误  "chsh: .local/bin/zsh is an invalid shell"
在服务器上无root权限，而且chsh也被管理员锁定，无权限无法使用（错误提示：You may not change the shell for …）。即使每次登入都执行bash命令，配合tmux时真的不胜其烦。

方法是修改.profile,在其中加入

```
export SHELL=/user/bash
exec /user/bash -l
```

### 关于插件



其实默认oh my zsh(以下简称zsh)已经在安装的时候就帮我们下载好了所有插件,只不过需要用户自己选择启用哪个.

根据官网的说明,需要做的很少,下面就来实际操作一下吧.

* 编辑 vim ~/.zshrc

* 添加一行: plugins=(laravel5)  ps: (添加多个空格隔开即可)

* 重启 source ~/.zshrc
这样zsh就会启用larvel5的这个插件,非常简单吧:)


### 参考
[Compiling zsh without root](https://drewsilcock.co.uk/compiling-zsh)

[打造高效个性Terminal（二）之 zsh](http://huang-jerryc.com/2016/08/11/%E6%89%93%E9%80%A0%E9%AB%98%E6%95%88%E4%B8%AA%E6%80%A7Terminal%EF%BC%88%E4%BA%8C%EF%BC%89%E4%B9%8B%20zsh/)

