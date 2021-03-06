---
title: 新Mac 记一次修改shadowsocks过程
date: 2016-08-25 18:29:55
tags: shadowsocks
---
>  **记住 确认是否已经登录服务器了 以下操作基本都是基于登录状态** 

### 1.使用ssh生成秘钥


     ssh-keygen -t rsa -C "your_email@example.com"
     
     
* 然后会提示输入密钥的名字 比如输入 oktang
* 注意要避免重复命名
* 还要求输入私钥密码 password

### 2.配置本地ssh登录 需要修改config文件 添加服务器 用户名和端口信息

     Host vultr
          HostName 0.0.0.0 //自己的服务器地址
          User user  //服务器用户名
          Port 1080  //连接服务器的端口
          IdentityFile ~/.ssh/oktang //私钥路径

### 3.将这个公钥*oktang.pub*添加到服务器上的authorization.keys中
     
* authorization.keys这个文件如果没有就新生成一个
* 使用ssh登录到服务器
		
		ssh oktang
		 cd ~/.ssh
	     vi authorized_keys
	    
* 将之前生成的公钥内容拷贝到authorized_keys中
* ok

### 4.如果需要修改端口 使用ssh登录服务器

修改shadow socks中的端口 或者密码什么的

    vi /etc/shadowsocks.json
    
    单一账号配置
     {
         "server":"0.0.0.0”, //这里不用绑定你的服务器
         "server_port”:port, //这个是shadow socks端口 到时候客户端需要用的
         "local_address": "127.0.0.1",
         "local_port":1080,
         "password”:"password”,//shadowsocks密码
         "timeout":300,
         "method":"aes-256-cfb",
         "fast_open": false
     }
     
     多账号配置
     {
         "server":"0.0.0.0", //这里不用绑定你的服务器
         "local_address": "127.0.0.1",
         "local_port":1080,
         "port_password":{
            "port1":"password1",
            "port2":"password2",
            "port3":"password3"
         },
         "timeout":300,
         "method":"aes-256-cfb",
         "fast_open": false
     }
* 修改“server_port”:12345 即可

* 修改登录端口

		cd /etc/ssh
		
	     vi sshd_config 
	    
* 修改sshd_config中的Port端口 这个端口需要和ssh登录的端口一致 如果不一致ssh将会登录不了


### 5.配置完成后 可以添加shadowsocks环境


#### ** 在已经登录服务器的情况下 执行 **



		sudo pip install shadowsocks
	

** 在没有/etc/shadowsocks.json配置文件的情况下  **

		ssserver -h //查看所有参数

		 ssserver -p 443 -k password -m aes-256-cfb //前台运行，443为服务器端口，password为密码，修改这两项即可

		 sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start //后台运行方法，修改同上

		 sudo ssserver -d stop //停止运行

		 sudo less /var/log/shadowsocks.log //检查日志
	
** vi /etc/shadowsocks.json **
	
	{
	    "server":"my_server_ip",
	    "server_port":8388,
	    "local_address": "127.0.0.1",
	    "local_port":1080,
	    "password":"mypassword",
	    "timeout":300,
	    "method":"aes-256-cfb",
	    "fast_open": false
    }
	    
		ssserver -c /etc/shadowsocks.json //前台运行

		ssserver -c /etc/shadowsocks.json -d start //后台运行

		ssserver -c /etc/shadowsocks.json -d stop //停止


### 6 几项检查命令 和一些error
	 netstat -antpl|grep -i listen //查看服务器运行状态

	  chmod 600 ~/.ssh/authorized_keys //记住要设置文件为可读可写rw
	
      ll ~/.ssh //查看文件的权限
	
 **[Error 98] Address already in use**
 
	 这个error应该是端口被占用了 修改shadowsocks.json中的端口
	 
	  切记shadowsocks.json中的端口不要和ssh登录端口一样 这是不一样的
	 
**Permission denied(publishkey,gssapi-keyex,gssapi-with-mic)**

	使用ssh登录服务器报错 这个错误是公钥没有添加在authorized_keys中 
	
     加进去就行

### 7 如果以上都成功了 还不能翻墙 
    请先检查一下自己是否开启了shadowsocks翻墙了 如果有关掉 然后重试
     玩的开心
     WTF GFW

     
### 2017-10-09 更新
之前的东京服务器被封了 于是又购买了一个新加坡的 遇到个小坑 CentOS 7 需要开放防火墙 shadowsocks 服务端口，检查 VPS 供应商是设置的 iptables 还是 firewall，我的是 firewall，所以需要添加一条，然后重新加载一下就好了：
        
    firewall-cmd --permanent --add-port=8388/tcp //8388改成自己设置的端口号
     firewall-cmd --reload

将端口改成自己配置的端口，也就是上面的8388

最后，启动ShadowSocks服务即可

    ssserver -c /etc/shadowsocks.json -d restart


