---
title: AI绘画第二课：文生图入门与提示词基础
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

<strong><font style='font-size:24px;'>第二课：文生图入门与提示词基础</font></strong>
<font style='color:#a5a5a5;'>*喜欢的话可以一键三连</font>
<font style='color:#a5a5a5;'>更新于2023.7.24优化了下笔记</font>
<font style='color:#a5a5a5;'>笔记下载看这篇专栏cv25267334</font>

[🚩00:01前言](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=1)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_570263.jpg)

[🚩01:57提示词基本概念](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=117)
<strong><font style='font-size:24px;'>一、提示词基本概念</font></strong>
	<strong><font style='font-size:20px;'>（一）什么是提示词？</font></strong>
		告诉AI我需要画什么的语言
	<strong><font style='font-size:20px;'>（二）提示词的作用：</font></strong>
		文生图：画面控制主要靠提示词
		图生图：图片辅助提示词进行画面控制 
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_165513.jpg)
[🚩03:24提示词书写方式](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=204)
<strong><font style='font-size:24px;'>二、提示词书写方式</font></strong>
	（一）提示词要是英文
	（二）提示词以词组为单位，不需要有完整的语法结构
		<font style='color:#a5a5a5;'>“一条又长又宽的面和一个又大又圆的碗”→（面，长，宽），（碗，大，圆）</font>
	（三）词组之间用英文的半角逗号,隔开
	（四）提示词可以换行，末尾最好加上逗号
		<font style='background-color:#fff359;color:#000000;font-size:17px;'>*AI生成的画面具有随机性</font>
[🚩04:56提示词的分类](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=295)
<strong><font style='font-size:24px;'>三、提示词的分类</font></strong>
	（一）<strong><font style='font-size:20px;'>内容型提示词</font></strong>
		1.人物及主体特征
		2.场景特征
		3.环境光照
		4.补充：画幅视角
		<font style='background-color:#fff359;color:#000000;'>*加入提示词越具体AI思路也越清晰</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_158563.jpg)


	[🚩05:52加入其他提示词](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=352)
	（二）<font style='font-size:20px;'>只有内容型提示词画出的东西很大概率不会满意</font>
<font style='font-size:20px;'>		→则需要加入其他提示词</font>——<strong><font style='font-size:20px;'>标准化提示词</font></strong>
			常用：
				<font style='background-color:#fff359;'>best quality,ultra- detailed,masterpiece,hires,8k,extremely detailed CG unity 8k wallpaper</font>
<font style='background-color:#ffffff;'>				最高的质量，超级细节，杰作，高分辨率，8k（分辨率），超级细节的Unity CG壁纸</font>
			画风：
				<font style='background-color:#ffffff;'>插画风：</font><font style='background-color:#fff359;'>illustation,painting,paintbrush</font>
<font style='background-color:#ffffff;'>				二次元：</font><font style='background-color:#fff359;'>anime,comic,game CG</font>
<font style='background-color:#ffffff;'>				写实系：</font><font style='background-color:#fff359;'>phototorealistic,realistic,photograoh</font>

[🚩07:15模板框架](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=435)
<strong>（三）提示词模板框架</strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_107087.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_466772.jpg)
[🚩08:10提示词权重](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=490)
<strong><font style='font-size:24px;'>四、提示词权重</font></strong>
	<strong>（一）括号:</strong>
		1.圆括号( )：例：(词组)，权重为原来的1.1倍
		2.花括号{ }：例：{词组}，权重为原来的1.05倍
		3.方括号[ ]：例：[词组]，权重为原来的0.9倍
				<font style='color:#a5a5a5;'>*括号都是英文的</font>
				<font style='color:#a5a5a5;'>*可以套多层括号，每套一层就乘对应的倍数</font>
				<font style='color:#a5a5a5;'>例：(((词组)))，权重：1.1*1.1*1.1=1.331倍</font>
	<strong>（二）数字+括号</strong>：例：(词组:1.5)，权重为原来的1.5倍
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_345246.jpg)


	<strong>（三）权重分配</strong>
			避免个别词条权重太高，安全范围在1±0.5
			<font style='background-color:#fff359;color:#000000;'>*当词条权重过高时，容易扭曲画面内容</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_033320.jpg)
	<strong>（四）进阶语法</strong>
		
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_954445.jpg)
[🚩10:06反向提示词](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=605)
<strong><font style='font-size:24px;'>五、反向提示词（负面提示词）</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_647352.jpg)
[🚩11:08出图参数](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=668)
<strong><font style='font-size:24px;'>六、出图参数</font></strong>
	<strong>（一）采样步数：</strong>模拟一次画面会清晰一些，生成画面每闪一下代表迭代了一步
		步数一般为<strong>20</strong>，算力充足要求高就设置<strong>30-40</strong>，最低不要低于<strong>10</strong>
		<font style='background-color:#fff359;'>*步数大于20步以后画面提升不大</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_212538.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_955404.jpg)
	[🚩12:02采样方法](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=722)
	<strong>（二）采样方法：</strong>AI生成图形时所使用的某种算法
			一般常用就4~5个算法
				Euler和Euler a适合插画风格
				DPM 2M和2M Karras速度较快
				SDE Karras细节较为丰富
				<font style='background-color:#fff359;'>*	推荐用带有+的算法，它们是改进过的算法</font>
					<font style='background-color:#fff359;'>大部分模型也有推荐使用某种特定算法</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_825019.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_545214.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_232578.jpg)
	
