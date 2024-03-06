---
nav:
  title: 笔记
  # order: 0
# group:
#   title: 介绍
#   order: 0
title: 搭建自己的文档站
---

# 搭建自己的文档站

用 dumi 和 github pages 来搭建一个文档型个人网站。

## dumi

一个用来搭建静态站点的框架，风格可以看看[官网](https://d.umijs.org)。

dumi 适合用来搭建文档站，也适合用来搭建 React 组件库。

## 准备

### Node.js

安装 [Node.js](https://nodejs.org/en/download) 并确保版本大于14，推荐使用 [nvm](https://github.com/coreybutler/nvm-windows) 来管理 Node.js 版本。

```bash
$ node -v
```

使用这个指令来检查 Node.js 版本

### 包管理工具

如果不想用 Node.js 自带的 npm ，建议提前先准备好。

***注：这个文档里我会使用 yarn 来演示***

```bash
$ npm instal -g yarn # 安装yarn
# 或
$ npm instal -g pnpm # pnpm
```

### 编辑器/IDE

随便找一个自己喜欢的编辑器/IDE，例如 [VS Code](https://code.visualstudio.com) 。

## 开始搭建

### 初始化

```bash
# 先找个地方建个空目录。
$ mkdir myapp && cd myapp

# 通过官方工具创建项目，选择你需要的模板
$ npx create-dumi

# 选择一个模板
$ ? Pick template type › - Use arrow-keys. Return to submit.
$ ❯   Static Site # 用于构建网站
$     React Library # 用于构建组件库，有组件例子
$     Theme Package # 主题包开发脚手架，用于开发主题包
```

只是用来做文档站的话可以选第一个，接下来根据提示一步步做下去就好了。

如果在安装依赖的时候出现了网络错误，可以尝试切换到国内的镜像源再重新`install`：

```bash
# 切换镜像源
$ yarn config set registry https://registry.npmmirror.com/

# 安装依赖
$ yarn
```

启动：

```bash
$ yarn start
```

打开地址出现下面的界面就算成功了。

![示例](/images/2024-03-06-1.png)

剩下的，参考 dumi 的文档搭建自己喜欢的站点吧！

## 部署到 Github Pages

github 提供了免费的静态站点托管服务，部署到 Github Pages 是一个很好的选择。

### 准备一个仓库并部署

1. 登录 github 。
2. 右上角选择`Your repositories`。

![示例](/images/2024-03-06-2.png)

3. 点击`New`

![示例](/images/2024-03-06-3.png)

4. 输入仓库名称，格式为`{用户名}.github.io`，选择`Public`公开仓库，最后点击`Create repository`按钮创建仓库。**（我这里是因为已经创建过了，所以会有红字警告）**

![示例](/images/2024-03-06-4.png)

5. 按照提示将本地的项目提交到仓库。
6. 创建一个名为`gh-pages`分支。**（名字可以随便取，用来存放构建后的产物）**
7. 本地打包项目：

```bash
$ yarn build
```

8. 将打包好的产物（默认是 dist ）提交到刚才创建的`gh-pages`分支上去，注意是将 dist 文件夹内的东西提交上去而不是整个文件夹。
9. 一切都准备好了之后，进入仓库页面，点击`Settings`。

![示例](/images/2024-03-06-5.png)

10. 选择`Pages`, Source 处选择`Deploy from a branch`， Branch 处选择刚刚创建好的分支（`/ (root)`表示根目录），然后点一下旁边的`save`按钮。稍等一会，上面出现的网址就是你的站点地址了，后续`gh-pages`分支上的每一次提交都会自动触发网站更新。

![示例](/images/2024-03-06-6.png)

### 自动化

上面我们通过手动的方式完成了部署，缺点是每次更新都要手动打包然后把产物放到`gh-pages`分支上去，接下来把这一过程自动化，这一过程可以直接替换掉上面的第7步和第8步。

切换到你的主分支，创建`.github/workflows/ci.yml`文件，以下是一个示例。**（建议稍微理解一下）**

```yaml
name: Build and Deploy
on:
  push:
    branches:
      - main # main分支提交时触发
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

        # 打包代码，dumi的打包命令
      - name: Install and Build 🔧
        run: |
          npm install
          npm run build

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@4.1.5
        with:
          branch: gh-pages # 部署到的目标分支
          folder: dist # dumi打包后的产物文件夹
```

这样，我们每次提交完代码都能自动完成部署和站点更新了。
