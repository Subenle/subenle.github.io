---
layout: post
#标题配置
title:  iOS App安装时证书失效
#时间配置
date:   2022-05-17 10:00:00 +0800
#大类配置
categories: iOS
#小类配置
tag: 证书,安装
---

* content
{:toc}


编译运行ios App时，最后时刻遇到证书失效的问题，弹窗信息如下：  
The certificate used to sign "xxx" has either expired or has been revoked.  
The identity used to sign the executable is no longer valid.

尝试了各种证书重置操作都没作用，但用同一个账号签名的新建项目的运行却没有问题。

广泛检索后发现：

在 Xcode 的 DerivedData 目录下「/Users/yourUsername/Library/Developer/Xcode/DerivedData/」，有两个目录对应同一个项目，只是目录后的哈希值不同。比如：  
proj-aaaaaaaaa  
proj-bbbbbbbbbb 

解决方案：  
1. 退出Xcode
2. 删除如上两个目录
3. 重新打开Xcode，正常编译即可

参考链接：[identity-no-longer-vald](https://stackoverflow.com/questions/31484742/0xe8008018-the-identity-used-to-sign-the-executable-is-no-longer-valid)