[🚩12:45](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=765)
<strong>（三）宽度和高度：</strong>出图的分辨率
		默认：<u>512*512</u>
			<font style='background-color:#fff359;'>*分辨率太大显存会爆，容易出现多人，多手，多脚等情况</font>
		避免问题：一般先低分辨率绘制，再高清修复（Hires Fix）来放大
[🚩13:38](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=817)
<strong>（四）高清修复（Hires Fix）：</strong>提高出图的分辨率
[🚩13:51](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=831)
<strong>（五）面部修复：</strong>采用对抗算法识别人物面部并进行修复
		<font style='background-color:#fff359;'>*画二次元不建议开</font>
[🚩14:02](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=841)
<strong>（六）平铺/分块（Tiling）：</strong>用来生成无缝贴满整个屏幕的纹理性图片
		<font style='background-color:#fff359;'>*没有需求不要开</font>
[🚩14:10](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=850)
<strong>（七）提示词相关性（CFG Scale）：</strong>数值越高，越贴近提示词
			与权重一样不会浮动太多，太高容易变形
			比较安全的数值：<u>7~12</u>
[🚩14:21](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=861)
<strong>（八）随机种子：</strong>控制画面一致性的重要参数
[🚩14:29](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=868)
<strong>（九）生成批次：</strong>按照同一组提示词和参数出图的次数
		   <strong>每批数量：</strong>每批次绘制的图像数量（显存小的容易爆，不建议调）
[🚩15:40写提示词的三大方法](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=940)
<strong><font style='font-size:24px;'>七、写提示词的三大方法</font></strong>
	<strong>（一）翻译大法：</strong>用自然语言说出想要画的东西
	<strong>（二）借助工具：</strong>
		Tag生成器：																									
			NovelAI tag生成器	https://wolfchen.top/tag/	2.1全新升级！																							
			魔咒百科词典	https://aitag.top/	网友分享的tag组合很多																							
			Danbooru 标签超市	https://tags.novelai.dev/	超级好用！这就是我理想的交互方式																							
			魔导绪论	https://magic-tag.netlify.app/	全tag搜索，交互方式也很有趣																							
			魔法导论	https://www.noveltags.com/allTag	全tag搜索
			<font style='background-color:#fff359;'>*思路不要被已有的词汇限制住了，有自己想加的可以自己撰写添加</font>
	<strong>（三）抄作业：</strong>复制别人在网上分享的提示词
			分享两个网站
				openart.ai：有很多基于SD官方模型和欧美主流模型生成的作品
				arthub.ai：二次元和亚洲审美的内容会多些
		[🚩17:53](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=1073)
		<font style='background-color:#fff359;'>抄作业别忘了我们说的大概的逻辑框架，对提示词进行仔细筛选，选择自己喜欢的部分</font>，
		比如人物表现形式和背景元素等，那就主要抄内容型的部分
[🚩18:18总结](https://www.bilibili.com/video/BV12X4y1r7QB?p=1&t=1097)
<strong><font style='font-size:24px;'>八、总结</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_264012.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV12X4y1r7QB_464565.jpg)



