---
title: Hexo 利用Github管理你的博客
date: 2016-08-26 16:06:27
tags: Hexo
---


### 1 搭建流程

1. 在github上创建一个repo 起名必须规范 不用使用大写字母 像这样：[iostang.github.io](https://iostang.github.io)
2. 创建两个分支 master · hexo
3. 设置hexo为默认分支
4. 使用git clone git@github.com:iostang/iostang.github.io.git 存放在自己想要管理博客的文件夹
5. 在该目录下 依次使用

		sudo npm install -g hexo
		hexo init
		npm install 
		npm install hexo-deployer-git --save
		
6. 修改_config.yml中的deploy参数

		deploy:
		type: git
		repo: git@github.com:iostang/iostang.github.io.git
		branch: master

7. hexo g 生成网站文件
8. hexo s 这个时候就可以在本地查看博客了[http://localhost:4000](http://localhost:4000/)

	这个时候再点击http://localhost:4000，正常情况下应该是最原始的画面，但是我看到的是：
	白板和Cannot GET / 几个字
	
	原因：由于2.6以后就更新了，我们需要手动配置些东西，我们需要输入下面三行命令：

		npm install hexo-renderer-ejs --save
		npm install hexo-renderer-stylus --save
		npm install hexo-renderer-marked --save
		
	**再次执行hexo g · hexo s**

9. 依次执行 git add . git commit -m "balabala" git push origin hexo 提交相关文件到hexo分支
10. 使用hexo g -d 生成网站并部署到github 注意提交到master分支上




		
### 2 日常维护

### 3 如何管理 