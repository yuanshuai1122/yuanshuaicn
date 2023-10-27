---
title: AI绘画第三课：图生图入门及应用
date: 2023-10-12 18:49:55.373
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags:
- AI
- 绘画
---

<strong><font style='font-size:24px;'>第三课：图生图入门及应用</font></strong>
<strong><font style='font-size:17px;color:#a5a5a5;'>*觉得笔记不错的可以来个一键三连</font></strong><font style='color:#a5a5a5;'>♡</font>
<font style='color:#a5a5a5;'>更新于2023.7.24</font>
[🚩00:00前言](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=0)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_188260.jpg)

[🚩01:22图生图原理](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=81)
<strong><font style='font-size:24px;'>一、图生图原理</font></strong>
（一） 简单理解（片面）：把一张图片画成另一种模样
<strong>（二）把绘画想法通过语言和图片传递给AI来实现想法</strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_372578.jpg)
[🚩02:43底层原理介绍](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=162)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_168326.jpg)
[🚩03:08图生图基本流程](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=188)
<strong><font style='font-size:24px;'>二、图生图基本流程</font></strong>
<font style='font-size:20px;'>（一）图生图的三个关键步骤</font>：导入图片→书写提示词→参数调整
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_371510.jpg)
<font style='font-size:20px;'>（二）图生图界面介绍</font>
1.主体结构与文生图区别不大
<strong><font style='background-color:#fff359;font-size:18px;'>2.新增</font></strong><font style='background-color:#fff359;'>：</font>导入图片区域和重绘幅度
	[🚩03:30导入图片方式](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=210)
	<strong>导入图片方式：</strong>
	（1）直接拖动图片到对应位置
	（2）单击导入图片区域打开资源管理器选取图片
	[🚩04:22重绘幅度](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=262)
	<strong>重绘幅度：</strong>跟原图有多像
		[🚩05:55参数设置](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=354)
		实现生成漫画效果推荐值：<u>0.6~0.8</u>
		<font style='background-color:#ffffff;color:#a5a5a5;'>太高容易导致画面变形，太低画面看不出效果</font>
[🚩04:01图生图也需要提示词](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=241)
3.图生图也需要提示词<font style='background-color:#fff359;'>（同样重要）</font>
		[🚩05:06](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=306)
	<font style='color:#a5a5a5;'>（1）</font><font style='color:#a5a5a5;background-color:#ffffff;'>没有提示词，AI只会提取图片信息，get不到画面里的具体内容</font>
	<font style='color:#a5a5a5;'>（2）除了加入图片内容提示词外，为了把控质量还需要加入标准化提示词，如第二节课的两段“魔咒”、其他符合自己需求的提示词</font>
[🚩06:04分辨率设置](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=364)
<font style='color:#000000;'>4.默认下使用与上传图片的原始尺寸一样的分辨率</font>
<font style='color:#a5a5a5;'>如果原始图片分辨率过大可以适当缩小到不会爆显存的分辨率</font>
5.生成其他尺寸的图片，推荐在电脑上用其他软件裁切成预想的比例再导入
[🚩06:41缩放模式](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=400)
6.缩放模式：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_268693.jpg)
<font style='background-color:#fff359;'>*直接缩放（放大潜变量）不推荐使用，对显存要求很高</font>
<font style='background-color:#f8ba00;'>*图生图的进阶功能将在第七课介绍</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_174467.jpg)
[🚩06:59随机种子作用解析](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=419)
<strong><font style='font-size:24px;'>三、随机种子作用解析</font></strong>
	[🚩07:56随机种子](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=475)
	<strong>（一）随机种子：</strong>
			AI生成一幅画的过程是随机的，但每一次生成都有一套描绘方式，这个描绘方式就会被记录成一组随机数，叫随机种子
	<strong>（二）随机性：</strong>
		使用不同的随机种子出来的效果就随机性强
		使用同一个随机种子，生成的图像就会有很多相似之处
		<font style='color:#a5a5a5;'>（因为用同一套方法随机出来的）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_556983.jpg)
	[🚩08:30功能按钮](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=510)
	<strong>（三）功能按钮</strong>
			1.骰子：把随机参数设置为-1
				<font style='color:#a5a5a5;'>（每次都抽一张新卡）</font>
			2.绿色循环：使用上次的随机种子
				<font style='color:#a5a5a5;'>（卡池里都是一张卡）</font>
	[🚩08:42图库浏览器](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=521)
	（四）打开图库浏览器能查看图片的生成信息，里面就有种子号码（Seed）
<font style='color:#a5a5a5;'>（或者在“图片信息”功能里上传图片也能查看）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_662488.jpg)
（五）使用相同的随机种子，添加相关提示词，能最大程度保持任务风格相对一致
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_505244.jpg)
 [🚩09:04图生图的拓展应用](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=543)
<strong><font style='font-size:24px;'>四、图生图的拓展应用</font></strong>
	（一）真实图片转换成二次元风格
		<font style='color:#a5a5a5;'>（比一些平台可调节的精细程度更高）</font>
	（二）物品甚至风景“拟人化”
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_120399.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_383099.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_116450.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_006376.jpg)
	（三）让二次元角色走到三次元
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_352623.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_409915.jpg)
<font style='background-color:#f8ba00;'>*模型风格的区别在第四课里会提到</font>
<font style='background-color:#f8ba00;'>*LoRa模型在第九课里</font>
[🚩10:04更进阶的玩法](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=604)
（4）简单绘画通过AI生成更精美的画
<font style='background-color:#ffffff;color:#a5a5a5;'>弹幕里敲6，他就会教给我们</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_885673.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_360868.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_955542.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_298623.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_379766.jpg)

[🚩11:15总结](https://www.bilibili.com/video/BV1Lh411u7vs?p=1&t=675)
<strong><font style='font-size:24px;'>五、总结-思维导图</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Lh411u7vs_702178.jpg)





