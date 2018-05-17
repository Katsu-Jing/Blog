---

title: set-mac
date: 2017-05-12 15:43:58

---

# 1. 调整触摸板设置
使用mac的你根本不需要使用鼠标，只要善用mac强大的多点触摸将使你事半功倍。

系统偏好设置>触控板，可以很方便的了解到所有多点触控手势。
1. 光标与点按：强烈勾上“轻点”和“辅助点按”，点击毫不费力；
2. 滚动缩放：推荐全部勾选；
3. 更多手势：强烈推荐勾上“页面间轻扫”和“全屏幕显示应用之间轻扫”，前者便于应用内的前进后退，后者便于在沉浸模式下切换应用；

另外强烈建议开启三指拖移，
系统偏好设置>辅助功能>鼠标与触控板>触控板选项，勾选启用拖移，选择“三指拖移”

程序编写需要使用到很多功能键，如果每次都要Fn+Fx就比较麻烦，强烈建议把功能键设置成标准功能键，方法：系统偏好设置>键盘，勾选`将F1、F2等键用作标准功能键`

# 2. 软件包管理工具
主流是macport和homebrew两种方案，推荐homebrew，方便的安装各类开源程序和软件包。地址：https://brew.sh/

推荐安装的程序：
1. proxychains4，方便在执行各种各样需要连接网络的命令时加上代理。
2. python3
2. zsh，更聪明的shell
3. maven，注意安装maven3
4. node，vscode依赖大量npm开源工具，同时npm本身也提供很多实用程序，如果有兴趣你也可以学习一下nodejs开发，建议安装
5. git，系统自带git，通过brew获取最新版的git

常用命令：
1. 安装homebrew：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

```

2. 安装软件： brew install xxx

3. 查找软件：brew search xxx
4. 更新：brew update


