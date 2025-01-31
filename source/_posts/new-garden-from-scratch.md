---
title: 从零开始建立花园
date: 2024-05-26 21:58:34
#updated: 2023-03-26 16:16:34
description: 希望以后能多写一点博文
tags: 
    - 博文
    - 博客
    - Hexo
    - Git
categories: 叶子的小知识
comment: true
# keywords:
# sticky: 100

# hide: true/index
# author: 叶子
# email: clover@mskclover.com
hideTime: false
readmore: false
# ermaid: true
---

![cover](/images/new-garden-from-scratch/cover.jpg)

## 前言

自从今年初换了 MacBook 电脑以来，我每次写的东西都要用磁碟拷贝到原来的 PC 上才能发布，所以一直都想要将原来的博客本地文件迁移到这台新的电脑上。最近工作上的事告一段落，我总算是考完执照了。依照我以前的经验来看，每次以为未来会更有时间的时候，未来都会忙得要命，所以就趁着现在有空把这件事做完吧。

<!-- more -->

迁移文件其实也不难，主要是很繁琐。为了保证稳定性，我选择将所有的步骤都重新做一遍。这次就记录一下，以后再有迁移的时候，就可以按照现在的经验，知道要安装那些组件、更改哪些配置了。

还是与四年前一样，安装步骤主要是基于 [**云游前辈**](https://www.yunyoujun.cn/) 的这篇文章：[教你如何从零开始搭建一个属于自己的网站](https://www.yunyoujun.cn/posts/how-to-build-your-site/)，还有网站主题的[参考文档](https://yun.yunyoujun.cn/)，另外包含了一些遇到的新问题，希望能对以后的我、还有观看这篇博文的朋友有帮助。

## 迁移/重新安装步骤

就像上面说的一样，与其说是迁移，不如说是重新安装。

**⚠️ 注意事项：**

- 如果遇到 权限不足/Authentication 相关的报错，需要尝试 `su` 或者 `sudo` 并输入密码，以管理员权限重新执行。
- 由于中国大陆的防火长城目前已经阻止许多的站点，近日又屏蔽了包括上海交大在内的多所大学镜像站点，因此请确认网络环境是否支持。

{% blockquote ——李老师不是你老师（@whyyoutouzhele） https://twitter.com/whyyoutouzhele/status/1798731317322133642 %}
6 月 6 日，上海交大的 Docker Hub 镜像加速器宣布因监管要求被下架。
Docker Hub 是目前最大的容器镜像社区，但由于中国防火长城的限制，程序员开发者从 Docker Hub 上获取容器镜像的时候很不方便。Docker Hub 镜像加速器可以帮助开发者更快地下载 Docker 镜像。
<img src="/images/new-garden-from-scratch/post.jpg" width="500" alt="post" />
{% endblockquote %}

### 安装 Node.js

在其官网下载 **Node.js** 的 [长期发行版 LTS](https://nodejs.org/zh-cn/)，并全部默认下一步安装。过程中会默认安装 `npm` 包管理器，国内访问会比较困难，因此需要换源或者使用网络代理。

安装完成后，在终端内使用 `node --version` 查看版本，以确认安装成功。

```bash
% node --version 
v20.15.0
```

### 安装 VS Code

**Visual Studio Code**，在此 [下载](https://code.visualstudio.com/)。

### 安装 Git

> Git is a [free and open source](https://git-scm.com/about/free-and-open-source) distributed version control system designed to handle everything from small to very large projects with speed and efficiency.      
>
> Git is [easy to learn](https://git-scm.com/doc) and has a [tiny footprint with lightning fast performance](https://git-scm.com/about/small-and-fast). It outclasses SCM tools like Subversion, CVS, Perforce, and ClearCase with features like [cheap local branching](https://git-scm.com/about/branching-and-merging), convenient [staging areas](https://git-scm.com/about/staging-area), and [multiple workflows](https://git-scm.com/about/distributed).      

**Git**，在此[下载](https://git-scm.com/)。

与 Windows 不同，在 Mac 上无法直接通过安装包的方式获取 Git。因此我们需要先安装 [**Homebrew**](https://brew.sh/)，一个专为 macOS/Linux 打造的包管理器。Mac 用户可以无需在命令行中安装，[点此](https://github.com/Homebrew/brew/releases/latest/)即可下载最新版本的 **Homebrew**。

此时，如果直接使用 `brew` 指令会报错：

```bash
% brew install git                                                                               
zsh: command not found: brew
```

这是[因为](https://stackoverflow.com/questions/36657321/after-installing-homebrew-i-get-zsh-command-not-found-brew/)我们没有将 `/opt/homebrew/bin` 和 `/opt/homebrew/sbin` 添加到 `$PATH` 环境中，运行命令以快速添加：

```bash
% echo export PATH=$PATH:/opt/homebrew/bin:/opt/homebrew/sbin >> ~/.zshrc
% brew doctor
# run this to check it
```

接着输入命令安装：

```bash
% brew install git
```

按 Enter 确认，在「设置—通用—软件更新」中下载 XCode 的相关组件。然后查询其版本，以确认安装成功：

```bash
% git -v
git version 2.45.2 (Apple Git-146)
```

### 安装 Hexo

这是我目前使用的博客框架，在终端中输入命令**全局**安装：

```bash
% npm install hexo-cli -g
# 如果安装失败，可能是没有权限，可以尝试头部加上 sudo 重新执行
# sudo npm install hexo-cli -g
```

然后终端访问到我们打算创建本地文件的目录下，比如我现在的目录是：`/Users/clover/github/misaka-clover.github.io`。

```bash
% cd /Users/clover/github
% hexo init misaka-clover.github.io 
```

在使用 **Hexo** 初始化该文件夹后，输入：

```bash
# 进入你的博客文件夹
% cd misaka-clover.github.io
# 默认安装所有 `package.json` 文件中提到的包
% npm install
# 你也可以缩写成 hexo s
% hexo server
```

在浏览器中打开：`localhost:4000`，就可以看到一个默认的 Hexo 主页了。

### 安装 yun 主题

接下来安装**云游前辈**制作的主题 [hexo-theme-yun](https://github.com/YunYouJun/hexo-theme-yun/)：

```bash
% npm i hexo-theme-yun
```

在 Hexo 的原始配置文件 `_config.yml` 中找到 `theme` 这个字段，将其后的 `landscape` 修改为 `yun`。

```yaml
theme: yun
```

这时打开本地预览界面，发现显示的页面无比苍白（

![add-hexo-render](/images/new-garden-from-scratch/add-hexo-render.jpg)

这是因为，由于云游前辈的主题使用了 pug 和 stylus 这两个包，所以也需要安装：

```bash
% npm install hexo-render-pug hexo-renderer-stylus
```

#### 包 pug 和 stylus 的注意事项

不过在安装完以后，却发生重要的安全提示：

```bash
3 high severity vulnerabilities

Some issues need review, and may require choosing
a different dependency.

Run `npm audit` for details.
```

运行 `npm audit` 后，提示：



```bash
% npm audit
# npm audit report

pug  <=3.0.2
Severity: high
Remote code execution via the `pretty` option. - https://github.com/advisories/GHSA-p493-635q-r6gr
Pug allows JavaScript code execution if an application accepts untrusted input - https://github.com/advisories/GHSA-3965-hpx2-q597
Depends on vulnerable versions of pug-code-gen
No fix available
node_modules/hexo-render-pug/node_modules/pug
node_modules/pug
  hexo-render-pug  *
  Depends on vulnerable versions of pug
  node_modules/hexo-render-pug

pug-code-gen  <=2.0.3
Severity: high
Pug allows JavaScript code execution if an application accepts untrusted input - https://github.com/advisories/GHSA-3965-hpx2-q597
No fix available
node_modules/hexo-render-pug/node_modules/pug-code-gen

3 high severity vulnerabilities

Some issues need review, and may require choosing
a different dependency.
sh-3.2# 

```

原来是这两个包被发现有致命漏洞，将使得客户端被远程操纵，其 Report 在此：

- [Remote code execution via the `pretty` option.](https://github.com/advisories/GHSA-p493-635q-r6gr/)；
- [Pug allows JavaScript code execution if an application accepts untrusted input](https://github.com/advisories/GHSA-3965-hpx2-q597/)；

因此，我们需要更新这两个包至最新版本：

```bash
% npm install pug@3.0.3
% npm install pug-code-gen@3.0.3
```

或

```bash
% npm install pug@latest
% npm install pug-code-gen@latest
```

#### 包 core-js 的注意事项

另外，我还遇到了 core-js 的警告：

> **npm** warn deprecated core-js@2.6.12: core-js@<3.23.3 is no longer maintained and not recommended for usage due to the number of issues. Because of the V8 engine whims, feature detection in old core-js versions could cause a slowdown up to 100x even if nothing is polyfilled. Some versions have web compatibility issues. Please, upgrade your dependencies to the actual version of core-js.

根据[这篇贴子](https://stackoverflow.com/questions/73108853/npm-warn-deprecated-core-js2-6-12/)，强制更新至最新版本即可：

```bash
% npm cache clean --force
% npm i -D core-js@latest
```

## 自定义主题配置

为了**不直接修改主题的默认配置**，最好的解决方案就是在博客根目录下（不是主题目录）新建 `_config.yun.yml`。我直接将原来的配置拷贝过来，覆盖了现有的配置文件，结果在运行时直接报错。我料想应该是一些第三方组件没有安装，却在配置中调用了它们。所以，我不得不逐个检查配置项……

接下来，我依照主题的[配置文档](https://yun.yunyoujun.cn/)检查参数，没有什么需要修改的。

## 安装第三方包及额外依赖

有一些功能组件已经内置到主题文件中了，比如 **Giscus** 评论系统，所以我们可以直接在配置文件中设定好参数就可以了。但是，有一些功能并没有内置其中，因此需要手动安装。

### 安装 RSS 支持

在终端中敲入以下指令，安装 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed/)：

```bash
% npm install hexo-generator-feed --save
```

另外还需要生成一个文件什么的，详情请参见[官方文档](https://github.com/hexojs/hexo-generator-feed/)。

### 安装 词云 wordcount 组件

```bash
% npm install hexo-wordcount
```

### 安装 Hexo 的 Git 推送组件

此时，如果想要直接部署到 GitHub Pages 上，会出现如下报错：

```bash
% hexo d
INFO  Validating config
INFO  Yun version 1.10.11. Guide: https://yun.yunyoujun.cn
ERROR Deployer not found: git
```

根据这篇[博文](https://theriseofdavid.github.io/2020/07/11/blog/hexo-d-g-error-to-deployer-not-found/)，这是因为 缺少/我忘记安装 `hexo-deployer-git` 套件，在当前目录安装套件即可：

```bash
npm install hexo-deployer-git --save
```

## 推送问题

好了，现在再次执行 `hexo d` 指令，却发现有 GitHub 的权限问题：

```bash
remote: Support for password authentication was removed on August 13, 2021.
remote: Please see https://docs.github.com/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.
fatal: Authentication failed for 'https://github.com/misaka-clover/misaka-clover.github.io/'
FATAL Something's wrong. Maybe you can find the solution here: https://hexo.io/docs/troubleshooting.html
Error: Spawn failed
    at ChildProcess.<anonymous> (/Users/clover/github/misaka-clover.github.io/node_modules/hexo-deployer-git/node_modules/hexo-util/lib/spawn.js:51:21)
    at ChildProcess.emit (node:events:519:28)
    at ChildProcess._handle.onexit (node:internal/child_process:294:12)
```

根据这篇[贴子](https://stackoverflow.com/questions/68775869/message-support-for-password-authentication-was-removed/)，这是因为 GitHub 在云游前辈发布那篇博文以后，GitHub 更改了命令行认证的方式，因此我们需要去 GitHub 的后台[生成](https://github.com/settings/tokens/)一个验证码，在这里当作密码输入才行。

### 2025-01-31 更新

感谢 [**Skyone**](https://www.skyone.host/) 的留言，在得到允许以后，摘录如下：

> 文中提到的“推送问题”，git常用的有两种协议：https协议和ssh协议。
> 
> https协议使用 用户名+token 验证，而ssh使用公钥验证。建议你通过添加公钥的方式push提交。
> 
> 具体方法是，先查看远程：
> 
> `git remote -v`
> 
> 一般来说，会显示一个叫 origin 的类似 https://github.com/name/repo.git 的链接，把它改成 git@github.com:name/repo.git
> 
> `git remote remove origin`
> `git remote add origin git@github.com:name/repo.git`
> 
> 生成ssh密钥对（我记得macos自带ssh）
> 
> `ssh-keygen -t ed25519`
> 
> 按几次回车就行，公钥在 `~/.ssh/id_ed25519.pub` 添加到 github
> 
> 此外，推送应该使用
> 
> `git push origin master # 即 remote 名称和分支名称`

## 代码高亮问题

我检查完配置文件以后，发现代码高亮失效了。我以为是因为 Hexo 升级到高版本的问题，去查了一下[官方文档](https://hexo.io/zh-cn/docs/syntax-highlight#PrismJS/)，发现官方用了一个更新的高亮组件，并将其参数配置到 `_config.yml` 中了。

```yaml
# 代码高亮
# 只能在 _config.yml 中起作用
syntax_highlighter: prismjs
highlight:
  auto_detect: false
  line_number: true
  line_threshold: 0
  tab_replace: ""
  exclude_languages:
    - example
  wrap: true
  hljs: false
prismjs:
  preprocess: true
  line_number: true
  line_threshold: 0
  tab_replace: ""
```

## 嵌入 Twitter 贴子问题

我想在本文嵌入一个推特的贴子，加入 [**Twitter Publish**](https://publish.twitter.com/) 上复制的链接，居然无法变成预览的样子，所以只好使用 Hexo 自带的「[标签插件](https://hexo.io/zh-cn/docs/tag-plugins/)」功能。

## 本地文件 Git 备份问题

在依照云游君的教程时，我想要将本地文件通过 Git 推送到 GitHub 上，突然想起我好像没有配置本地的 Git，所以就：

```bash
# 与远程 Git 仓库建立连接，只此一次即可
git remote add origin https://github.com/misaka-clover/misaka-clover.github.io
```

然后，我将文件推送到缓冲区：

```bash
# 添加到缓存区
git add -A
git commit -m "这次做了什么更改，简单描述下即可"
```

但是，在推送的时候，却遇到了问题：

```bash
# 推送至远程仓库
$ git push
# 第一次提交，你可能需设置一下默认提交分支
# git push --set-upstream origin hexo

# 报错
error: src refspec hexo does not match any
error: failed to push some refs to 'https://github.com/misaka-clover/misaka-clover.github.io'
```

我查询了一些[资料](https://sentry.io/answers/git-src-refspec-master-does-not-match-any-error-message/)和[论坛](https://stackoverflow.com/questions/4181861/message-src-refspec-master-does-not-match-any-when-pushing-commits-in-git/)，发现是我的**这台新电脑本地并没有我新分支的文件**。理论上来说，一个从 GitHub 上将分支文件同步到本地，但实际上我删除了原来的那个分支，然后重新创建了一个一模一样的。因此，执行：

```bash
# 初始化 git
$ git init
Reinitialized existing Git repository in /Users/clover/github/misaka-clover.github.io/.git/

# 查询 git 下有哪些分支
$ git branch -a
* master

# 创建分支 hexo
$ git checkout -b hexo
Switched to a new branch 'hexo'
```

然后再推送，就一切正常了：

```bash
$ git add -A

$ git commit -m"transfer my local blog files to new macbook"
On branch hexo
nothing to commit, working tree clean

$ git push --set-upstream origin hexo
Username for 'https://github.com':
Password for 'https://misaka-clover@github.com':
failed to store: -60006
Enumerating objects: 14802, done.
Counting objects: 100% (14802/14802), done.
Delta compression using up to 8 threads
Compressing objects: 100% (10836/10836), done.
Writing objects: 100% (14802/14802), 48.70 MiB | 1.38 MiB/s, done.
Total 14802 (delta 3264), reused 14799 (delta 3262), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (3264/3264), done.
remote: 
remote: Create a pull request for 'hexo' on GitHub by visiting:
remote:      https://github.com/misaka-clover/misaka-clover.github.io/pull/new/hexo
remote: 
To https://github.com/misaka-clover/misaka-clover.github.io
 * [new branch]      hexo -> hexo
branch 'hexo' set up to track 'origin/hexo'.
```

然后，就大功告成啦。

## macOS 的终端显示英文

参照有关[文章](https://www.vvave.net/archives/chinese-shell-terminal-change-to-english.html/)的解决方法：

```bash
# 查看本地配置
$ locale
LANG=""
LC_COLLATE="C"
LC_CTYPE="UTF-8"
LC_MESSAGES="C"
LC_MONETARY="C"
LC_NUMERIC="C"
LC_TIME="C"
LC_ALL=

# 生效新的配置
$ export LANG=en_US.UTF-8
```

## 新学到的 git 知识

你知道的，我在计算机程序领域并没有多么深入的了解，像是使用 Git，我只会以上的那几个简单的命令。而在大多数时候，我没办法去解决遇到的问题，只好在网上搜索，比如[这个](https://www.cyberciti.biz/faq/linux-unix-exit-vim-editor/)、[这个](https://www.hokang.info/git-pull-warning/)和[这个](https://jumping-code.com/2022/06/09/solution-git-divergent-branches/)（但愿不是内容农场🙏）。

不过，感谢前辈们的互联网精神，我在 Git 的官网上找到了一份完整的[教程](https://git-scm.com/book/zh/v2/)以及[简体中文翻译件](https://github.com/progit/progit2-zh/releases/download/2.1.68/progit.pdf/)，我打算在接下来的几个月内好好地学习一下。你知道的，之前我没有学过 SQL，现在不也一样能上手了。

我最喜欢学习了（
## npm 包列表

```bash
% npm list
hexo-site@0.0.0 /Users/clover/github/misaka-clover.github.io
├── core-js@3.37.1
├── hexo-deployer-git@4.0.0
├── hexo-generator-archive@2.0.0
├── hexo-generator-category@2.0.0
├── hexo-generator-feed@3.0.0
├── hexo-generator-index@3.0.0
├── hexo-generator-tag@2.0.0
├── hexo-render-pug@2.1.4
├── hexo-renderer-ejs@2.0.0
├── hexo-renderer-marked@6.3.0
├── hexo-renderer-stylus@3.0.1
├── hexo-server@3.0.0
├── hexo-theme-landscape@1.0.0
├── hexo-theme-yun@1.10.11
├── hexo-wordcount@6.0.1
├── hexo@7.2.0
├── pug-code-gen@3.0.3
└── pug@3.0.3
```

## 后记

其实我当时是想要直接用上云游前辈开发的新框架的，可是这中间出了一些我无法解决的问题，想着它还处于开发中的阶段，就算了。

现在可以在 MacBook 上直接发布了吧？那就快多写一点博文吧！
