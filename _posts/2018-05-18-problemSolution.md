---
title: "Manjaro linux 的一些问题解决" 
tags: [linux]
---
- [分区](#)
- [软件安装与配置](#)
    - [matlab 中文乱码问题](#matlab)
    - [vim插件YouCompleteMe安装](#vimyoucompleteme)
    - [matplotlib 中文乱码问题](#matplotlib)
    - [TexStudio 中文乱码问题](#texstudio)
    - [fsl 安装问题](#fsl)
- [系统配置](#)
    - [Manjaro 中文输入法问题](#manjaro)
    - [终端vim模式](#vim)

很久不更新博客，不是因为这段时间没有学习，而是转战 **OneNote** 了。对于一些需要大量图片的笔记，在windows下还是富文本笔记比较方便。而对于需要大量代码的笔记我选用了 **jupyter-notebook**。所以以后在此处更新的主要为文字笔记。

前几天 linux 崩了，愤而重装。这里记录一下工作环境的一些配置与问题解决（以便下一次重装）。

> 目前我使用的操作系统为 *4.9.98-1-MANJARO* , 是 Arch 的一个发行版。

# 分区
由于上次系统分区时给软件分区的空间不够，这里我只有分三个区：(参考 Manjaro 安装手册)

| 分区   | 大小         | 文件系统格式 | 挂载点    | 标签       |
| ------ | ------------ | ------------ | --------- | ---------- |
| EFI    | 500MB        | fat32        | /boot/efi | boot,esp   |
| SWAP   | 8G           |              |           | linux swap |
| 主分区 | 其他所有空间 | ext4         | /         |            |

* 其中，SWAP分区的建议分配空间约等于自己的内存空间

# 软件安装与配置

## matlab 中文乱码问题
matlab 在安装后打开中文显示为方块，网上的解释为matlab 的 Java 环境未配置中文字体。

首先设置JAVA_HOME环境变量为matlab中自带的jre目录。

然后将中文字体复制到jre中的fonts目录中，在fontsdir文件中添加该字体，注意修改首行字体数目。


> [JAVA环境中文字体配置](linux-wiki.cn/wiki/配置JAVA程序字体)

## vim插件YouCompleteMe安装

该插件安装后会立刻shutdown，查看log文件为找不到 *libinfo.so.5*。

首先运行以下命令在指定目录列出含有libtinfo的库文件。

```bash
ls -l /usr/lib | grep libtinfo
```
结果为

```bash
-rw-r--r--   1 root root        24 1月  30 00:06 libtinfo.so

lrwxrwxrwx   1 root root        16 1月  30 00:06 libtinfo.so.6 -> libncursesw.so.6
```

这里我们不安装*libinfo.so.5*,以防出现其他依赖问题。建立软链接，新建*libinfo.so.5*文件，将其链接到*libinfo.so.6*。

```bash
sudo ln -sf /usr/lib/libncursesw.so.6 /usr/lib/libtinfo.so.5
```

## matplotlib 中文乱码问题

参考了网上修改 *matplotlibrc* 文件的方法，然而没用。最后采取笨办法，在需要的时候指定中文字体：

```python
plt.rcParams['font.sans-serif']=['SimHei']
plt.rcParams['axes.unicode_minus']=False #用来正常显示负号
```

## TexStudio 中文乱码问题

首先确保安装了 *tex-live* 中文字体包, 然后在设置中将默认编译器选为 *xelatex* ,编码选为 *UTF-8* 。最后在代码中添加 *ctex* 包。

这里还可以安装 **tllocalmgr**, 用来安装 latex 包。

使用方法为：

```
install package
texhex
```

## fsl 安装问题
fsl 是linux下的一个大脑图像处理库，这里我使用的是 Manjaro 的包管理工具 *yaourt* 安装的。
在安装时遇到空间不足安装失败的问题。

是因为 yaourt 的缓存目录默认为 /tmp ，而这个空间只有4G多，在安装较大的包时会空间不足。这里我们修改其缓存目录。

```bash
export TMPDIR=/opt/tmp       #永久修改
yaourt -tmp <dir> package          #暂时修改
```

另外提醒，假如使用本地 PKGBUILD 文件，则只会下载并打包，并不会自动安装到系统。

# 系统配置

## Manjaro 中文输入法问题

在 .xprofile  中添加:

```bash
#fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
export LC_ALL=zh_CN.UTF-8
export XIM=fcitx  
export XIM_PROGRAM=fcitx
export QT_IM_MODULE=fcitx
eval `dbus-launch --sh-syntax --exit-with-session`
exec fcitx &

```

网上的教程大多只有前三句，事实证明还需要后面的。

## 终端vim模式
我们的终端默认为eamcs模式，假如需要切换到vim模式，在fish中为在  .config/fish/config.d/omf.fish  中添加：

```
fish_vi_mode
```
