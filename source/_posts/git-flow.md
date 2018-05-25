---
title: git-flow
date: 2018-05-25 15:01:12
tags:
---


#git-flow 使用


##安装 git-flow
    
    brew install git-flow

*要了解安装 git-flow 细节，请阅读下面这个文档 [official documentation](https://github.com/petervanderdoes/gitflow/wiki#installing-git-flow)。*

##在项目中设置 git-flow
    
    ➜  GitFlow git:(master) git flow init

    Which branch should be used for bringing forth production releases?
       - master
    Branch name for production releases: [master]
    Branch name for "next release" development: [develop]
    
    How to name your supporting branch prefixes?
    Feature branches? [feature/]
    Bugfix branches? [bugfix/]
    Release branches? [release/]
    Hotfix branches? [hotfix/]
    Support branches? [support/]
    Version tag prefix? []
    Hooks and filters directory? [/Users/tonytong/Desktop/GitFlow/.git/hooks


##功能开发
对于一个开发人员来说，最平常的工作可能就是功能的开发。这就是为什么 git-flow 定义了很多对于功能开发的工作流程，从而来帮助你有组织地完成它。

**Feature 开始开发一个新功能**
    

    ➜  GitFlow git:(develop) git flow feature start test-gitflow
    Switched to a new branch 'feature/test-gitflow'
    
    Summary of actions:
    - A new branch 'feature/test-gitflow' was created, based on 'develop'
    - You are now on branch 'feature/test-gitflow'
    
    Now, start committing on your feature. When done, use:
    
         git flow feature finish test-gitflow
         
    
    上面的 git flow feature start test-gitflow 会基于develop创建一个新的分支test-gitflow 并自动切换到该分支


**Feature 完成一个新功能**
    
    ➜  GitFlow git:(feature/test-gitflow) git flow feature finish test-gitflow
    Switched to branch 'develop'
    Updating 862396e..e367e7c
    Fast-forward
     GitFlow/ViewController.swift | 2 ++
     1 file changed, 2 insertions(+)
    Deleted branch feature/test-gitflow (was e367e7c).
    
    Summary of actions:
    - The feature branch 'feature/test-gitflow' was merged into 'develop'
    - Feature branch 'feature/test-gitflow' has been locally deleted
    - You are now on branch 'develop'

执行feature finish之后会把我们的工作整合到主 “develop” 分支中去 
而且还会删除这个当下已经完成的功能分支 并且自动切换到 “develop” 分支 

**创建Release**

    ➜  GitFlow git:(develop) git flow release start 1.0.0
    Switched to a new branch 'release/1.0.0'
    
    Summary of actions:
    - A new branch 'release/1.0.0' was created, based on 'develop'
    - You are now on branch 'release/1.0.0'
    
    Follow-up actions:
    - Bump the version number now!
    - Start committing last-minute fixes in preparing your release
    - When done, run:
    
         git flow release finish '1.0.0'
         
    
请注意，release 分支是使用版本号命名的。这个命名方案还有一个很好的附带功能，那就是当我们完成了release 后，git-flow 会适当地_自动_去标记那些 release 提交。

有了一个 release 分支，再完成针对 release 版本号的最后准备工作（如果项目里的某些文件需要记录版本号），并且进行最后的编辑。


         
**完成Release**

    ➜  GitFlow git:(release/1.0.0) git flow release finish -m "*release) finish"
    Switched to branch 'master'
    Merge made by the 'recursive' strategy.
     GitFlow/ViewController.swift | 1 +
     1 file changed, 1 insertion(+)
    Already on 'master'
    Switched to branch 'develop'
    Already up to date!
    Merge made by the 'recursive' strategy.
    Deleted branch release/1.0.0 (was 93ae994).
    
    Summary of actions:
    - Release branch 'release/1.0.0' has been merged into 'master'
    - The release was tagged '1.0.0'
    - Release tag '1.0.0' has been back-merged into 'develop'
    - Release branch 'release/1.0.0' has been locally deleted
    - You are now on branch 'develop'


    这个命令会完成如下一系列的操作：

    1 首先，git-flow 会拉取远程仓库，以确保目前是最新的版本。
    2 然后，release 的内容会被合并到 “master” 和 “develop” 两个分支中去，这样不仅产品代码为最新的版本，而且新的功能分支也将基于最新代码。
    3 为便于识别和做历史参考，release 提交会被标记上这个 release 的名字（在我们的例子里是 “1.0.0”）。
    4 清理操作，版本分支会被删除，并且回到 “develop”。
    
    从 Git 的角度来看，release 版本现在已经完成。依据你的设置，
    对 “master” 的提交可能已经触发了你所定义的部署流程，或者你可以通过手动部署，
    来让你的软件产品进入你的用户手中。


**创建Hotfix**

    ➜  GitFlow git:(develop) git flow hotfix start crash-login
    Switched to a new branch 'hotfix/crash-login'
    
    Summary of actions:
    - A new branch 'hotfix/crash-login' was created, based on 'master'
    - You are now on branch 'hotfix/crash-login'
    
    Follow-up actions:
    - Start committing your hot fixes
    - Bump the version number now!
    - When done, run:
    
         git flow hotfix finish 'crash-login'

**完成Hotfix**

    ➜  GitFlow git:(master) git flow hotfix finish crash-login -m "*bugfix) finish"
    Switched to branch 'develop'
    Merge made by the 'recursive' strategy.
     GitFlow/ViewController.swift | 1 +
     1 file changed, 1 insertion(+)
    Deleted branch hotfix/crash-login (was 1330d0b).
    
    Summary of actions:
    - Hotfix branch 'hotfix/crash-login' has been merged into 'master'
    - The hotfix was tagged 'crash-login'
    - Hotfix tag 'crash-login' has been back-merged into 'develop'
    - Hotfix branch 'hotfix/crash-login' has been locally deleted
    - You are now on branch 'develop'


Git-Flow的hotfix分支和release分支有点像，区别在于release分支是由develop分支拉取出来的新分支，而hotfix分支是由master分支拉取出来的新分支，两者最终都会合并入master和develop分支。只不过hotfix用于生产环境中的紧急修复，需要快速响应和修复，减少Code ReView和QA环节的时间（不是说不做，只是说尽量快点完成这两个环节，尽量快点修复，否则大批用户都会受这个bug影响，毕竟是生产环境。）

##提交commit规范

    feat: 新增feature
    fix: 修复bug
    docs: 仅仅修改了文档，比如README, CHANGELOG, CONTRIBUTE等等
    style: 仅仅修改了空格、格式缩进、都好等等，不改变代码逻辑
    refactor: 代码重构，没有加新功能或者修复bug
    perf: 优化相关，比如提升性能、体验
    test: 测试用例，包括单元测试、集成测试等
    chore: 改变构建流程、或者增加依赖库、工具等
    revert: 回滚到上一个版本
    
    eg:
        git commit -m "feat： balabalabalaxxxxxxxxxx"
        
        git commit -am "fix： balabalabalaxxxxxxxxxx"
        
        fix: Add TimeUnit null check test case in Timed #5231
        * Add TimeUnit null check test case in Timed
        * Correct ugly formatting in BasicIntQueueDisposable
        * Reformatting line
        * Add blockingIterable’s negative buffer size fail  test,close #5232
        * Modify BlockingMultiObserver field’s modfier to private,fix #5231
        * Revert style, modifier
        * Remove duplicated test case.
        * Remove no need annotation and variable

##使用流程

如果项目之前没有用过需要初始化一下

    git flow init 

有新的需求了 
    
    git flow feature start order_delete

推到远端 要不队友看不到（如果之前没有的话）
    
    git push origin order_delete

修改了xxx
    
    git commit -m "fix: 修复了xxxxx"

推到远端
    
    git push origin order_delete

过了几天 项目开发完成 测试也通过了
    
    git flow feature finish order_delete

这时候默认会自动删掉order_delete这个分支 并切换到develop

测试通过 准备发包
    
    git flow release start 2.2.5

看看要不要修改版本号什么的。。。 没问题的话
    
    git flow release finish 2.2.5


---

上线了 发现这个版本有bug需要立即修复
    
    git flow hotfix start crashBug
    
    git commit -m "fix: xxxxxx"
    
    git push origin crashBug

再次提测 没问题了就
    
    git flow hotfix finish crashBug

测试通过 准备发包
    
    git flow release start 2.2.5

看看要不要修改版本号什么的。。。 没问题的话
    
    git flow release finish 2.2.5





##问题

**feature是否应该细化到一个个小功能？feature与feature之间有耦合怎么处理？**

这个问题可以看具体情况而定

如果产品的需求耦合性不是很高 功能比较独立 这是建议分拆feature

如果需求本身就是相互依赖 这时候分拆feature会适得其反

##总结

GitFlow有5大分支：
master（主干）
develop（开发）
feature（功能）
release（预发布）
hotfix（热修复）
这里说下release分支，其实正名应该叫发布分支，我为什么叫他预发布分支呢，因为这个release分支并不是真正的发布，他是由develop分支经过多次feature功能迭代后分出来的一个分支，告诉大家这些功能准备的差不多了，可以准备发布了，但是实际上并没有发布。release分支创建好后，应由QA做测试，研发一起联调，然后先发布到测试环境中进行测试，QA如果觉得有问题，可以先提交工单给研发，研发在这个release分支上做小幅度bug修复，如果QA觉得可以使用的时候再由研发完成发布功能，此时release分支上所做的更改会被合并入master和develop分支中,release分支会在合并后被删除


##参考

[GitFlow with SourceTree](https://www.jianshu.com/p/8a3988057d0f)

[git-flow 的工作流程](https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/git-flow)

