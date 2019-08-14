---
title: LKImageKit 简述
date: 2019-08-14 21:31:17
tags:
---


 
# 简介   

LKImageKit是一个高性能的图片框架，包括了图片控件，图片下载、内存缓存、磁盘缓存、图片解码、图片处理等一系列能力。合理的架构和线程模型，并特别针对不同场景进行优化，能充分发挥硬件的性能。
   
同时，该框架具有高度的扩展性。在此框架下，开发者可以自定义图片框架中的任何一个部分，比如：自定义图片显示逻辑、自定义缓存、自定义下载组件、自定义解码器、自定义图片处理算法等等。

# 结构

### 架构图

![image](http://iostang.github.io/images/LKImageKit_architecture.png)

## 目录结构
   
    Core
        Class
            LKImageView
            LKImageRequest
            LKImageError
            LKImageConfiguration
            LKImageInfo
        Manager
            LKImageDecoderManager
            LKImageLoaderManager
            LKImageManager
            LKImageProcessorManager
            LKImageCacheManager
            LKImageLogManager
        Utility
            LKImageMonitor
            LKImageUtil
        Private
            ...
    Components
        Cache
            LKImageMemoryCache
            LKImageSmartCache
        Loader
            LKImageBundleLoader
            LKImageLocalFileLoader  
            LKImageMemoryImageLoader
            LKImageNetworkFileLoader
            LKImagePhotoKitLoader
        Decoder
            LKImageSystemDecoder
        Processor
            LKImageBlurProcessor
            LKImageGrayProcessor
            LKImagePredrawProcessor
            LKImageSpritesToMutipleImagesProcessor
### 流程图

![image](http://iostang.github.io/images/LKImageKit_flow.png)
       

# 实现细节

### 1 缓存

*内存缓存*
   
    第一：使用LRU+FIFO双队列的改进算法，提高缓存的命中率，解决进入新页面的突发大量图片的缓存污染问题。
    第二：使用缓存模糊匹配算法。对于图片请求，如果发现缓存中有比请求大小更大的图片，则也视为命中缓存。
    第三：使用C++编写缓存，组合链表和哈希表的存储结构，可以把LRU队列的增删查的时间复杂度将为O(1)

*磁盘缓存*

    只有网络图片用 相册图片、本地图片不需要磁盘缓存
    //将url MD5后当中文件名 然后把data写入到 Library/Caches/images/
    NSURL *fileURL = [NSURL fileURLWithPath:[LKImageNetworkFileLoader cacheFilePathForURL:request.keyForLoader]];
    [data writeToURL:fileURL atomically:YES];
      
### 2 图片请求

磁盘缓存IO速度，和图片的下载速度是比较慢的，如果我们在一个图片加载完成之前再发出同样的请求，不可避免的会导致重复的磁盘读取和图片下载。

因此，在此基础上，我们还可以添加一个请求队列，实现对图片请求的去重。对于相同的请求，我们进行合并，在请求完成之后，批量进行回调。这样就可以防止同一个url重复请求导致的多次缓存查询和重复下载问题。

[LKImageLoaderManager imageWithRequest function]


### 3 图片处理

有的图片并不是直接展示，而是需要进行处理，比如套用一个滤镜

因此，我们在原有的架构上再添加一个模块，图像处理模块，一方面解决上述的图片处理的问题。另一方面，在这个模块，对加载的图片进行一次绘制。这是由于iOS的特性，UIImage加载之后并没有立即解码，而是在显示或其他需要的时候解码，我们需要进行一次绘制，强制系统进行解码。

### 4 自定义

自定义图片显示逻辑、自定义缓存、自定义下载组件、自定义解码器、自定义图片处理算法

详情可看:
    
    ExampleFastFileCache.m
    ExampleNetworkFileLoader.m
    ExampleImageURLRequest.m
    LKImageWebPDecoder.m
    

### 多线程 高性能 内存占用优化



## 用法

    //使用LKImageView
    网络图片：imageView.URL = "http://dsadsada.jpg"
    相册图片：imageView.URL = "phasset://" + "图片identifier"
    本地图片：imageView.URL = "file://" + "图片路径"
    沙盒图片：imageView.URL = "图片沙盒地址"
    还可以设置 loadingURL failureURL  用法同上
        
    //或者这样：
    imageView.loadingImage = UIImage(named: "loading")
    imageView.failureImage = UIImage(named: "failure")
    imageView.defaultImage = UIImage(named: "default")
        
    //其他设置
    imageView.effect.blurEnabled = true
    imageView.effect.grayEnabled = false
    imageView.scaleMode = LKImageScaleMode(rawValue: 3)
    imageView.predrawEnabled = true
    imageView.anchorPoint = CGPointMake(100, 5)
    imageView.fadeMode = LKImageViewFadeMode(rawValue: 2)
    
    //渐进式加载
    LKImageURLRequest *request = [LKImageURLRequest requestWithURL: @"http://dsadsadsa.jpg"];
    //开启这个属性即可
    request.supportProgressive = true;
    imageView.request = request;


# 知识点

    1 多线程（gcd信号量 线程锁 独立线程 并发控制 优先级）
    2 图片处理（模糊 灰度 预加载 解码 渐进式加载）
    3 链表 LRU MapTable
    
    
    
# 资料
    
[LRU、LFU、FIFO算法](https://www.jianshu.com/p/2bed0e361b86)
[@Swift开发者大会——如何打造易扩展的高性能图片组件](https://zhuanlan.zhihu.com/p/26955368)
[iOS中的NSHashTable和NSMapTable](https://www.jianshu.com/p/dcd222900fa9)
[关于dispatch_semaphore的使用](http://www.cnblogs.com/snailHL/p/3906112.html)
[NSOperation和NSOperationQueue的一些事儿](https://www.jianshu.com/p/f5eddfe074fe)
[相对于SDWebImage的优势在哪里](https://github.com/Tencent/LKImageKit/issues/5)


