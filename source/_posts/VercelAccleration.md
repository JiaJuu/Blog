---
title: 白嫖Vercel加速GitHub Pages
date: 2021-01-24 22:21:46
tags: Blog
categories: Blog
cover: https://jiajuu.top/2021/01/24/VercelAccleration/VercelCover.jpg
---



> 搭建完Github Pages后，还是忍受不了github pages的速度，实在太太太太慢了啦！┭┮﹏┭┮...
>
> 作为一名优秀的面向搜索引擎编程的程序猿，直接baidu走起~~
>
> 发现很多云服务器都有CDN加速的服务，但是腾讯CDN加速只有120G赠送，然后以后便要收费了，便打消了这想法...
>
> 最后还是被我发现了一个好宝贝(●'◡'●)，就是利用Vercel拉取github上的代码来实现加速。



P.S.	利用Vercel实现github pages加速实现的步骤大致就是导入github仓库，然后把域名的解析指向Vercel要求的IP地址即可，全程傻瓜式操作。



### 在Vercel中导入github仓库

- 进入Vercel官网：[https://vercel.com](https://vercel.com/)
- `右上角sign in` 	->	`continue with github`
- 登录以后就需要你`Import Git Repository`了
- 选择`+ Add github Org or Account`后会跳转至`github官网`授权
- 然后import你的github pages项目即可



### 域名解析指向Vercel的IP地址

- 选项卡选择`Setting`	->	`Domains`
- 填写你的域名后，点击`Add`
- 正常来说会报错，提示你的域名指向错误，需要你去域名解析网站那里重新指向
- 在域名解析管理添加记录：`主机记录：@ 	记录类型：A 	记录值：76.76.21.21`
- 回到Vercel,`refresh`一哈就大功告成了O(∩_∩)O。