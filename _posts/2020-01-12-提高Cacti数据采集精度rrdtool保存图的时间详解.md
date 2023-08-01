---
layout: mypost
title: 提高Cacti数据采集精度rrdtool保存图的时间详解
categories: [Cacti]
---

默认的Cacti监控图形是以日、周、月、年 4个时间，每个时间都可以任意缩放查看，但是大家肯定也发现了， 默认的情况下，日图是每5分钟频率的平均值，周图是30分钟，月图是2小时，年图是1天；这样的话，如果查看一周前的图就会比当时的实际图相差很大，监控值会低不少，查看一月或一年之前的就会相差更大；这里我们来说说怎么解决此问题，或者说是缩小差值。

Cacti主要是通过rrdtool这个工具来绘图的，其实cacti只是个构造比较合理的框架。rrdtool 有一套自己的数据文件供其绘图使用，Cacti主要使用了，rrdtool create, rrdtool graph, rrdtool update 三个功能，rrdtool的数据文件，和一般关系型数据库不同的地方在于，它是环形数据库，一个特别依赖时间的数据库。在前面我们说过了，在实际生产环境中，默认的Cacti图形是以日、周、月、年 4个时间，每个时间都可以任意缩放查看，但是大家应该发现了， 默认的情况下，日图是每5分钟频率的平均值，周图是30分钟，月是2小时，年图是1天，而这些又代表什么意思呢？下面会简单解释一下。

大家在看当天的流量图的时候(5分钟) 会发现，它是5分钟一个点，两个点之间画一条直线的。这样一天下来会有288个点了，这样就形成了一天的流量图。一般情况下，我们每天生成的数据，会一直不停的变化更新，会有N个288，数据文件会变的无限大的。rrd文件就不会这样，之前说过了它是环形数据库，就在于他会合并数据。比如看周图的时候， 你绝对看不了5分钟一个点的，为什么呢？这个就是因为数据被合并了。6个5分钟的点，合并成为周图中的一个点，也就是说，12个5分钟点才画成周图中的一条线。月、年都是依次类推。合并之后是如何取值呢，默认情况下,是取平均的，也就是为什么时间越大，数据越小的原因了，时间越往前推差值就会越大。

既然在当天可以看见5分钟的流量图，那么多久之后就看不了，只能看见30分钟的图了呢？在默认情况下，5分钟的图是保存50小时的，也就是说2天内，你还可以看见那天的5分钟图，比如8月1号的流量图在8月2号还可以看见，到了3号就只能看见30分钟了的。

在【Console】-【Management】-【Data Sources】中，【---rras】下面有 日、周、月、年的循环归档，这个地方就可以决定我们数据保存周期。`保存时间 = 时间 * rows` 。我们来看看默认的Cacti 5分钟的保存时间：基于以上公式，我们算出来 (5 * 600) / 60m = 50h ≈ 2d

其它的时间都可以算出来。既然这样可以算出来的话，我们可以反过来想，我们要保存一个月的5分钟流量。应该怎么写rows呢？`rows = (30d * 24h * 60m) / 5`，这样写好rows之后，在一个月之内，你就可以看见那个月某一天的详细流量了（5分钟平均值）。其中，要注意的是，时间，比如8月1号到8月31号 与8月1号到9月1号 是不同的概念的，到了9月1号才算是过了一个月，Cacti用的是自然月。

参数解疑：

    Steps（步进、步伐）设置为：a
    Rows（行）设置为：b
    Timespan（时间段）设置为：c

这三个数值的关系是，steps 意思是每多少时间绘图一次，timesspan 意思是某时间段、时间跨度，rows 意思是在这个时间跨度里面保存多少个数据记录。

如一天的参数：

    steps设置为1：表示每分钟绘图一次；
    rows设置为1440：表示这一天存储1440行数据；
    Timespan设置为86400：表示一天的时间86400秒

采集精度详细的修改实例，直接参考修改即可：

因为cacti默认5分钟采集一次数据进行绘图，采集精度不高，对要求高精度的采集需求，需要修改默认的采集精度。

1、修改rrdtool的存储大小

    【console】-【Management】-【Data Sources】-【RRAs】

修改模板里面的参数

如下，比如采集一天的数据，按一分钟的精度

    Name设置为：Daily (1 Minute Average)
    Consolidation Functions设置为：max、min、avg和last
    X-Files Factor修改为：0.5
    Steps设置为：1
    Rows设置为：1440
    Timespan设置为：86400

参考模板参数如下：

    Names Steps Rows Timespan**
    Daily (1 Minute Average) 1 1440     86400     
    Weekly (6 Minute Average) 6 1680     604800     
    Monthly (24 Minute Average)     24 1860     2678400     
    Yearly (1 Hour Average) 60 8760     31536000     
    Two Yearly (2 Hour Average) 120 8760     63072000

2、修改数据索引

    【console】-【Management】-【Templates】-【data templates】

    1)在Associated RRA's添加刚刚创建的5个RRD模板
    2)将step设置为60（每60秒绘图一次）
    3)将Heartbeat设置为120（120为超时时间）

3、修改计划任务crontab

    将每5分钟执行一次

        */5 * * * * /usr/bin/php /var/www/html/poller.php > /dev/null 2>&1

    修改为每1分钟执行一次

        */1 * * * * /usr/bin/php /var/www/html/poller.php > /dev/null 2>&1

4、重建采集器缓存

【console】-【System Utilities】-【Rebuild Poller Cache】，刷新一下网页图形就出来了

########################################################################

附加说明：
在Cacti或者其他基于RRDTool的MRTG系统中，我们可能会遇到这样的问题：在修改了graph template和data template，图像仍然显示之前的5分钟的间隔，而不是新的1分钟间隔。

经过一个小研究发现这个问题是由于RRDtool在创建rrd文件的时候对rrd的文件进行了step的设定，但是这个设定是不能被动态更改的，也就是说你不能只修改某个rrd文件的step参数。

    [root@cacti~ rra]# rrdtool info abc_111.rrd | more
    filename = "abc_111.rrd"
    rrd_version = "0003"
    step = 300

这个就需要用到rrdtool dump和restore来对rrd文件进行一些小的修改。

首先用rrdtool dump将rrd库dump成一个xml文件。

    [root@cacti~ rra]# rrdtool dump abc_111.rrd > abc_111.xml

之后用手动编辑的方法修改xml文件中的step值。如本例，可以将300修改成60。
