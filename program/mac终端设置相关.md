# 终端准备（Mac OS 14.0）

可打开终端>双击>显示检查器>切换主题

```sh
#首次打开git/python3需要安装xcode
xcode-select --install#会弹窗,按提示安装
```

安装homebrew（[设置相关参考链接](https://mirrors4.tuna.tsinghua.edu.cn/help/homebrew/)）

```sh
#1.可按照官网提示下载pkg或者通过命令远程安装
#2.设置环境变量
echo export PATH=/opt/homebrew/bin:$PATH >> ~/.zshrc
#3.环境变量生效
source ~/.zshrc
#4.设置代理
echo export HOMEBREW_API_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/api" >> ~/.zshrc
echo export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git" >> ~/.zshrc
echo export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles" >> ~/.zshrc
source ~/.zshrc
```

# 安装软件

## anaconda

```sh
brew install anaconda
echo export PATH="/opt/homebrew/anaconda3/bin:$PATH" >> ~/.zshrc
source ~/.zshrc
```

设置源参考[jupyter和conda设置相关](./jupyter和conda设置相关.md)

## tensorflow-gpu

[参考链接](https://developer.apple.com/metal/tensorflow-plugin/)

```sh
pip install tensorflow
python -m pip install tensorflow-metal
```

## python3.7

Mac m1默认系统不支持3.8以下python环境，需要用rosetta转换：

1. 重新安装brew，然后安装python3.7[参考链接](https://stackoverflow.com/questions/73074173/how-to-install-tensorflow-1-x-on-m1-chip)
2. 通过conda安装

```sh
## 创建一个空的环境
conda create -n py37
 
## 启动该环境
conda activate py37
 
## 使用x86_64 architecture channel(s)
conda config --env --set subdir osx-64
 
## 安装python3.7之类的操作  install python, numpy, etc. (add more packages here...)
conda install python=3.7
```

 # 设置相关

## 文件夹设置

```shell
#显示隐藏的文件夹
sudo chflags nohidden /opt
#隐藏文件夹
sudo chflags hidden opt
```

# idea

## 配置

- 主题：One Dark
- 字体：Inconsolata
- Size：14
- Line Height：1.2
