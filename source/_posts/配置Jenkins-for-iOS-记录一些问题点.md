---
title: 配置Jenkins for iOS 记录一些问题点
date: 2016-09-26 17:24:46
tags: Jenkins
---

## 1 源码管理

*  Git源码管理问题	

		Repository URL	添加完git地址之后需要再添加一个ssh的私钥
	
*  超时问题：

		 将Timeout (in minutes) for clone and fetch operations噶为60 默认为10 如果项目大的话可能会超时

![](/images/sourceManager.png)


## 2 构建触发器

* 这一步比较简单 按固定格式填写就行

		比如：
		 Build periodically H 20 * * *
		 Poll SCM H/5 * * * *

![](/images/BuildTrigger.png)		
		
		
## 3 构建环境

   * 这个需要配置证书和描述文件 以及输出路径  配置证书这部分就不细说了 网上很多资料
   
![](/images/BuildEnv.png)	

* [手把手教你利用Jenkins持续集成iOS项目](http://www.jianshu.com/p/41ecb06ae95f)

* [CI第一篇:Jenkins+github->fir.im/蒲公英pgyer.com](http://www.jianshu.com/p/a501153c9d59)

* [CI第二篇 集成项目(SVN)workspace编译生成ipa到fir-蒲公英(jenkins)](http://www.jianshu.com/p/12e445c0d4ee)

* [Jenkins+GitHub+Xcode+fir搭了一个持续集成环境](http://www.jianshu.com/p/a17167274463)

	
## 4 构建
	
* 有一个小坑 

	针对非cocoapods项目 就是如果你的项目结构比较复杂 在build的时候可能会出现error
	
		xcodebuild: error: '/Users/Shared/Jenkins/Home/jobs/Jiuai/workspace/YouXian/YouXian' is not a project file.
		
	这个问题是由于你	Xcode Project File 这个选项没有指定xcodeproj导致的 将Xcode Project File设置成你的xcodeproj文件路径
	
		${WORKSPACE}/YouXian/YouXian.xcodeproj
	
![](/images/Advanced Xcode build options.png)

	
## 5 构建后操作

* 生成ipa之后可以利用fir或者pgyer上传 填好Token就行了
* 要是有能力写脚本也行


## 6 The following build commands failed: Check dependencies

	
* Jenkins 集成Xcode 项目的时候在证书上遇到了问题
	
	    Check dependencies
		 Code Sign error: No codesigning identities found: No codesigning identities (i.e. certificate and private key pairs) 		 that 	
		 match the provisioning profile specified in your build settings (“qingyunDeveloper”) were found.

* 如果遇到类似的错误 解决办法：

		1 打开keychain keys 找到apple 的开发者证书。然后复制。 
		 2 选择左边的系统（system）把刚复制的证书放进去。
		
* 如果这个还没有解决 接下来第二步：

		1 找到你用户下的Provisioning Profiles 文件。目录为 /Users/xxx/Library/MobileDevice/Provisioning Profiles  
		 xxx表示你自己的用户名
		 2 把这里面所有的证书复制到/Users/Shared/Jenkins/Library/MobileDevice/Provisioning Profile 这个文件夹下


* 最后提示
		
		iPhone.build/ProjectCos.xcent /Users/chenqing/Sqy/iOSProject/cyou/Svn/Cos/CosXCode_lxh/build/ProjectCos.app

		 ** BUILD SUCCEEDED **

		 Finished: SUCCESS
	
	[链接： Jenkins Xcode 证书设置错误 Code Sign error: No matching codesigning identity found: No codesigning identities
](http://www.cnblogs.com/qingjoin/p/3929493.html)


## 7 注意点

* 关于证书问题还有一个注意点 
		
		如果你是一个测试项目 
		 在本地配置好证书和描述文件后一定要上传到github或者服务器上 
		 然后在进行构建 否则肯定会构建失败的  
		 因为jenkins是拉取你远端代码仓库的最新代码 
		 如果只在本地配置不上传 必然构建失败 
		 这也是一个比较基础的问题 

	