---
title: 在Github上搭建Hexo静态博客
date: 2016-08-05 20:39:14
categories: Web
tags: 
- Githup 
- Hexo
---
## 准备
**系统：**
 - Window7 64位
**软件：**
 - Git
 - Node.js
 **注册账号：**
 - GitHub账号

 * * * * *
 
 <!--more-->
## 搭建环境
 1. 验证是否已安装完相关软件
- 验证是否已安装git，在任意目录下右击鼠标查看是否找打Git Bash Here的选项。
![enter description here][1]
- 验证是否安装Node.js,打开cmd(点击"开始菜单" -> "运行" -> 在打开的输入框中输入cmd->回车确认)，输入命令行 node -v。查看是否能显示出Node.js 的当前版本信息。
![enter description here][2]
 2. 相关软件的安装
 - Git: [msysgit](https://www.baidu.com/link?url=yxX6Se12M8uzk1hi-xJ4HgKmfl1wPLTnS8C4aTASGX3Y7PhTcS_ikeB7FRb92JjdisfTCcYj8l_NJiYeK9koQNuQS7K-yPNjLpeoMVDzZDG&wd=&eqid=e5e319750007f04b0000000657a4a6a2)软件，执行安装程序，直接默认安装。
 - Node.js:[node.js](https://nodejs.org/dist/v4.4.4/node-v4.4.4-x64.msi),直接默认安装.
 3. GitHub注册
 - 进入[GitHub首页](https://github.com/),按提示创建你的GitHub账号。当你填写完你的信息后，github会发一封邮件到你的注册邮箱下，点击邮箱的链接地址就能激活你的账号。
 - 账号激活后登陆你的githup账号，然后点击New repository 新建一个repository（存放你博客代码的版本库）。版本库的名称格式应遵从 "xxxx.github.io"，xxxx为你的账号名称。如我的账号名称为linkezhi，建立的版本库名称为 linkezhi.github.io
 ![enter description here][3] 
 ![enter description here][4]
 - 进入到你这个版本库中，切换到Setting选项->点击''Launch automatic page generator''(构建静态网页)->点击底部的"Continue to layouts"->点击"Publish page",然后通过在浏览器里输入"https://xxxx.github.io/"  （xxxx为账号名）地址就能访问你的静态页面了。
 ![enter description here][5]
 ![enter description here][6]
 ![enter description here][7]
 * * * * *
 ## 安装Hexo
 1. 新建一个文件夹如mBlog，文件夹所在的目录最好要没有中文路径
 2. 进入mBlog文件夹，空白位置右键点击`Git Bash Here`
 3. 安装Hexo
 `npm install hexo-cli -g`
 4. 初始化mBlog文件夹
  `hexo init`
 5. 生成静态文件
 `hexo g`
 6. 启动服务器进行本地预览
 `hexo s`
 7. 浏览器打开http://localhost:4000/ ，安装成功就能看到博客页面，Hexo安装就完成了
 ![enter description here][8]
 >因为墙的原因有时初始化时会比较慢，耐心等待就行。有些教程会用淘宝NPM镜像，但使用NPM镜像安装的Hexo有时候会初始化时报错。
 
 * * * * *

## 配置Hexo

> 本博客的Hexo使用的是[Next主题](http://theme-next.iissnan.com/getting-started.html),，Next主题简洁优雅是本人比较喜欢的一种风格，所以下面就是如何配置Next主题的Hexo。


----------

 1. 下载Next 主题
进到你的Hexo安装目录下的themes下，右键--Git Bash Here，打开git的命令行窗口，通过git下载next
`git clone https://github.com/iissnan/hexo-theme-next themes/next`
下载完后可以通过Hexo的配置文件启用主题。

 2. Hexo 基础配置
 在你安装的Hexo目录下有个_config.yml文件，这个文件就是Hexo的配置文件，打开文件进行配置。下面是我的配置，可以参照着配置自己的博客。

``` 
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: Z博客 #站点名，站点左上角
subtitle: Hello World #副标题，站点左上角
description: #给搜索引擎看的，对站点的描述，可以自定义
author: linkezhi #在站点左下角可以看到
language: zh-Hans #Next主题下的中文设置，主题不同属性值也不同
timezone:

# URL #这项暂不配置，绑定域名后，欲创建sitemap.xml需要配置该项
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://linkezhi.github.io/
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing 文章布局、写作格式的定义，不修改
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination 每页显示文章数
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Extensions 配置站点所用主题和插件 这里配置的是next主题，需要先下载主题再配置
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next

# Deployment 站点部署到github的配置
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  #linkezhi改为你的github用户名
  repository: git@github.com:linkezhi/linkezhi.github.io.git
  branch: master
```

配置完成了可以重新构建一下，启动Hexo查看一下站点。
![enter description here][9]

## 发布博客
> 我们能通过http://localhost:4000/访问自己的博客，在同一个局域网其他机器也可以通过http://+‘主机ip’+:4000/ 访问博客，但为了让别人能通过互联网访问我们的博客站点，我们需要把本地的站点发布到Github上，下面就叫大家如何把本地的博客站点发布到Github上。


 1.配置SSH keys

> SSH keys 能把本地的git项目与远程的github建立联系

生成新的SSH Key：
在Git Bash输入以下指令（任意位置点击鼠标右键），检查是否已经存在了SSH keys。
 `ls -al ~/.ssh`
如果存在的话，直接删除.ssh文件夹里面所有文件重新生成。
生成SSH Key：

``` 
$ ssh-keygen -t rsa -C "邮件地址@youremail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<直接回车>
```
注意1: 此处的邮箱地址，你可以输入自己的邮箱地址；
注意2: 此处的「-C」的是大写的「C」
然后系统会要你输入密码：

``` 
Enter passphrase (empty for no passphrase):<输入加密串>
Enter same passphrase again:<再次输入加密串>
```

在回车中会提示你输入一个密码，这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往你的项目里提交内容。


 2.添加SSH key到Github
 
 键入以下指令，拷贝Key（先拷贝了，等一下可以直接粘贴）：
clip < ~/.ssh/id_rsa.pub
到Github里面新增SSH key
![enter description here][17]
然后在Key里粘贴刚刚拷贝的key（也可以文本打开文件复制里面的内容）
点击 Add Key

输入你的Github密码即可完成SSH Key的添加。嗯，最后还是测试一下吧，键入以下命令，你可能会看到有警告，没事，输入“yes”就好。：
 `ssh -T git@github.com `
![enter description here][18]


 3.设置git身份信息
键入指令：

``` 
$ git config --global user.name "你的用户名"
$ git config --global user.email "你的邮箱"
```
发布更新博客

``` stylus
hexo d -g
```
发布时需要输入github的帐号和密码，输入密码时是看不到自己输入的内容的
如果发布遇到

``` vbscript
On branch master
nothing to commit， working directory clean
```

就先删除.deploy 再发布
``` 
rm -rf .deploy
hexo clean
hexo g
hexo d
```


发布成功后在浏览器输入：
http://你的用户名.github.io/
![enter description here][19]

每次修改本地文件后，需要键入hexo generate才能保存。每次使用命令时，都要在C:\Hexo目录下。每次想要上传文件到Github时，就应该先键入hexo generate保存之后，再键入hexo deploy。

  [1]: /img/4ec2d5628535e5dd90259a7e71c6a7efce1b6201.jpg "4ec2d5628535e5dd90259a7e71c6a7efce1b6201.jpg"
  [2]: /img/d5c67235-696a-40c6-9cae-0313657d7de8.png "d5c67235-696a-40c6-9cae-0313657d7de8.png"
  [3]: /img/N%5BAM%29X2XSU77%7BO_WG1T%287DY.png "N[AM&#41;X2XSU77{O_WG1T&#40;7DY.png"
  [4]: /img/GMO$~1CQFHV~Z49EPIJX32N.png "GMO$~1CQFHV~Z49EPIJX32N.png"
  [5]: /img/0J7KLLIH%295%28C%28CRVT7$3RMI.png "0J7KLLIH&#41;5&#40;C&#40;CRVT7$3RMI.png"
  [6]: /img/LVFCA%28XA51$1XJ~D7XF~Z8W.png "LVFCA&#40;XA51$1XJ~D7XF~Z8W.png"
  [7]: /img/%7DCT%5BPTFY0V_DT7AW%60ASP~54.png "}CT[PTFY0V_DT7AW`ASP~54.png"
  [8]: /img/XEKC0QG50%7BH%28F1M~A%5DFSVQ1.png "XEKC0QG50{H&#40;F1M~A]FSVQ1.png"
  [9]: /img/QQ%E6%88%AA%E5%9B%BE20160817205016.png "20160817205016.png"
  [10]: /img/QQ%E6%88%AA%E5%9B%BE20160817210526.png "20160817210526.png"
  [11]: /img/QQ%E6%88%AA%E5%9B%BE20160817210830.png "20160817210830.png"
  [12]: /img/QQ%E6%88%AA%E5%9B%BE20160817211531.png "20160817211531.png"
  [13]: /img/QQ%E6%88%AA%E5%9B%BE20160817211645.png "20160817211645.png"
  [14]: /img/QQ%E6%88%AA%E5%9B%BE20160817211718.png "20160817211718.png"
  [15]: /img/QQ%E6%88%AA%E5%9B%BE20160817212021.png "20160817212021.png"
  [16]: /img/QQ%E6%88%AA%E5%9B%BE20160817212111.png "20160817212111.png"
  [17]: /img/QQ%E6%88%AA%E5%9B%BE20160817212322.png "20160817212322.png"
  [18]: /img/QQ%E6%88%AA%E5%9B%BE20160817212755.png "20160817212755.png"
  [19]: /img/QQ%E6%88%AA%E5%9B%BE20160817213113.png "20160817213113.png"