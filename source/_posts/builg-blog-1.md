---
title:  hexo + docker + github搭自己的博客
date: 2019/3/17
comments: true
categories: 
- tech
tags: 
- hexo
- docker
- blog
---
新鲜出炉的博客! 今天上午刚刚搭好自己的博客,特兴奋. 由于自己毫无技术背景,搭博客这过程极为艰辛,走了不少弯路,一度想放弃. 特写下这篇文章以激励那些还在路上摸索的人们:我都能搞定的事情,你们一定可以. 我是用Docker + hexo + github来完成这一过程的,三者的关系如下:从docker上拉取镜像生成容器,容器里含有安装hexo所需的应用:Node.js与Git(Docker的好处在于不用在自己电脑上安装这些应用了).hexo是博客生成框架,用来生成肉眼可见的静态网页(就是现在看到的页面).用hexo生成了静态网页之后,将其托管给github,生成github page,这样别人都能访问啦. 相关概念如下: 
[Docker](https://en.wikipedia.org/wiki/Docker_software"docker")
[hexo](https://hexo.io/zh-cn/docs/) 
[github page](https://pages.github.com/) 

本文接下来将从三个部分记录博客搭建过程: 
1. 前期准备 
2. 博客生成阶段 
3. 创建github page 

# 前期准备 
### 下载docker 
这儿有[Docker官方文档](https://docs.docker.com/get-started/ "docker_document"),上面有详细指引,跟着一步一步走就ok啦.个人的话注意下载CE版本. 
### 注册一个github帐号,创建blog托管的仓库"
这一步我是跟着简书上的大神做的:[dimsky:5分钟搭建免费个人博客](https://www.jianshu.com/p/4eaddcbe4d12 "jianshu"),其中1.1,1.2就是这一过程. 
# 博客生成阶段 
这一步为博客生成阶段,也是本文的核心.具体过程是从dockerhub上下载一个hexo镜像(image),并用该镜像生成容器(container),再用这个容器完成博客的生成,部署等工作. 上述过程都是在terminal里完成,对于像我一样才接触programming的小白来说,看见各种命令都懵了.因此在这儿贴出[docker](https://docs.docker.com/engine/reference/commandline/run/ "docker_CLI")和[hexo](https://hexo.io/zh-cn/docs/commands "hexo_command")的操作命令,下面所有的命令都能在这两个文档里找到. 
### 从docker hub上拉取(pull)镜像
这一步我踩坑无数次.试了好多都不能用.最后我用的这个镜像:[spurin/hexo](https://hub.docker.com/r/spurin/hexo "image").这个镜像的overview还写得特别详细!特棒! 
下面是命令: 
`sudo docker pull spurin/hexo`
接下来可以通过:
`sudo docker image ls `
来查看image,如下图所示: 
{% asset_img image_title.png %}
{% asset_img image_detail.png %}
### 运行docker 容器(参考spurin/hexo overview)
`sudo docker create --name=hexo-domain.com  -e HEXO_SERVER_PORT=4000  -v /blog/domain.com:/app  -p 4000:4000 spurin/hexo `
这儿的-v /blog/domain.com:/app 代表将本地地址映射到容器的app地址.通过查看官方文档: 
{% asset_img run.png %}
解释很形象:将一卷东西钉在一起,也就是说将/blog/domain.com和/app这俩地址钉在一起. 这儿 -p 4000:4000 表示将容器4000端口映射到本地4000端口. 
### 开启容器:
`sudo docker start hexo-domain.com `
开启容器后,可以通过: 
`sudo docker container ls`
来查看. 此时打开刚刚自己设的文件地址/blog/domain.com,可以看见以下文件夹: 
{% asset_img blog/dir.png %}
### 更改博客设置 
在刚刚的文件中,找到_config.yml并打开,根据需求修改博客设置,这儿参考hexo文档中[配置](https://hexo.io/zh-cn/docs/configuration "configure")部分. 
提示: 本容器默认的deployment部分长这样: 
deploy: 
type:
需要自行补充三行: 
deploy: 
type: git 
repo: https://github.com/username/username.github.io.git
branch: master 
message: 
### 更改主题 
如需更改主题,参考spurin/hexo overview中 install a theme部分. 
### 安装hexo-deployer-git 
`sudo docker exec -it hexo-domain.com npm install hexo-deployer-git --save `
我当时在这儿卡了好久.上一步在_config.yml中将deploy type 改为了git,在下一步部署中terminal一直提示找不到git,原因就是少了这一步. 
### 生成静态网页并部署到github上 
`sudo docker exec -it hexo-domain.com hexo generate`
`sudo docker exec -it hexo-domain.com hexo deploy`
第二条命令后会让输入github的用户名跟密码. 此时登录github,找到刚刚建立的blog托管仓库,就能看见博客的东西啦! 
# 创建github page 
我们的终极目标是通过 https://usename.github.io 能够访问博客,这就需要通过[github page](https://pages.github.com/ "github_page")为博客创建站点(site).这是我的理解,可能不太准确. 首先进入github 装blog的仓库里,点击setting,如图: 
{% asset_img git_rep_setting.png %}
setting在右下角. 接着找到github page部分,先选择一个主题,接着把source选为master(与_config.yml中保持一致).hexo官文中推荐用published,但github page上显示个人只能用master. 
{% asset_img git_page.png %}
大功告成,现在应该就可以通过 https://usename.github.io 能够访问博客啦!祝好运!



