---
title: AI绘画第四课：模型的概念及应用
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

<strong><font style='font-size:24px;'>第四课：模型的概念及应用</font></strong>
<font style='color:#a5a5a5;'>*喜欢的话可以一键三连</font>
[🚩00:00前言](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=0)
模型的概念和原理
[🚩01:19](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=78)
想要的内容=提示词+模型+参数
当三者都确定下来时，才能产出想要的内容
<font style='color:#a5a5a5;'>*当抄了一份提示词，产出的图却跟原作者不同，有可能是和原作者使用的模型不同</font>

[🚩02:03](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=123)
<strong><font style='font-size:24px;'>一、模型文件基础</font></strong>
模型放置路径一般为SD中的“<font style='background-color:#89d4ff;'>models</font>”
大模型一般存放在models里的“<font style='background-color:#89d4ff;'>Stable-diffusion</font>”
模型解析：https://spell.novelai.dev/（把下载的模型拖进去，会自动分析是什么模型，并提示放在哪个目录下）
[🚩02:22检查点](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=141)
（一）这种模型叫做检查点或者关键点模型（Checkpoint）
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_741202.jpg)
<font style='color:#a5a5a5;'>*运算到某个关键位置时候，建立一个关键点保存已经运算部分，以后方便回滚和继续运算（类似游戏里面的存档点）</font>
<font style='background-color:#fff359;'>（二）常把一般占用几GB的叫做“大模型”</font>
<font style='color:#000000;'>1.checkpoint模型：文件后缀通常是“.ckpt”一般占用3-7GB，，另一种后缀.safetensor，占用空间会比较小一点，通常1-2GB</font>
<font style='color:#a5a5a5;'>*这类模型都是放在“models—Stable-diffusion”目录下</font>
[🚩03:22加载模型](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=201)
2.左上角<font style='color:#000000;'>加载模型选项，右边蓝色按钮为刷新</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_743318.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_575646.jpg)
3.用的是 秋葉aaaki 的整合包，启动器里有相关选项对模型进行管理、下载
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_604711.jpg)

[🚩03:54VAE](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=234)
（三）VAE可以简单理解为“调色滤镜”，主要影响画面色彩质感
文件存放在“models—VAE”后缀一般为pt
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_295627.jpg)
大部分模型都已经把VAE整合进大模型文件里了，没有VAE出图会发灰、发白
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_712089.jpg)
针对特定模型的VAE改成和他们的Checkpoint一样的名字，在切换模型时能自动切换VAE（勾选自动加载）
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_115912.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_513313.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_879919.jpg)
 *<font style='color:#a5a5a5;'>模型不止Checkpoint一种</font>
<font style='color:#a5a5a5;'>有画面微调的hypernetwork，优化画风的embeddings，固定特定人物角色特征的LoRa</font>
<font style='color:#a5a5a5;'>（详细请看第六课）</font>
[🚩05:09模型获取](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=308)
<strong><font style='font-size:24px;'>二、模型下载渠道分析</font></strong>
（一）大家会把训练AI学习图片生成模型的事情叫做“炼丹”
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_847324.jpg)
“炼丹”有一定技术门槛和硬件需求

[🚩06:21](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=381)<font style='font-size:24px;'>（介绍）</font>
  （二）主流模型下载网站有两个：<strong>HugginFace</strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_142331.jpg)
地址：https://huggingface.co

[🚩07:44C站](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=463)<font style='font-size:24px;'>（介绍）</font>
<strong><font style='font-size:24px;'>Civitai</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_259966.jpg)
地址：https://iativic.com
<font style='color:#a5a5a5;'>(需要魔法)</font>

[🚩11:21模型类目与推荐](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=681)
<strong><font style='font-size:24px;'>三、模型类目与推荐</font></strong>
<font style='font-size:17px;'>（一）目前模型主要分为三大类：二次元类，真实系，2.5D类</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_934904.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_049802.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_454939.jpg)
<strong><font style='font-size:20px;'>（二）三大类模型风格区别</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_973314.jpg)
（三）模型推荐
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_162968.jpg)

<strong><font style='font-size:20px;'>1.</font></strong>[🚩12:38二次元模型](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=758)
<strong><font style='font-size:18px;'>（1）Anything</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_177174.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_323684.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_941283.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_018148.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_200754.jpg)
<strong><font style='font-size:18px;'>（2）Counterfeit</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_415998.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_880616.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_799901.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_301054.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_097592.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_843999.jpg)
<strong><font style='font-size:18px;'>（3）Dreamlike Diffusion</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_392432.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_462118.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_475058.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_772655.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_202276.jpg)

[🚩13:45](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=825)
（4）其他有名的模型
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_911961.jpg)

<strong><font style='font-size:20px;'>2.</font></strong>[🚩14:04真实系模型](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=844)
<strong><font style='font-size:18px;'>（1）Deliberate</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_409625.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_055087.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_067787.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_226810.jpg)
<strong><font style='font-size:18px;'>（2）Realistic Vision</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_839094.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_238951.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_474883.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_011909.jpg)
<strong><font style='font-size:18px;'>（3）LOFI</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_940683.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_269898.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_603815.jpg)




![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_212816.jpg)

<strong><font style='font-size:20px;'>3.</font></strong>[🚩15:102.5D类](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=909)
<strong><font style='font-size:18px;'>（1）NeverEnding Dream（NED）</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_182328.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_104168.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_837006.jpg)
<strong><font style='font-size:18px;'>（2）Protogen</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_526709.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_640222.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_963527.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_916703.jpg)
<strong><font style='font-size:18px;'>（3）国风3（GuoFeng3）</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_045622.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_211616.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_486390.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_668717.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_311984.jpg)

<strong><font style='font-size:20px;'>4.</font></strong>[🚩16:22其他一些比较“小”的大模型](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=981)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_567101.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_041956.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_898741.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_547354.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_280088.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_194295.jpg)


<strong><font style='font-size:24px;'>四、</font></strong>[🚩16:39结尾](https://www.bilibili.com/video/BV1Us4y117Rg?p=1&t=999)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_971658.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1Us4y117Rg_625026.jpg)



