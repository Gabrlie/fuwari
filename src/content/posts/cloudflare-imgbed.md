---
title: 免费在线图床 CloudFlare ImgBed
published: 2025-07-30
description: '使用CloudFlare ImgBed + Cloudflare R2 就等于羊毛逮着一只羊薅，不过tg bot无限存储与无限流量也确实很香'
image: 'https://img.gabrlie.top/2025/07/b0beba80954255d6039e847e7e9c325c.png'
tags: ['Cloudflare', 'EdgeOne', '图床']
category: '教程'
draft: false 
lang: ''
---
> 赛博大善人CloudFlare有一个叫做CloudFlare R2的对象存储服务，它不会限速，出口流量也不进行任何收费，只会对存储容量收费。存储的话 10G 以内是免费的，对于我们博客的存储来说完全是足够了，不够的话再增加存储空间也很便宜。

正常自己搭建在线图床也是需要一个服务器的，当然可以使用claw.cloud的免费服务白嫖alist来上传，也可以用其他在线图床或者PicGo手动上传

我们这次使用CloudFlare ImgBed来作为在线图床

## CloudFlare ImgBed部署

> 这是一个免费文件托管解决方案，具有**上传**、**管理**、**读取**、**删除**等全链路功能，覆盖文件全生命周期，支持**鉴权**、**目录**、**图片审查**、**随机图**等各项特性
> 具体文档可以前往[CloudFlare-ImgBed](https://github.com/MarSeventh/CloudFlare-ImgBed)进行查看

它支持以pages或者docker方式进行部署，也支持以下多种存储空间

| 渠道类型      | 优点                       | 限制                                    |
| :------------ | :------------------------- | :-------------------------------------- |
| Telegram Bot  | 完全免费、无限容量         | 大于20MB文件需分片存储                  |
| Cloudflare R2 | 无文件大小限制、企业级性能 | 超出10G免费额度后收费，需要绑定支付方式 |
| S3 兼容存储   | 选择多样、价格灵活         | 根据服务商定价                          |

Telegram Bot免费且无限量。Cloudflare R2有10G额度，但出口带宽无上限。

所以我们这次两个存储方式都会讲，并且同时带上cloudflare pages以及edgeone pages的两种部署方式

## Cloudflare + R2

### 前期准备

- 一个CloudFlare的账号
- 托管到CloudFlare的域名
- 一个github的账号用于fork项目源码

### fork 项目源码

[源码地址](https://github.com/MarSeventh/CloudFlare-ImgBed)

![image-20250730170550351](https://img.gabrlie.top/2025/07/17daa0580e6d6356dc18ffbec6c70464.png)

![image-20250730170628992](https://img.gabrlie.top/2025/07/df11bcb4f9379f58d95cc7552c656225.png)

### 创建R2 存储桶

首先创建一个存储桶

![image-20250730162717892](https://img.gabrlie.top/2025/07/89763e7f9267be247abdd3c9c60ca817.png)

![image-20250730162920291](https://img.gabrlie.top/2025/07/fa8ed6d7ec81f1d63bc8fdf483654b44.png)

### 创建KV存储

imgbed还需要使用kv存储来存放设置等信息，可以把kv理解为是一个小型数据库

点击按钮输入名称创建完毕后就不用管了，可以继续创建项目

![image-20250730164509699](https://img.gabrlie.top/2025/07/4fb72edf06700baca5cc6d53df368261.png)

### 部署项目

通过连接github账号选择对应的代码库来创建创建一个Pages项目

![image-20250730164813958](https://img.gabrlie.top/2025/07/a0128cf46a4231ffe170e67b7e7ec9bd.png)

![image-20250730164902324](https://img.gabrlie.top/2025/07/01c99fe1dd5098043b4801150eddf7b6.png)

授权github账号后选中之前fork的仓库

![image-20250730190826185](https://img.gabrlie.top/2025/07/81c411723835d9e712aa78506bcb48d5.png)

![image-20250730190929368](https://img.gabrlie.top/2025/07/f28a7140bdea5473551d3c61df2ad933.png)

项目名称随意，构建命令 `npm install`，其它默认即可

![image-20250730191204266](https://img.gabrlie.top/2025/07/99aa449b6c931577a8cfda83afd89c6d.png)

部署完成后显示的链接国内无法访问，问题不大，后面会配置自己的域名
![image-20250730191512087](https://img.gabrlie.top/2025/07/da567acfd00098b91614c5da2632dcd7.png)

进入设置绑定之前创建好的R2与KV，两个存储资源的变量名称需分别为img_url与img_r2，不然项目无法识别

![image-20250730191557859](https://img.gabrlie.top/2025/07/98a1e315ced37b10ce6de6c58c1c24bb.png)

![image-20250730170034349](C:\Users\13761\AppData\Roaming\Typora\typora-user-images\image-20250730170034349.png)

![image-20250730192107769](https://img.gabrlie.top/2025/07/f8036d195641cd4a742998570cdea691.png)

![image-20250730192231459](https://img.gabrlie.top/2025/07/e5d486c7219634e8f2bf80e3d5914cb9.png)

重新部署一遍以启用刚刚绑定的资源
![image-20250730191725968](https://img.gabrlie.top/2025/07/b4c3c16d2427fe76d8c1de71e7026478.png)

这时候用默认分配的域名就可以访问到网站了，但是国内无法正常访问，我们需要用自己的域名
![image-20250730192620350](https://img.gabrlie.top/2025/07/c1aa90339e9eb5d4ceed50f289c975c8.png)

输入自己的域名，如果是cf托管的会自动帮你设置dns，与r2存储的域名不能是一样的

![image-20250730192706433](https://img.gabrlie.top/2025/07/4fc630836dbfdf2bc744349dd3e4fa90.png)

部署完就可以正常通过刚刚的域名访问了，设置什么的也可以通过kv正常保存，并且能正常使用了。建议设置一个上传密码，避免所有人都可以拿你的图床进行上传，这样10G空间都不够用的。

![image-20250730193038544](https://img.gabrlie.top/2025/07/097b3bc1adceadc18702cf764cc26fbe.png)

![image-20250730201213743](https://img.gabrlie.top/2025/07/b0beba80954255d6039e847e7e9c325c.png)

## EdgeOne + TG Bot

### 前期准备

- github账号一个 （用于fork仓库）
- edgeone免费或付费套餐，还没有的可以通过[Speed Test to Grab More EdgeOneFree Plans](https://edgeone.ai/zh/get-free-plan) 测个速白嫖两份套餐
- edgeone pages kv存储名额
- telegram账号一个

### 创建TG Bot

搜索@BotFather机器人，点击下方这个Open可以打开可视化界面

![image-20250730203616888](https://img.gabrlie.top/2025/07/fd0145d3a575a81abfb8be060a5548cf.png)

![image-20250730203658996](https://img.gabrlie.top/2025/07/95069f6c4ea29405cc3fb04a5d4802b1.png)

根据指引创建机器人

![image-20250730203920588](https://img.gabrlie.top/2025/07/ae5319202003ac6f2434aadc9cdf8197.png)

保存好机器人的token，点击copy复制

![image-20250730204022411](https://img.gabrlie.top/2025/07/5e70bca9f69000b1085a6c5dc660c41b.png)

创建一个新频道，把刚刚创建的机器人拉进群并设置为管理员，同时将群转为私有

![image-20250730204158889](https://img.gabrlie.top/2025/07/36ffff66dc950c6b1ea0fb18b971b62f.png)



![image-20250730204502247](https://img.gabrlie.top/2025/07/79c48f7bbb91eec711657166355a6293.png)

![image-20250730204906260](https://img.gabrlie.top/2025/07/299e068afd7149eb7f99a862f49241fb.png)

随便发一条消息并将其转发给@VersaToolsBot，记住这个聊天名称

![image-20250730205145965](https://img.gabrlie.top/2025/07/ce5c7a7fcb63a4da2a148ce5ca79a858.png)

![image-20250730205247276](https://img.gabrlie.top/2025/07/f3d0b45a83dc6d86abf789277754f5f2.png)

### 部署项目

在服务总览的标签页中可以看到pages的选项卡，导入并创建项目，fork过程上面讲过了，故省略。构建命令还是为`npm install`
![image-20250730202052094](https://img.gabrlie.top/2025/07/16fbdea91f0053510878c8900542d0e1.png)

 ![image-20250730202111211](https://img.gabrlie.top/2025/07/96670948e53f9f62fc038073b9ded3f4.png)

![image-20250730205600982](https://img.gabrlie.top/2025/07/e26b2142d42e9ff05410bc8a156aa40b.png)

edgeone的kv存储还没申请到，所以这里无法展示，但流程和cf一致，绑定好kv后重新构建一下

然后也可以使用环境变量的方式设置用户名密码以及token与id

``````环境变量
AUTH_CODE=上传页的鉴权密码
BASIC_USER=管理员账号
BASIC_PASS=管理员密码
TG_Bot_Token=机器人的token
TG_Chat_ID=频道ID
``````

![image-20250730205424887](https://img.gabrlie.top/2025/07/da709a18af43d8789f19a2960e4d93d4.png)

设置完成以后等待部署完成就可以通过域名访问了

## 总结

以上就是使用pages部署imgbot的方式了，edgeone的kv存储没有申请到还是很遗憾的，不过也可以使用代理cf的方式来对国内网络进行加速，不过值得注意的是这里的加速回源配置需要设置为使用源站域名，然后在cf对应pages的自定义域中添加加速域名就可以进行加速了。

![image-20250730212318675](https://img.gabrlie.top/2025/07/f34e4f8e5912513fa6e65bd434187ef9.png)

