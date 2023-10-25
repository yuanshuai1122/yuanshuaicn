---
title: 第一课：入门AI绘画
date: 2023-10-12 18:49:55.373
author: 'yuanshuai'
cover: 'https://aabb-2023.oss-cn-beijing.aliyuncs.com/CleanShot%202023-10-25%20at%2013.41.52.png'
theme: 'light'
tags:
- AI
- 绘画
---

<strong><font style='font-size:24px;'>第一课：入门AI绘画</font></strong>


[🚩00:00前言](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=0)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1As4y127HW_457634.jpg)

[🚩02:31](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=151)
<strong><font style='font-size:24px;'>一、原理：</font></strong>
原图→扩散（增加噪声）生成（去除噪声）
[🚩06:05配置要求](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=365)
<strong><font style='font-size:24px;'>二、配置要求：</font></strong>
（一）电脑（Windows/Mac系统）
	<font style='color:#a5a5a5;'>建议win10以上的系统</font>
（二）显卡（优先选择NVIDIA英伟达显卡）
	<font style='color:#000000;'>1.独立显卡而非核显</font>
	<font style='color:#000000;'>2.显卡性能与显存会影响操作体验 ：</font>
	<font style='color:#000000;'>（1）性能影响出图效率</font>
	<font style='color:#000000;'>（2）显存影响绘制图形最大分辨率大小和模型训练规模</font>
[🚩07:51配置清单](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=471)
<strong><font style='font-size:22px;'>三、配置清单：</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1As4y127HW_568047.jpg)
[🚩08:08Webui及前置软件安装](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=488)
<strong><font style='font-size:24px;'>三.Webui及前置软件安装及运行</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1As4y127HW_006044.jpg)
<font style='color:#a5a5a5;'>*现在好像不用安装CUDA了，详情参考最新安装攻略，我是自己下载大佬的整合包直接就可以使用了，没有安装三前置，就不详细写了，感兴趣的可以看视频</font>
（一）自行手动安装
[🚩11:34](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=693)
（二）下载国内大佬的整合包（推荐）
<font style='background-color:#fff359;'>（链接仅做指路，随着时间推移会不断更新，下载前看看UP有没有发布最新版本的整合包下载）</font>
<strong>1.</strong><strong><font style='font-size:18px;'>秋葉aaaki</font></strong>
整合包视频链接：https://www.bilibili.com/video/BV1iM4y1y7oA/
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1As4y127HW_480992.jpg)
<strong>2.独立研究员-星空</strong>
整合包视频链接：
https://www.bilibili.com/video/BV1bT411p7Gt/
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1As4y127HW_936927.jpg)
3.幻灵AI绘画盒子（独立研究员-星空 参与的另一个整合包)
整合包视频链接：
https://www.bilibili.com/video/BV1Vc411T7Nw/

[🚩12:32](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=752)
 （三）程序运行
<strong><font style='background-color:#fff359;font-size:20px;'>*</font></strong><font style='background-color:#fff359;'> 	①安装路径不要有中文</font>
<font style='background-color:#fff359;'>	②Webui文件夹放在存储空间较多的盘里，尽量不要放C盘</font>
[🚩13:59注意点：操作端和命令行](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=839)
（四）操作端和命令行
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1As4y127HW_002699.jpg)

[🚩14:17基本操作指南](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=857)
<strong><font style='font-size:24px;'>四、基本操作指南</font></strong>
（一）常用的为前两个功能：文生图，图生图
（二）界面的功能的基本介绍
[🚩16:04基础模型](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=964)
（三）基础模型：
		1.推荐模型：
		2.StableDiffusion1.4
		3.AbyssOrangeMix（深渊橘）
		<font style='background-color:#fff359;'>*模型文件夹路径：Webut根目录/Models/Stable-diffusions</font>
[🚩16:46文生图](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=1006)
<strong><font style='font-size:20px;'>（四）文生图：文字生成图片</font></strong>
	提示词（Prompts）：被输入进去的文字
		<font style='color:#a5a5a5;'>*不支持中文，用英文逗号,隔开</font>
[🚩17:15“魔咒”](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=1035)
<strong><font style='font-size:20px;'>（五）“魔咒”：让AI更懂我们的意图</font></strong>
	<strong>正向提示词：</strong>
<font style='font-size:16px;'>(masterpiece:1,2),best quality,masterpiece,highres,original,extremely detailed wallpaper,perfect lighting,(extremely detremely detailed CG:1.2),drawing,paintbrush,</font>
	<strong><font style='font-size:16px;'>反向提示词：</font></strong>
 <font style='font-size:16px;'>NSFW,(worst quality:2),(low quality:2),(normal quality:2),lowres,normal quality,((monochrome)),((grayscale)),skin spots,acnes,skin blemishes,age spot,(ugly:1.331),(duplicate:1.331),(morbid:1.21),(mutilated:1.21),(tranny:1.331),mutated hands,(poorly drawn hands:1.5),blurry,(bad anatomy:1.21),(bad proportions:1.331),extra limbs,(disfigured:1.331),(missing arms:1.331),(extra legs:1.331),(fused fingers:1.61051),(too many dingers:1.61051),(unclear eyes:1.331),lowers,bad hands,missing fingers,extra digit,bad hands,missing fingers,(((extra arms and legs)))</font>
<font style='font-size:16px;color:#a5a5a5;'>*了解更多请看第二课</font>
[🚩19:34保存与导出](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=1173)
<strong><font style='font-size:24px;'>五、保存与导出</font></strong>
（一）生成的图片都会自动保存在本地文件夹里
<font style='background-color:#ffffff;'>（二）</font>图库浏览器：记录图像的各种生成信息
（3）图片查看：
	1.用图库浏览器查看生成的图片
	 2.本地文件查看：
			位置：去Webui根目录里（安装位置）找到Outout的文件夹
			<strong>txt2img-images</strong>：文生图
			<strong>img2img-images</strong>：图生图
			<strong>extras-images</strong>：图片分辨率放大
			<strong>txt2img-grids</strong>：文生图_生成多张图片的预览图
			<strong>img2img-grids</strong>：图生图_生成多张图片的预览图
[🚩20:19](https://www.bilibili.com/video/BV1As4y127HW?p=1&t=1218)
<strong><font style='font-size:24px;'>六、总结</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1As4y127HW_429292.jpg)





