---
title: 使用Edgeone边缘函数搭建一个必应每日壁纸api
published: 2025-08-11
description: 'EdgeOne的边缘函数还是一个刚出来的工具，最近才开发出了在线代码编辑的功能，但是潜力还是有的，就是部署能再快点就好了。'
image: 'https://bing.gabrlie.cn'
tags: ['EdgeOne', 'Edge Functions', 'bing', 'wallpaper']
category: '教程'
draft: false 
lang: ''
---
## 简介

 腾讯云边缘函数提供了 EdgeOne 边缘节点的 Serverless 代码执行环境，您只需编写业务函数代码并设置触发规则，无需配置和管理服务器等基础设施，即可在靠近用户的边缘节点上弹性、安全地运行代码。

使用限制：

| **内容**           | **限制**   | **说明**                                                     |
| ------------------ | ---------- | ------------------------------------------------------------ |
| 单站点函数数量     | 100 个     | 单个站点最多支持创建边缘函数个数为100                        |
| 单站点触发规则数量 | 200 条     | 单个站点最多支持创建函数的触发规则数量为200                  |
| 函数名称长度       | 30 字符    | 2~30个字符，最多支持30个字符                                 |
| 代码包大小         | 5 MB       | 单个函数代码包大小最多支持 5 MB                              |
| 请求 body 大小     | 1 MB       | 客户端请求携带 body 最多支持 1 MB                            |
| CPU 时间           | 200 ms     | 函数单次执行分配的 CPU 时间片，不包含 I/O 等待时间           |
| 开发语言           | JavaScript | 目前仅支持 JavaScript                                        |
| console 调用次数   | 20 次      | 每个函数内最多允许调用 20 次 console 方法。超过 20 次后，将不再执行打印操作 |
| 循环执行次数       | 100000 次  | 函数中 `for`、`for in`、`for of`、`while`、`do while` 循环限制执行不超过 100000 次 |

这也就意味着它干的事和CF Worker类似，只是语法存在些许不同，性能也比较羸弱，但是用作简单的请求转发还是没有问题的，所以我们就可以用它搭建一个国内也可以加速访问的必应每日壁纸api了，正好最近我的CF的域名无法正常访问，就直接使用edgeone搭建一个吧。

## 创建函数

点击新建函数，随便点击一个模板

![image-20250812025301155](https://img.gabrlie.cn/2025/08/ea9593e2c59ead93c364e03925e8af93.png)

随便起一个名称，然后将[Gabrlie/BingWallpaperApi: 基于EdgeOne边缘函数的必应每日壁纸api](https://github.com/Gabrlie/BingWallpaperApi)中的worker.js的所有代码全部复制进去，点击创建并部署就可以了

![image-20250812025447170](https://img.gabrlie.cn/2025/08/62a5545f1cf3ef94e504da1d4208f2f6.png)

这个edgeone的边缘函数第一次部署的时间很长，需要耐心等待一下，然后就会给你一个默认的访问域名，这个肯定不是我们想要的，所以需要绑定一下域名。

![image-20250812025601950](https://img.gabrlie.cn/2025/08/11228252be343a6b6db5fd5f62253218.png)

首先需要为这个域名设置一下cdn加速，不使用的话好像无法绑定触发规则。设置好域名加速后还需要在对应函数下设置触发规则，默认会给你一个绑定域名的规则，只需要在值中选中你加速的域名就行了

![image-20250812030009921](https://img.gabrlie.cn/2025/08/ba03fdec66f0f15faf40a00957dccff1.png)

## 使用方法

这个代码包含了api访问模式以及图片访问模式，访问根域名例如[bing.gabrlie.cn](https://bing.gabrlie.cn/) 会直接返回对应的图片，访问api接口则会返回json格式的数据，包含了原本的完整JSON数据，方便开发

### **API模式** - 返回JSON数据

```bash
# 获取今日壁纸信息
GET https://your-domain.com/api/bing-wallpaper

# 获取多张壁纸信息  
GET https://your-domain.com/api/bing-wallpaper?n=8&mkt=zh-CN

# 返回完整JSON数据，包含图片信息、多种分辨率、代理链接等
```



### **图片模式** - 直接返回图片

```bash
# 今日壁纸 (默认1920x1080)
GET https://your-domain.com/

# 4K壁纸
GET https://your-domain.com/?resolution=uhd

# 移动端壁纸  
GET https://your-domain.com/?resolution=mobile

# 昨天的缩略图
GET https://your-domain.com/?idx=1&resolution=thumbnail
```

## 总结

EdgeOne的边缘函数还是一个刚出来的工具，最近才开发出了在线代码编辑的功能，但是潜力还是有的，就是部署能再快点就好了。
