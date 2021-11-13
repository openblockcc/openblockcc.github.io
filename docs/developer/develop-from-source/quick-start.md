!!! warning
    注意，该栏资料仅适用于需要对软件核心功能进行开发与调整的社区开发者与第三方开发者。过程涉及开发环境搭建，源代码编译等复杂操作，没有对软件底层核心功能修改的需求无需参考此资料。

## 开发环境

请确保你的计算机已经安装好了以下软件或工具。

- Node.js > 8 (推荐使用版本14，过高的Node版本在某些情况下也会导致编译失败) 
- python2.7
- git
- Java
- 根据 [node-gyp官方说明](https://www.npmjs.com/package/node-gyp) 安装必要的本地编译工具

!!! Warning
    如果你是在中国大陆地区，你需要购买一个科学上网的代理才能够成功执行下面的操作内容。由于科学上网的代理软件只会在特定端口监听和转发数据，所以我们推荐开发者同时下载安装本地代理软件: `proxifier`，以此来将计算机上运行程序的网络流量转移到代理软件的监听端口。在 proxifier 中完成 Proxy Servers 的配置，并在 Proxification Rules 中添加设置科学上网的代理软件的连接方式为直连（不设置这个会形成网络回环），而后将默认连接改为由代理转发，这样计算机上大部分程序的网络连接都会通过 proxifier 转发至科学上网的代理端口，从而能够访问开发时所需要下载的资源。

    在没有成功配置网络代理前请不要尝试开发！

    在没有成功配置网络代理前请不要尝试开发！

    在没有成功配置网络代理前请不要尝试开发！

    重要的事情说三遍。使用 cnpm 或是国内镜像地址也没用，你一定会卡死在某些资源的安装步骤中。

## 克隆&安装

1. 克隆所有工程核心仓库。 (使用```--depth``` 指令来避免下载全部的仓库历史内容，否则GUI的大小将会超过1.18GB)

    ```bash
    mkdir openblock
    cd openblock
    git clone --depth 3 https://github.com/openblockcc/openblock-gui
    git clone https://github.com/openblockcc/openblock-link
    git clone https://github.com/openblockcc/openblock-resource
    ```
    
2. 安装

    - 使用 `install` 指令下载所有必要的npm包，并运行 fetch 指令下载所有远程资源
    
    ```bash
    cd openblock-gui
    npm install
    cd ..
    cd openblock-link
    npm install
    npm run fetch
    cd ..
    cd openblock-resource
    npm install
    npm run fetch
    cd ..
    ```
    

## 运行

1. 启动硬件接口服务器 openblock-link

    ```bash
    cd openblock-link
    npm start
    ```

2. 启动外部插件与设备服务器 openblock-resource

    在新的一个终端中执行。

    ```bash
    cd openblock-resource
    npm start
    ```

3. 启动 openblock-gui

    在新的一个终端中执行。

    ```bash
    cd openblock-gui
    npm run start-open
    ```

4. 在 openblock-gui 的 webpack 构建完成后，将会有弹出 openblock-gui 的网页。当然你也可以通过手动输入地址 `http://127.0.0.1:8601/` 来访问。

## 构建完整的开发环境

在前面的步骤中我们已经成功编译启动了 openblock 软件，但是在过程中我们使用的软件包均为社区在 npm 上发布的软件包，若是我们想要修改其中的源代码，我们仍需要构建一个更完整的软件开发环境。下面就来介绍一些你在构建自己版本的软件过程中需要了解的内容。

### 获取全部的核心软件包

1. 克隆 openblock-blocks 与 openblock-vm，关于这些软件包各自的作用，你可以阅读[**构架介绍**](framework-introduction.md)来了解。

    ```bash
    git clone --depth 3 https://github.com/openblockcc/openblock-blocks
    git clone --depth 3 https://github.com/openblockcc/openblock-vm
    ```

2. 安装和链接

    `link` 指令会以本地npm包替换项目内默认的包，这样你在 openblock-blocks 等包中的修改才会被 openblock-gui 使用，否则它默认使用的将会是从npm服务器下载的内容，你可以查看 openblock-gui/node_modules 下的 openblock-blocks 与 openblock-vm 的文件夹，如果 link 成功，那么他们将被链接到本地克隆下来的工程的位置。

    ```
    cd openblock-blocks
    npm install
    npm link
    cd ..
    cd openblock-vm
    npm install
    npm link
    cd ..
    cd openblock-gui
    npm install
    npm link openblock-blocks openblock-vm
    cd ..
    ```

!!! Tip
    在 openblock-gui 工程下使用 `npm start`或 `npm run start-open` 启动 webpack 服务器后，webpack 会持续监听工程内的源代码变动，包括 link 的软件包内的代码，也就意味着你在修改 openblock-gui 或是 openblock-vm 中的代码后只需要保存文件，就会触发 webpack 重新编译与更新页面，这个过程的耗时要比 webpack 重启编译短很多，调试代码时很方便。

!!! attention
    对于 openblock-blocks 来说，由于其编译模式的特殊 (使用 google-closure-compiler 编译)，在修改其内部代码后我们不仅要在其路径下执行 `npm run prepublish` 重新编译代码，还需要重启 gui 的 webpack 服务器才能使变动生效。

### 二次开发过程

在上一步骤中，我们链接了全部的核心软件包，大部分需要二次修改的代码都包含在里面了。实际开发时我们可以根据二次修改的需要取消加减这些包，根据实际需要只 link 需要修改的软件包内容即可。一般来说，推荐大家使用一个独立的文件夹来作为 OpenBlock 的工作文件夹，如下所示：

![workspace](asset\workspace.png)

在日常的工作中中我们都是使用这套环境和步骤来进行功能开发与调试的，启动 gui，link 与 extension 三个服务器，而后修改代码。在最终确认完成后，再转到 desktop 工程下进行打包发布。

## 了解更多开发内容

1. [构架介绍](framework-introduction.md)
2. [添加新控制板](add-a-new-board.md)
3. [打包桌面版](pack-desktop-software.md)
4. [本土化](localization.md)