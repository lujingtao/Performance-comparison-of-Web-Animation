# 前言

上一篇《[从chrome开发工具中直观了解浏览器的渲染过程](https://mp.csdn.net/postedit/101295778)》我们介绍了如何通过chorme开发者工具查看浏览器渲染页面过程，这篇文章我们介绍如何观察动画性能。

前端离不开动画效果，例如轮播图、轮盘抽奖、跑马灯等，如果页面涉及
成千上万个元素变化，或者需要面向低端性能设备时，页面的性能优化就至关重要了，本文以最简单的js动画和css动画配合chrome的开发者工具做可视化比较，能直观了解各种方法的性能差异。

# 准备工作
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191009230312527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2lhbWx1amluZ3Rhbw==,size_16,color_FFFFFF,t_70)
如上图，我们准备了一个demo，上面有一个item元素，我们会操作它会从左到右运动；
- 第1组/行动画按钮，是使用js操作left来运动；
- 第2组/行动画按钮，是使用js操作translateX来运动；
- 第3组/行动画按钮，是使用css操作translateX来运动；

# 单帧动画比较
前端性能优化其中有一个说法就是使用translate来代替left，原因是前者能减少重排，真的吗？现在我们测试看看。
我们先使用最简单的一帧动画来测试，分别使用css动画translateX、js动画translateX和left来控制item前进10px。
使用chrome开发者工具中的performance来查看：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010000747303.gif)
- css动画translateX前进10px的渲染过程如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010001000662.png)
- js动画translateX前进10px的渲染过程如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010001016468.png)
- js动画left前进10px的渲染过程如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010001032349.png)
可以看出，css动画translate步骤最多，同时translateX比left少了“Layout（重排）”步骤，理论上比left性能要好。当然这是单帧的，动画关键还是要看连续效果性能。

# 连续动画比较
1、由于现在电脑性能都比较好，所以我们要模拟电脑性能比较低的情况下工作，设置cup为4x：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010001324226.png)

2、打开fps插件观察帧频率，在开发者工具中按ctrl+shift+p，然后搜索fps：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010001341961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2lhbWx1amluZ3Rhbw==,size_16,color_FFFFFF,t_70)

3、1个item体现不出性能瓶颈，我们添加50个item，然后一起运动看看效果，视觉上也基本观察出差别了，当然也可以仔细看fps：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010001901933.gif)


先执行js动画left播放，再执行js动画translate播放，最后执行css动画translate播放，通过performance来观察：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191010002826275.png)
如上图，黄色框部分为fps，绿色柱越低表示帧频率越低（越卡），黄色柱下面的火焰图是cup工作情况，火焰颜色越多表示cup要处理的程序越多，性能越低。
可见，动画性能从高到排序为：
css动画translate > js动画translate > js动画left

# 总结
如果要动画流畅，特别是对于移动端来说：
1、尽量使用css动画
2、使用js动画时，尽量使用css3属性 来代替原始属性（例如translate代替left/top）
3、如有必要，可以使用gpu加速（元素添加3d属性hack -- transform:translateZ(0) ,更多方法和原理可以自行百度）
总的方针是减少页面重排、重绘来提高动画性能。

# GitHub和在线演示
[GitHub地址](https://github.com/lujingtao/Performance-comparison-of-Web-Animation)

[在线演示地址](https://lujingtao.github.io/Performance-comparison-of-Web-Animation/)
