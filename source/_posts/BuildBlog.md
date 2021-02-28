---
title: 搭建github pages
date: 2021-01-24 18:18:45
tags: Blog
categories: Blog
cover: https://jiajuu.top/2021/01/24/BuildBlog/BuildBlogCover.jpg
---
> 因为之前的腾讯云新用户优惠的服务器到期了，续费的话还需要原价购买，如果使用学生优惠的话又需要重新部署一遍。最后想了一想还是决定去`gitee`或者`github`白嫖。开始的时候准备使用`gitee`的，但是部署完以后发现自己的域名解析不了，折腾了许久才发现这是`gitee pages pro`的功能，而且现在不知道为什么已经消失不见了。最后还是无奈选择`github pages`。



### github访问速度

我不想用`github pages`最主要的原因还是因为这网速实在是太堪忧了，经常就直接原地去世。为了改善一下`github`的速度，可以上[ipaddress.com](https://www.ipaddress.com/)查询一下`github.com`和`github.global.ssl.fastly.net`的IP地址，接着添加到本地的host文件`C:\Windows\System32\drivers\etc\hosts`中便可，亲测有些许效果。

```
140.82.114.3	github.com
199.232.69.194	github.global.ssl.fastly.net
```



### github pages搭建前的工作

- 安装`node.js` 
  - [官方网址传送门](https://nodejs.org/en/download/)
  - [环境配置教程](https://www.runoob.com/nodejs/nodejs-install-setup.html)
  
- 安装`git`
  
  - [安装教程](https://www.liaoxuefeng.com/wiki/896043488029600/896067074338496)
  
- 安装cnpm(不是必须过程，只是使用国内源，速度能快得一批)

  `npm install -g cnpm --registry=https://registry.npm.taobao.org`



### 开始搭建

- 安装Hexo

`npm install -g hexo`或者`cnpm install -g hexo`

- 创建Hexo项目

```shell
cd Hexo //跳转到目标文件夹
Hexo init //在目标文件夹创建Hexo项目
```

- 安装主题样式解析

`npm install hexo-renderer-scss --save`

- 安装主题

我的主题是github上的[diaspora](https://github.com/Fechin/hexo-theme-diaspora),教程上面也有，代码核心代码就也就下面这句:

`git clone https://github.com/Fechin/hexo-theme-diaspora.git themes/diaspora`

- 启用主题

在`hexo项目的根目录 -> _config.yml配置文件`中，修改theme为theme: diaspora

```
...
theme: diaspora
...
```



### 本地预览

1. 编译Hexo项目：`hexo g` 

2. 本地运行Hexo项目：`hexo s`

3. 预览：浏览器输入`http://localhost:4000/`即可



### 部署到github

1. `注册`->`登录`->`右上角“+”`->`New repository`

2. 仓库名为`username.github.io`,这个以后就是未来你博客的域名了

3. 在`hexo项目的根目录 -> _config.yml配置文件`中配置

   ```
   deploy:
     type: git
     repo: 复制你的仓库的https链接即可
     branch: master
   ```

4. 安装自动部署发布工具

   `npm install hexo-deployer-git --save`

5. 发布到github上

   部署项目：`hexo d`

   ```
   hexo clean && hexo g && hexo d
   ```




### 绑定域名

- 在`购买域名的控制台`->`域名-`>`域名列表`->`解析`时

  ​	域名记录选择`CNAME`

  ​	解析路线填写`username.github.io.`

- 在`hexo项目的根目录 -> source文件夹`中，新建一个名字叫CANME的文件(不带后缀名),在里面写上你的域名

- 重新部署一下项目，然后等个10分钟就完事鸟



### github图床

> 都白都白嫖了github一个博客了，也不差着再薅一个图床了，对吧!

1. 首先，在`hexo项目的根目录 -> _config.yml配置文件`中

```
...
post_asset_folder: true //设为true以后，每创建一篇文章都会同时创建一个同名文件夹，用来装相对应的图片
...
```

2. ​	图片路径名`https://github用户名.github.io/存储库名/你的图片名.后缀名`

![](https://jiajuu.top/2021/01/24/BuildBlog/dancing.gif)
