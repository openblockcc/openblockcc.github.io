## 打包社区官方版本

1. 首先 clone 离线版工程

    ```bash
    git clone https://github.com/openblockcc/openblock-desktop
    cd openblock-desktop
    ```

2. 安装依赖包

    ```bash
    npm i
    ```

3. 打包

    ```bash
    npm run dist
    ```

5. 等待 electron 运行完毕后即可在工程文件夹下的 dist 文件夹中看到打包完毕的软件了

## 打包自己的版本

打包我们在本地基础开发环境中修改后的工程。

1. 与打包社区官方版本的步骤相同，首先 clone 离线版工程，并安装依赖包。

    ```bash
    git clone https://github.com/openblockcc/openblock-desktop
    cd openblock-desktop
    npm i
    ```

2. link 本地的 openblock-gui

    注意在前面的教程中我们使用的是 openblock-gui 默认的 develop 分支，在打包离线版时我们需要切换至 desktop 分支下，如果在此之前你已经在 develop 分支下变动了代码，不要担心，使用 `git commit` 指令提交这些变更，并在切换到 desktop 分支后使用 merg 指令合并即可。

    ```bash
    cd ..
    cd openblock-gui
    git checkout desktop
    npm link openblock-blocks openblock-vm
    npm link
    ```

3. 在 openblock-desktop 中 link gui

    ```bash
    cd ..
    cd openblock-desktop
    npm link openblock-gui
    ```

    同样的如果你还在本地修改的其他软件包的内容，也按照这样的方式链接即可。

4. 打包

    ```bash
    npm run dist
    ```

## 调试离线版工程

- 开启控制台

    软件在调试与分发版本中均为限制控制台的开启权限，使用快捷键即可开启。

    Windows/Linux：Control+Shift+i

    MacOS：Command+Option+i

- 监听调试模式

    如果你想要调试desktop中的代码，则按照以下步骤操作。该操作会开启离线版软件监听调试模式，任何对desktop源代码的修改都将触发对其的重新编译。

    ```bash
    cd openblock-desktop
    npm run compile
    npm run start
    ```
