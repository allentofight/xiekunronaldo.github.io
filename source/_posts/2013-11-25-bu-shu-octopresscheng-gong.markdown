---
layout: post
title: "部署octopress成功"
date: 2013-11-25 21:35:14 +0800
comments: true
categories: 
---
今天经过一番探索,按船哥的[博文指引](http://beyondvincent.com/blog/2013/08/03/108-creating-a-github-blog-using-octopress/)，查阅了一些资料后，终于成功的部署了octopress博客系统，成功地写出了博客，用得主题是很流行的[greyshade](https://github.com/shashankmehta/greyshade)主题,整理一下需要注意的几个问题

1. 侧栏social links支持众多社交网站,但不支持微博,可以点[这里](http://www.imallen.com/blog/2013/05/12/add-support-for-weibo-and-dribbble-to-greyshade.html)查看解决方法
2. 写博客时用到的命令rake new_post["title"]，在zsh下无效，会出现`zsh: no matches found`的错误，原因：zsh中若出现‘*’, ‘(’, ‘|’, ‘<’, ‘[’, or ‘?’符号，则将其识别为查找文件名的通配符

	* 快速解决：用引号括起来$ rake "new_post[arch-linux-reinstall-glibc.markdown]"

	* 彻底解决：取消zsh的通配(GLOB), 在.zshrc中加入alias rake="noglob rake"点
	
		参考[这里](http://fancyoung.com/blog/use-octopress-new-post-function-with-zsh/)