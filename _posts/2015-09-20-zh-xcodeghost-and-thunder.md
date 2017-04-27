---
layout: post
title: 为什么我觉得 XCODEGHOST 事件跟迅雷无关
---

事情来源于乌云的第一篇公开博文，提到有人评论称：
“还是不能相信迅雷，我是把官网上的下载URL复制到迅雷里下载的，还是中招了。”
此后一些著名的评论人比如 月光博客 认为大面积感染是因为迅雷导致。
后继的言论中，不断出现对迅雷质疑，很多人指出迅雷没有做 MD5（或者其他 HASH）检测。

迅雷的主要 HASH 函数：GCID
----
GCID 不是迅雷的原创 HASH 函数，GCID 其实是基于 SHA1 的分段 HASH 技术。这样的技术在 P2P 文件共享中广泛使用。
如 ED2K 就使用的是分段 MD4，而 BT 种子里面使用了跟 GCID 非常相似的 SHA1 的分段 HASH 技术。
GCID 的原理是将文件分割为 256K 分别做 SHA1，再将最后结果合并再做 SHA1。GCID 具有极低的碰撞概率（远低于 MD5），配合文件大小和来源 URL，有很好的识别能力。
迅雷服务器会保留所有 256K 的 SHA1，服务器上能够迅速地将 GCID 转化为记录好的分段 SHA1，每个下载分片都会检测。
SHA1 目前无可攻击算法，更没有每个分片和总分片均一致的攻击算法。
所以说，迅雷用了 HASH 检测，而且非常严格，HASH 对比的对象是服务器上的记录。用迅雷下载的文件，绝对跟服务器上的记录一样。

迅雷的文件猜测函数：CID
----
那另一个问题就是，有没有可能服务器上的记录误以为 XCODEGHOST 才是原版的 XCODE？
的确有这个可能性，但是可能性非常低，需要涉及迅雷另一个技术 CID。
CID 其实是个采样 HASH 函数，迅雷通过 CID 样本来猜测你所下载的文件，CID 是有广泛的碰撞可能性的，特别是大文件有传输错误的时候。
如果两个文件 CID 和 大小一致，的确有可能欺骗服务器，但那是很多年前，实际上目前迅雷服务器处理这类问题非常谨慎。来源 URL 的可靠性，下载的次数，下载的账户都会被参考，无法通过简单的方式骗过服务器，大部分情况会保险的提示无法使用迅雷加速。
而在 XCODE 这个 CASE 上是不可能利用 CID 碰撞欺骗的。
1. XCODEGHOST 和 XCODE 并不具有一样的 CID，其实是完全重新打包。
2. 是 Apple 官网的 URL 必定有着很高的优先权。
3. 是 Apple 官网的 XCODE 源必定比 XCODEGHOST 要早。
这三个条件任意一个都可以保证迅雷不会犯错，而实际上三个都满足。

关于 MD5
----
相关评论里大部分人会直接想到 MD5，但非常不幸的是 MD5 在 2004 年被发现了可攻击的碰撞算法。
注意是 “可攻击的” 就是说不但可以碰撞，还具有指定内容的能力，而且速度还很快。（山东大学王小云在 2004 年公布，后续还公布了一些更快的算法。）
ED2K 选择分段 MD4 因为那时候很早，MD4 其实很慢。
BT 选择了分段 SHA1。可碰撞，但很算法慢，且无攻击能力。
迅雷选择了分段 SHA1，估计是想兼容 BT。
此外 SHA256 等其他 SHA2 算法也是不错的选择。
MD5 是一个很快的算法，但目前来看，在文件传输上单纯使用 MD5 是非常不明智的。

XCODEGHOST
----
XCODEGHOST 事件很显然已经发展成了一个公关事件。所以谁，在什么地方，说了什么，都基本已经不可信了。
新闻编辑 需要一个哗众取宠的猛料。
新闻评论人 则是唯恐天下不乱。
软件公司 在营造一个良好的公关姿态。
而迅雷、百度网盘之类，则希望尽量撇清关系，不给迅雷这次的确是属于躺枪。
（迅雷看到文章后请给我汇款谢谢）