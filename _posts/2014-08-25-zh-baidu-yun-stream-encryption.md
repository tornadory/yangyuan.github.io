---
layout: post
title: 百度云视频流加密新方法初步分析
---

群里说百度云又更新了，上去看了看。

视频流文件改动较大，主要是两个字段
xcode
sign
这两个字段在百度其他产品中也在用，不知道是不是一个东西。

### 目前能看出来的
xcode为哈希值或者 ID 数值的拼接，多段拼接。
其中第一段是长度为 16 的时间 token。（看不出什么特别的，所以姑且认为是一个 token。）
sign为哈希值，从行为来看属于自验证。（也就是服务端取得 sign 之后，用一个不连数据库不联网的小算法就可以验证 sign 是否准确）
sign分为三段，BOUTHNF-长度32哈希-长度40验证hash。

### 猜测
可能性比较大的，依然是使用自验证的方法。也就是说服务端取到xcode和sign之后，跟其他参数进行hash匹配。
“长度40验证hash” 是其他字段拼接数据后的 SHA1，如时间和salt。
“长度32哈希”可能是产品相关信息。
没有充足数据，而且hash很难猜，所以……懒得折腾了。

### 其他
百度云的视频流文件有过两次大更新。
最初的版本可以说毫无防范，利用苹果视频流列表文件，可以保存视频流，随意播放。
后来简单更新了一下，加进了时间验证，和referer验证，但是自定义客户端依然可以随便破解。
这次的改动终于不再是花瓶了，估计得死一片网站了。

### 2014-10-10 补充
网上流传xcode字段被移除，只有一个sign，实测不对，是部分文件没有更新，新的都含有xcode，双重验证。
文件ID大小写情况依然保留，原文件小写，瑕疵文件大写，也不排除是反过来的。
不再分段disk。

然后大致看了下，基本可以确定sign的后段是由几个前段参数（如time和r）的sha1组成，前端是“长度32哈希”是F3530edecde9cd71b79378b290804a96，试了两个帐号一样的，貌似是固定值。
尝试猜测hash原文拼接方式，失败了，但是却发现了一个验证漏洞，哎，百度又2了。