---
title: "[博客搭建] 用Hugo搭建个人网站"
date: 2023-08-12
author: 余忻萌
---
<h1 id="-hugo-">[博客搭建] 用Hugo搭建个人网站</h1>
<h3 id="-">搭建过程</h3>
<p>1.首先：
要有github账号，有文本编辑器，安装git，安装hugo，知道一些markdown语法</p>
<ul>
<li><a href="https://github.com/gohugoio/hugo/releases">安装hugo</a>
需要配置系统路径</li>
</ul>
<p>2.新建hugo网站
hugo博客是一个文件夹，找一个位置用终端新建一个hugo文件：</p>
<pre><code>hugo new site mywebsite
</code></pre><p>3.选一个hugo网站主题</p>
<ul>
<li><a href="https://themes.gohugo.io/">hugo主题</a>
放进新建的mywebsite/themes中之后，可以用：<pre><code>hugo server -D # 这里的 D 是 draft 的意思
</code></pre>在 <a href="http://localhost:1313/">http://localhost:1313/</a> 上进行预览，Ctrl+C停止预览。</li>
</ul>
<p>4.新建一个github仓库
命名成  Github用户名 xxx.github.io (public)</p>
<p>5.密钥设置</p>
<ul>
<li>首先,在mywebsite根目录下终端输入：<pre><code>mkdir .github
mkdir .github/workflows
touch .github/workflows/gh-pages.yml
</code></pre></li>
<li><p>其次，在git bash 中输入：</p>
<pre><code>cd /C/Users/电脑用户名
start ~/.ssh
mkdir ~/.ssh/ &amp;&amp; cd ~/.ssh # 在当前目录下新建.ssh文件夹
ssh-keygen -t rsa -b 4096 -C &quot;$(git config user.email)&quot; -f gh-pages -N &quot;&quot;
start ~/.ssh
</code></pre><p>会得到两个新增的文件：gh-pages 和 gh-pages.pub</p>
</li>
<li><p>然后，</p>
<ul>
<li><p>github新建的库里：Settings -&gt; Deploy keys -&gt;Add deploy key，
同时终端输入：</p>
<pre><code>clip &lt; ~/.ssh/gh-pages.pub # 复制里面的deploy key
</code></pre><p>粘贴到库的key value里，Add key。</p>
</li>
<li><p>github新建的库里：Settings -&gt; -&gt; Actions -&gt;New repository secret，
再终端输入：</p>
<pre><code>clip &lt; ~/.ssh/gh-pages # 复制里面的secret
</code></pre><p>粘贴到库的secret value,Name填ACTIONS_DEPLOY_KEY，Add secret。</p>
</li>
<li><p>打开<a href="https://github.com/settings/tokens">这里</a>设置github的个人token，点击Generate new token (Classic)，选中workflow，然后generate token并复制。
github新建的库里：Settings -&gt; Security -&gt; Secrets -&gt; Actions -&gt; New repository secret，粘贴到value框，Name填PERSONAL_TOKEN，Add secret。</p>
</li>
</ul>
</li>
</ul>
<p>6.添加workflow
终端输入：</p>
<pre><code>notepad .github/workflows/gh-pages.yml
</code></pre><p>粘贴以下文本：</p>
<pre><code>name: github pages

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
          hugo-version: &#39;填自己的hugo version&#39; # 请修改你的 hugo version

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.PERSONAL_TOKEN }}
          publish_branch: gh-pages
          publish_dir: ./public
</code></pre><p>7.上传github</p>
<pre><code>git init
git add .
git commit -m &quot;first commit.&quot;
git remote add origin https://github.com/github用户名/github用户名.github.io.git
git push -u origin master
</code></pre><p>在github新建的库里：Settings -&gt; Pages,在 Source 那里，Branch 选择 gh-pages, 右侧的框框选择 root，然后点 Save。得等一会：</p>
<pre><code>Your site is published at https://github用户名.github.io/
</code></pre><p>8.后续更新
直接更改本地hugo文件就行，改了之后上传到github：</p>
<pre><code>git add .
msg=&quot;updating site on $(date)&quot; 
git commit -m &quot;$msg&quot;
git push origin master
</code></pre><p>把上面四行打包放到一个update.sh文件里，放在hugo文件夹的根目录下，下次更新的时候直接运行：</p>
<pre><code>bash update.sh
</code></pre><h3 id="-">过程中遇到的问题</h3>
<p>1.国内github经常出现连不上的问题，进行git更新的时候一半时间都会出错，使用VPN浏览github倒是可以，用git向库里更新时还是经常会出错，类似于：</p>
<pre><code>Fatal: fatal: unable to access &#39;https://github.com/xxx&#39;: OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
</code></pre><p>搜到的解决办法，输入：</p>
<pre><code>git config --global --unset-all remote.origin.proxy
</code></pre><p>来消除behind a proxy的影响。
实际来说对我并不管用，只能多传几次直到更新成功</p>
<p>2.可能是github经常连接出问题的缘故，在最后步骤 Branch 里选择的时候，一开始怎么都没有gh-pages分支，一直都以为是之前的步骤没做好，出了问题，重新来过了很多次，之后用的同样的流程，等个几分钟之后发现就有了。后面在git更新频繁出问题后，回想下这里或许也是由于大陆github连接不稳的缘故。</p>
<p>3.在网站中增加图片的方法：
在根目录的static文件夹下新建一个用于存图片的文件夹，例如images，之后再加入图片的话就可以在帖子中这样加：</p>
<pre><code>{figure src= &quot;/images/picture.jpg&quot; title = &quot;blablabla&quot; caption = &quot;blablabla&quot; width = &quot;xxx&quot;}
</code></pre><h3 id="-">个人的一些奇奇怪怪的问题</h3>
<p>1.用git bash终端更新的时候，进入hugo网站文件是直接复制路径的，这样的路径类似于</p>
<pre><code>$ cd C:\Users\my_website
</code></pre><p>就会得到“No such file or directory”的反应，要改成：</p>
<pre><code>$ cd /c/user/my_website
</code></pre><p>2.按教程设置SSH Deploy Key的时候生成的gh-pages 和 gh-pages.pub文件在.ssh下怎么都找不到，最后发现是保存在了/C/User/Admin文件夹下，事实上配置用的代码里就是这么写的：</p>
<pre><code>$ cd /C/User/Admin
</code></pre><p>3.更新主页图片的时候怎么都显示不出来，一度以为是自己的src路径写错了，没怎么写过html语言，找了好多上传图片的方法，结果发现是自己把</p>
<pre><code>&lt;figure src=&quot;/images/sandy.jpg&quot; title=&quot;I am swimming in the ocean of knowledge.&quot; width=&quot;300&quot;&gt;
</code></pre><p>写成了：</p>
<pre><code>&lt;figure src=&quot;\images\sandy.jpg&quot; title=&quot;I am swimming in the ocean of knowledge.&quot; width=&quot;300&quot;&gt;
</code></pre><p>耽误了好久，发现的时候简直无语死了 =.= ，以前也有过把main()写成mian(),该长记性!</p>
<hr>
<p>整了好几天，经历了 「看起来好简单的样子」 -&gt; 「卡在了开头」 -&gt; 「放弃」 -&gt; 「再试一次」 -&gt; 「再试N次」 
只想告诉自己经验少、知识不足不该是借口，always on the road to learning.
总之，深刻感受编程知识不是看来的而是自己试出来的，再详细的教程实操起来都困难重重，看起来再无脑的坑实际操作时还是会踩到，多学多练别放弃。</p>