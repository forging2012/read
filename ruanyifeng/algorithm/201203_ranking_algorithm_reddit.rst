.. _201203_ranking_algorithm_reddit:

基于用户投票的排名算法（二）：Reddit
=======================================================

`ruanyifeng.com <http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_reddit.html>`__

（不好意思，这个系列中断了近两周，我会尽快在这几天，把后面几篇写完。）

上一次，我介绍了\ `Hacker
News <http://www.ruanyifeng.com/blog/2012/02/ranking_algorithm_hacker_news.html>`__\ 的排名算法。它的特点是用户只能投赞成票，但是很多网站还允许用户投反对票。就是说，除了好评以外，你还可以给某篇文章差评。

`Reddit <http://www.reddit.com/>`__\ 是美国最大的网上社区，它的每个帖子前面都有向上和向下的箭头，分别表示”赞成”和”反对”。用户点击进行投票，Reddit根据投票结果，计算出最新的”热点文章排行榜”。

怎样才能将赞成票和反对票结合起来，计算出一段时间内最受欢迎的文章呢？如果文章A有100张赞成票、5张反对票，文章B有1000张赞成票、950张反对票，谁应该排在前面呢？

Reddit的程序是\ `开源 <https://github.com/reddit/reddit>`__\ 的，使用Python语言编写。排名算法的\ `代码 <http://pastebin.com/bygavdb7>`__\ 大致如下：

这段代码考虑了这样几个因素：

（1）帖子的新旧程度t

    　　t = 发贴时间 - 2005年12月8日7:46:43

t的单位为秒，用unix时间戳计算。不难看出，一旦帖子发表，t就是固定值，不会随时间改变，而且帖子越新，t值越大。至于2005年12月8日，应该是Reddit成立的时间。

（2）赞成票与反对票的差x

    　　x = 赞成票 - 反对票

（3）投票方向y

　　|image0|

y是一个符号变量，表示对文章的总体看法。如果赞成票居多，y就是+1；如果反对票居多，y就是-1；如果赞成票和反对票相等，y就是0。

（4）帖子的受肯定（否定）的程度z

　　

z表示赞成票与反对票之间差额的绝对值。如果对某个帖子的评价，越是一边倒，z就越大。如果赞成票等于反对票，z就等于1。

结合以上几个变量，Reddit的最终得分计算公式如下：

　　|image1|

这个公式可以分成两个部分来讨论：

（一）

　　|image2|

这个部分表示，赞成票与反对票的差额z越大，得分越高。

需要注意的是，这里用的是以10为底的对数，意味着z=10可以得到1分，z=100可以得到2分。也就是说，前10个投票人与后90个投票人（乃至再后面900个投票人）的权重是一样的，即如果一个帖子特别受到欢迎，那么越到后面投赞成票，对得分越不会产生影响。

当赞成票等于反对票，z=1，因此这个部分等于0，也就是不产生得分。

（二）

　　|image3|

这个部分表示，t越大，得分越高，即新帖子的得分会高于老帖子。它起到自动将老帖子的排名往下拉的作用。

分母的45000秒，等于12.5个小时，也就是说，后一天的帖子会比前一天的帖子多得2分。结合前一部分，可以得到结论，如果前一天的帖子在第二天还想保持原先的排名，在这一天里面，它的z值必须增加100倍（净赞成票增加100倍）。

y的作用是产生加分或减分。当赞成票超过反对票时，这一部分为正，起到加分作用；当赞成票少于反对票时，这一部分为负，起到减分作用；当两者相等，这一部分为0。这就保证了得到大量净赞成票的文章，会排在前列；赞成票与反对票接近或相等的文章，会排在后面；得到净反对票的文章，会排在最后（因为得分是负值）。

（三）

这种算法的一个问题是，对于那些有争议的文章（赞成票和反对票非常接近），它们不可能排到前列。假定同一时间有两个帖子发表，文章A有1张赞成票（发帖人投的）、0张反对票，文章B有1000张赞成票、1000张反对票，那么A的排名会高于B，这显然不合理。

**结论就是，Reddit的排名，基本上由发帖时间决定，超级受欢迎的文章会排在最前面，一般性受欢迎的文章、有争议的文章都不会很靠前。**\ 这决定了Reddit是一个符合大众口味的社区，不是一个很激进、可以展示少数派想法的地方。

[参考资料]

　　\* `How Reddit ranking algorithms
work <http://amix.dk/blog/post/19588>`__

（完）

.. note::
    原文地址: http://www.ruanyifeng.com/blog/2012/03/ranking_algorithm_reddit.html 
    作者: 阮一峰 

    编辑: 木书架 http://www.me115.com