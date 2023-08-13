---
title: "[博客搭建] 用Hugo搭建个人网站"
date: 2023-08-12
author: 余忻萌
categories:
  - test
tags:
  - article
---

- 从有想法到实现整了好几天，经历了 「看起来好简单的样子」 -> 「卡在了开头」 -> 「放弃」 -> 「再试一次」 -> 「再试N次」的过程 
- 只想告诉自己经验少、知识不足不该是借口，always on the road to learning.
- 总之，深刻感受编程知识不是看来的而是自己试出来的，再详细的教程实操起来都困难重重，看起来再无脑的坑实际操作时还是会踩到，多学多练别放弃。

---

### **搭建过程**
***Step 1.*** 首先：
要有github账号，有文本编辑器，安装git，安装hugo，知道一些Markdown语法
- [安装hugo](https://github.com/gohugoio/hugo/releases)
 需要配置系统路径
- Markdown 可以边用边学
- 用git bash终端进行更新

***Step 2.*** 新建hugo网站
hugo博客是一个文件夹，找一个位置用终端新建一个hugo文件：
```
hugo new site mywebsite
```

***Step 3.*** 选一个hugo网站主题
- [hugo主题](https://themes.gohugo.io/)
放进新建的mywebsite/themes中之后，可以用：
```
hugo server -D # 这里的 D 是 draft 的意思
```
在 http://localhost:1313/ 上进行预览，Ctrl+C停止预览。

***Step 4.*** 新建一个github仓库
命名成  Github用户名 xxx.github.io (public)

***Step 5.*** 密钥设置
- 首先,在mywebsite根目录下终端输入：
```
mkdir .github
mkdir .github/workflows
touch .github/workflows/gh-pages.yml
```
- 其次，在git bash 中输入：
```
cd /C/Users/电脑用户名
start ~/.ssh
mkdir ~/.ssh/ && cd ~/.ssh # 在当前目录下新建.ssh文件夹
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
start ~/.ssh 
```
会得到两个新增的文件：gh-pages 和 gh-pages.pub

- 然后，在github新建的库里：Settings -> Deploy keys ->Add deploy key，
同时终端输入：
```
clip < ~/.ssh/gh-pages.pub # 复制里面的deploy key
```
粘贴到库的key value里，Add key。

- 再在 github新建的库里：Settings -> -> Actions ->New repository secret，同时终端输入：
```
clip < ~/.ssh/gh-pages # 复制里面的secret
```
粘贴到库的secret value,Name填ACTIONS_DEPLOY_KEY，Add secret。

- 之后打开[这里](https://github.com/settings/tokens)设置github的个人token，点击Generate new token (Classic)，选中workflow，然后generate token并复制。
github新建的库里：Settings -> Security -> Secrets -> Actions -> New repository secret，粘贴到value框，Name填PERSONAL_TOKEN，Add secret。

***Step 6.*** 添加workflow
终端输入：
```
notepad .github/workflows/gh-pages.yml
```
粘贴以下文本：
```
name: github pages

on:
  push:
    branches:
      - master  # Set a branch name to trigger deployment

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod
          token: ${{ secrets.PERSONAL_TOKEN }}

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '填自己的hugo version' # 请修改你的 hugo version

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.PERSONAL_TOKEN }}
          publish_branch: gh-pages
          publish_dir: ./public
```

***Step 7.*** 上传到github
```
git init
git add .
git commit -m "first commit."
git remote add origin https://github.com/github用户名/github用户名.github.io.git
git push -u origin master
```
在github新建的库里：Settings -> Pages,在 Source 那里，Branch 选择 gh-pages, 右侧的框框选择 root，然后点 Save。得等一会：
```
Your site is published at https://github用户名.github.io/
```

***Step 8.*** 后续更新
直接更改本地hugo文件就行，改了之后上传到github：
```
git add .
msg="updating site on $(date)" 
git commit -m "$msg"
git push origin master
```
把上面四行打包放到一个update.sh文件里，放在hugo文件夹的根目录下，下次更新的时候直接运行：
```
bash update.sh
```

### **过程中遇到的问题**
***1.*** 国内github经常出现连不上的问题，进行git更新的时候一半时间都会出错，使用VPN浏览github倒是可以，用git向库里更新时还是经常会出错，类似于：
```
Fatal: fatal: unable to access 'https://github.com/xxx': OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
```
搜到的解决办法，输入：
```
git config --global --unset-all remote.origin.proxy
```
来消除behind a proxy的影响。
实际来说对我并不管用，只能多传几次直到更新成功

***2.*** 可能是github经常连接出问题的缘故，在最后步骤 Branch 里选择的时候，一开始怎么都没有gh-pages分支，一直都以为是之前的步骤没做好，出了问题，重新来过了很多次，之后用的同样的流程，等个几分钟之后发现就有了。后面在git更新频繁出问题后，回想下这里或许也是由于大陆github连接不稳的缘故。

***3.*** 在网站中增加图片的方法：
在根目录的static文件夹下新建一个用于存图片的文件夹，例如images，之后再加入图片的话就可以在帖子中这样加：
```
{figure src= "/images/picture.jpg" title = "blablabla" caption = "blablabla" width = "xxx"}
```

### **个人的一些奇奇怪怪的问题**
***1.*** 用git bash终端更新的时候，进入hugo网站文件是直接复制路径的，这样的路径类似于
```
$ cd C:\Users\my_website
```
就会得到“No such file or directory”的反应，要改成：
```
$ cd /c/user/my_website
```

***2.*** 按教程设置SSH Deploy Key的时候生成的gh-pages 和 gh-pages.pub文件在.ssh下怎么都找不到，最后发现是保存在了/C/User/Admin文件夹下，事实上配置用的代码里就是这么写的：
```
$ cd /C/User/Admin
```

***3.*** 更新主页图片的时候怎么都显示不出来，一度以为是自己的src路径写错了，没怎么写过html语言，找了好多上传图片的方法，结果发现是自己把
```
<figure src="/images/sandy.jpg" title="I am swimming in the ocean of knowledge." width="300">
```
写成了：

```
<figure src="\images\sandy.jpg" title="I am swimming in the ocean of knowledge." width="300">
```
耽误了好久，发现的时候简直无语死了 =.= ，以前也有过把main()写成mian(),该长记性!


