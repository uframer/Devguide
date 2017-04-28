# Mac开发环境

第一部是从Mac App Store安装Xcode。安装完Xcode后，打开终端并输入如下命令安装命令行工具：

<div class="host-code"></div>

```bash
xcode-select --install
```

## 安装Homebrew

我们推荐在Mac OS X使用使用[Homebrew包管理器](http://mxcl.github.com/homebrew/)。Homebrew的安装相当简单：[安装指南](http://mxcl.github.com/homebrew/)。

在安装Homebrew后，将下面的命令复制到你的shell：

```sh
brew tap PX4/homebrew-px4
brew tap PX4/simulation
brew update
brew install git bash-completion genromfs kconfig-frontends gcc-arm-none-eabi
brew install astyle cmake ninja
# simulation tools
brew install ant graphviz sdformat3 eigen protobuf
brew install homebrew/science/opencv
```

接下来安装所需的python包：

```sh
sudo easy_install pip
sudo pip install pyserial empy pandas jinja2
```

### Java for jMAVSim

If you're intending to use jMAVSim, you need to install [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

## Snapdragon Flight

使用Snapdragon Flight的开发者需要暂时使用一个Ubuntu虚拟机并遵循Linux部分的文档操作。Qualcomm只为Ubuntu提供了可靠的工具。PX4开发团队在VMWare上使用的体验最好，尤其是涉及到USB稳定性的时候。

## 模拟

OS X已经预装了CLANG，无需单独安装。

## 编辑器/IDE

最后，下载并安装Qt Creator：[下载](http://www.qt.io/download-open-source/#section-6)。

接下来继续安装[第一次构建](../setup/building_px4.md)的指南操作。
