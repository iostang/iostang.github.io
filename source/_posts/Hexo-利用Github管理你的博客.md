
---
title: Hexo 利用Github管理博客
date: 2016-08-26 18:49:55
tags: Hexo
---

# Hexo 利用Github管理博客


### 1 搭建流程

>配置环境
安装homebrew node.js git 
申请Github
算了这些就不写了。。。


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

如果想写新的博客或者修改博客 你可以按照这个流程

	1. hexo n "这是新博客的标题"
	2. hexo g
	3. hexo s
	4. git add .
	5. git commit -m "balabala"
	6. git pull origin hexo 
	7. git push origin hexo
	8. hexo d

**ok 这个时候新博客已经部署到Github仓库了**


### 3 如何管理 

那如果我有多台电脑 该如何管理你的博客呢？

	1. git clone git@github.com:iostang/iostang.github.io.git
	2. sudo npm install -g hexo
	3. npm install hexo-renderer-ejs --save
	4. npm install hexo-renderer-stylus --save
	5. npm install hexo-renderer-marked --save
	6. npm install hexo-deployer-git --save

千万不要执行**hexo init**这条指令 否则会覆盖_config.yml

### 4 博客主题
* 有了博客 当然想要一个风格好看你的主题 你可以去[这里](https://hexo.io/themes/)下载你喜欢的主题
* 也可以使用[这个](https://github.com/pinggod/hexo-theme-apollo) 个人还蛮喜欢这种风格
	
		npm install
		npm install --save hexo-renderer-jade hexo-generator-feed hexo-generator-sitemap hexo-browsersync hexo-generator-archive
		git clone https://github.com/pinggod/hexo-theme-apollo.git themes/apollo
		
* 修改 _config.yml 的 theme 配置项为 apollo:

		theme: apollo

主题里面的内容就需要自己去配置了 比如微博账号
    
* 安装主题有一个小问题就是 在你提交到Github的时候 你需要先把该主题目录下的.git文件删掉 不然提交不上去

