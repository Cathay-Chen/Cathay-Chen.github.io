---
title: "我的博客搭建过程"
date: 2022-07-27T18:26:12+08:00
draft: false
---

## 前言

既然是第一篇博客，那肯定就是搭建的过程喽。

最近开始学习 Go 语言，接触到了 [Hugo](https://gohugo.io/)，简单了解了一下，可以通过 git 提交博文到 [GitHub](https://github.com/) 仓库 [Vercel](https://vercel.com/) 自动更新内容到博客站便捷的更新博客，突然就来了兴致。之前用的 PHP 的 [Typecho](https://typecho.org/) 在自己服务器上搭建了一个[博客](http://www.chenguotai.com)，也没怎么更新内容，希望这次可以博客搭建之后，可以通过 git 提交的方式能够做到经常分享我的所见所闻所学所悟。

话不多说，接下来进入正题...

## 安装配置运行 Hugo

>  我这里只记录我用 mac 安装的详细过程，需要其他系统安装的请参考[官方文档](https://gohugo.io/getting-started/installing/)。

1. 使用 brew 安装

   ```shell
   ## 安装 hugo 
   brew install hugo
   ```

2. 创建新站点

   >  相当于初始化一个新的项目 :）

   ```shell
   ## 创建站点
   hugo new site 站点名称
   ```

3. 添加一个主题

   > 这里我使用的是我一个一直关注的博主的主题，[hugo-theme-echo](https://github.com/forecho/hugo-theme-echo)，其他更多主题可以通过官方提供的[主题链接](https://themes.gohugo.io/)自行选择。

   ```shell
   ## 打开刚创建的站点目录
   cd 站点名称
   
   ## git 初始化并 clone 主题文件到 git 子模块
   git init
   git submodule add https://github.com/forecho/hugo-theme-echo.git themes/echo
   ```

4. 在根目录添加 `package.json` 文件，代码如下（一些个人信息请改成你自己的）：

   ```json
   {
       "name": "xxx-blog",
       "version": "0.0.1",
       "description": "that is xxx blog",
       "repository": "https://github.com/forecho/hugo-blog",
       "license": "MIT",
       "devDependencies": {
           "autoprefixer": "^10.2.5",
           "postcss": "^8.2.9",
           "postcss-cli": "^8.3.1",
           "tailwindcss": "^2.0.4"
       },
       "browserslist": [
           "last 1 version",
           "> 1%",
           "maintained node versions",
           "not dead"
       ]
   }
   ```

5. 修改配置文件 `/根目录/config.toml`，代码如下（一些个人信息请改成你自己的）：

   > 注意：配置参数根据主题来的，这里是我这个主题可以配置的参数，请根据自己主题修改属于你们自己的配置，基本主题主页都有说明。

   ```toml
   baseURL = "http://localhost:1313"
   languageCode = "en-us"
   title = "Forecho's Blog"
   theme = "echo"
   DefaultContentLanguage = "cn"
   # 自动检测是否包含中文/日文/韩文，该参数会影响摘要和字数统计功能，建议设置为 true
   hasCJKLanguage = true
   # 设置页面生成形式，将默认的网站路径/修改成.html
   uglyURLs = true
   googleAnalytics = ""      # UA-XXXXXXXX-X
   
   [markup.highlight]
   codeFences = true # 高亮 markdown 的代码块
   guessSyntax = true # 高亮 markdown 中没有标注语言的代码块
   hl_Lines = ""
   lineNoStart = 1
   lineNos = true
   lineNumbersInTable = true
   noClasses = true
   style = "manni"
   tabWidth = 2
   
   # 网站文章 url 地址格式 see https://gohugo.io/content-management/urls/#aliases
   [permalinks]
   posts = "/:filename"
   
   [outputFormats.RSS]
   mediatype = "application/rss"
   baseName = "atom"
   
   [services.rss]
   limit = 20
   
   [author]
   name = "your name"
   avatar = "https://avatars0.githubusercontent.com/u/1725326?s=460&v=4"
   bio = "7 年开发经验，寻求技术 Leader 工作机会。Wechat: ipzone"
   homepage = "https://forecho.com/"
   
   [params]
   favicon = "https://avatars0.githubusercontent.com/u/1725326?s=460&v=4"
   keywords = "Hugo, theme, echo"
   description = "Hugo theme echo example site."
   toc = true
   navItems = [
     ["HOME", "/"],
     ["ARCHIVE", "/posts.html"],
     ["ABOUT", "/about.html"],
     ["RSS", "/atom.xml"]
   ]
   
   # rss 全文输出
   rssFullContent = true
   uglyURLs = true
   busuanzi = true # 是否使用不蒜子统计站点访问量
   staticCDNPrefix = "https://cdn.bootcss.com/font-awesome/5.11.2"
   extraHead = '<script async src="https://www.googletagmanager.com/gtag/js?id=UA-xxx"></script>'
   postAds = ""
   profileAds = '<div class="bg-white shadow"><img class=" object-cover w-auto mx-auto mt-6" src="https://blog-1251237404.cos.ap-guangzhou.myqcloud.com/20190424153337.png" alt="微信打赏"></div>'
   notFoundAds = ''
   
   # 开启版权声明，协议名字和链接都可以换
   [params.cc]
   name = "署名 - 非商业性使用 4.0 国际 (CC BY-NC 4.0)"
   link = "https://creativecommons.org/licenses/by-nc/4.0/deed.zh"
   
   # 文章打赏
   [params.reward]
   enable = true
   title = "打赏"
   wechat = "https://blog-1251237404.cos.ap-guangzhou.myqcloud.com/20190424153510.png" # 微信二维码
   alipay = "https://blog-1251237404.cos.ap-guangzhou.myqcloud.com/20190424153431.png" # 支付宝二维码
   
   ############## 评论系统  start ##############
   [params.gitment] # Gitment is a comment system based on GitHub issues. see https://github.com/imsun/gitment
   owner = "" # Your GitHub ID
   repo = "" # The repo to store comments
   clientId = "" # Your client ID
   clientSecret = "" # Your client secret
   
   [params.utterances] # https://utteranc.es/
   owner = "" # Your GitHub ID
   repo = "" # The repo to store comments
   theme = "github-light"
   issueterm = "pathname"
   
   [params.gitalk] # Gitalk is a comment system based on GitHub issues. see https://github.com/gitalk/gitalk
   owner = "" # Your GitHub ID
   repo = "" # The repo to store comments
   clientId = "" # Your client ID
   clientSecret = "" # Your client secret
   
   # Valine.
   # You can get your appid and appkey from https://leancloud.cn
   # more info please open https://valine.js.org
   [params.valine]
   enable = false
   appId = '你的 appId'
   appKey = '你的 appKey'
   notify = false # mail notifier , https://github.com/xCss/Valine/wiki
   verify = false # Verification code
   avatar = 'mm'
   placeholder = '说点什么吧...'
   visitor = false
   
   [params.giscus] # https://giscus.app
   repo = "forecho/hugo-blog" # Repo where the comments will live
   repo_id = "<data-repo-id>" # Repo where the comments will live
   category = "Announcements" # Repo where the comments will live
   category_id = "<data-category-id>" # Repo where the comments will live
   mapping = "pathname" # How Utterances will match comment to page
   theme = "light" # What theme to use
   reactions_enabled = "1" # Enable reactions
   emit_metadata= "0" # Emit metadata
   lang = "zh-CN" # Language
   ############ 评论系统  end ##############
   
   ## 社交链接
   [social]
   github = "forecho"
   jsfiddle = "forecho"
   codepen = "forecho"
   dribbble = "forecho"
   behance = "forecho"
   flickr = "forecho"
   instagram = "forecho"
   youtube = "forecho"
   vimeo = "forecho"
   vine = "forecho"
   medium = "forecho"
   wordpress = "forecho"
   tumblr = "forecho"
   linkedin = "forecho"
   slideshare = "forecho"
   stackoverflow = "forecho"
   reddit = "forecho"
   pinterest = "forecho"
   weibo = "forecho"
   facebook = "forecho"
   twitter = "caizhenghai"
   douban = "ipzone"
   rss = "/atom.xml"
   ```

6. 本地运行，查看效果

   ```shell
   ## 打开根目录，安装前端页面需要依赖
   npm install
   
   ## 运行 hugo
   hugo server -D
   ```



## 博客的部署

### 静态文件部署

如果希望在自己服务器部署可以通过 hugo 打包成静态页面部署，打包命令如下：

```shell
## 打包， 默认情况下，输出将在 ./public/ 目录中（-d/--destination 标志来更改它，或者在配置文件中设置 publishdir）。
hugo -D
```



### 使用 Vercel 部署

1. GitHub项目，打开 https://github.com/new 创建新项目

2. 创建成功后将本地项目文件提交到项目

3. 打开

   

