---
title: typora+PicGo+腾讯云COS实现图片一键上传
date: 2021-12-18 20:30:04
tags:
  - 腾讯云
  - typora
categories: 杂七杂八
---

# typora+PicGo+腾讯云COS实现图片一键上传

typora应该是很多人都会使用的一个记笔记软件，支持markdown，体验感极佳

但是在分享md文件时，往往出现md内的图片裂开，这是因为引用的是md作者电脑的本地文件，所以别人无法查看到图片

所以可以使用PicGo+腾讯云COS实现图片上传功能

## PicGo下载

picGo下载地址 https://github.com/Molunerfinn/PicGo/releases

![image-20211218203728293](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218203728293.png)

## 图床配置

![image-20211218203827673](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218203827673.png)

1. 目前要选择V5版本（2021-12-18）

2. 创建存储桶

   ![image-20211218204119166](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204119166.png)



一定要选**公有读私有写**

![image-20211218204208131](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204208131.png)

存储桶的名称对应存储空间名，地域英文对应确认存储区域

![image-20211218204339907](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204339907.png)

设置访问管理

![image-20211218204516432](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204516432.png)

![image-20211218204536215](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204536215.png)





新建秘钥

![image-20211218204608639](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204608639.png)

APPID和SecretId、SecretKey都有，对应填上去

保存、**设置为默认地址**

## typora设置

![image-20211218204731601](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204731601.png)

现在上传图片就会自动上传到图床，图片地址也变成图床地址

![image-20211218204836857](https://larmy-1303020690.cos.ap-guangzhou.myqcloud.com/image-20211218204836857.png)
