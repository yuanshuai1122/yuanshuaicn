---
title: AI绘画第六课：AI绘画进阶模型
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

<strong><font style='font-size:24px;'>第六课：AI绘画进阶模型</font></strong>
<font style='color:#a5a5a5;'>*喜欢的话可以一键三连</font>
<font style='color:#a5a5a5;'>笔记下载看这篇专栏cv25267334</font>
[🚩00:00前言](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=0)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_486144.jpg)

[🚩01:48](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=108)
<strong><font style='font-size:24px;'>一、文本嵌入（Embeddings）</font></strong>
<strong>（一）作用：指向某一种特定的形象，</strong>除了能帮AI更好地画好字典里已经有的东西以外，有时候也可以帮我们实现特定形象的呈现
<font style='font-size:17px;'>如：用于去除特定的不想要的画面（手部错乱等）</font>
<font style='color:#a5a5a5;'>在C站一些模型网站上是另一种形式表述：Textual Inversion - “文本倒置"</font>
<strong>（二）特点</strong>：文件占用空间非常小，小到可能只有kb，原因在于不包含信息，只是一个标记。
<font style='font-size:17px;'>就像大字典（Checkpoint）里面的一个小“书签”，能够精准的为你指向个别字、词的含义，提供高效的索引</font>
<font style='background-color:#ffffff;color:#a5a5a5;'>*Embeddings在深度学习领域的全称叫做“嵌入式向量"，向量在数学里的含义指一个带有方向、指向性的“量”</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_916336.jpg)

[🚩02:30例子解释](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=150)
<strong>（三）解释：</strong>
让AI画“猫又”，字典里虽然没有这个偏僻的概念，但AI知道“猫”、“人”、 “妖怪”怎么画。在字典里记有这些概念的夹上一片书签（标记），当提到”猫又“这个词时，把这带有书签的页面的信息汇总在一起，就知道“猫又”是个什么东西了
<font style='color:#a5a5a5;'>*</font><font style='color:#a5a5a5;font-size:18px;'>猫又，俗称猫妖、猫股，在</font><font style='color:#a5a5a5;'>日本神话</font><font style='color:#a5a5a5;font-size:18px;'>中是一种有着两条尾巴的</font><font style='color:#a5a5a5;'>黑猫</font><font style='color:#a5a5a5;font-size:18px;'>形象。耳朵大而尖，牙齿为双面</font><font style='color:#a5a5a5;'>锯齿型</font><font style='color:#a5a5a5;font-size:18px;'>，是猫妖的一种，据说能直立行走。</font>
[🚩03:21如何使用](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=200)
<strong>（四）用法：</strong>
C站上就有很多基于特定动漫角色形象训练的Embeddings
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_100492.jpg)

<strong><font style='font-size:17px;'>1.位置：</font></strong><font style='font-size:17px;'>Embeddings放置路径：根目录/embeddings</font>
文件后缀一般和vae一样是“ .pt ”
<strong>2.使用：</strong>Embeddings不需要特别调用，只需要在提示词里用特定的“咒语“去召唤它，一般它们的Model Card里都会标注
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_200124.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_329125.jpg)

[🚩04:07对比实验](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=247)
<strong>3.对比：</strong>
不使用Embeddings前：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_283765.jpg)
使用后：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_358719.jpg)

[🚩04:32](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=272)
<strong><font style='font-size:24px;'>*</font></strong><strong><font style='font-size:17px;'>一个小功能的补充</font></strong>
不知道如何写提示词，可以导入图片通过图生图里面的反推提示词功能反推提示词
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_509500.jpg)
DeepBooru和CLIP可以算是两种不同的图像识别算法，DB会更快一些
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_284902.jpg)
出来的提示词自己确认无误后加到文生图提示词里面


[🚩05:19](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=319)
<strong>4.优缺点：</strong>
（1）
优点：对于一些更为广泛、容错率更高的形象概念，Embeddings的表现会好很多
缺点：对于角色形象上可能不完全像，也只是让A去按图索骥而已（习惯让LoRa去做）

[🚩05:40](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=339)
例如：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_228405.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_680931.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_392432.jpg)
它其实就是把这种几个人物朝向并列的图片结构，作为一种“书签”输入到了AI的字典里

[🚩05:58](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=357)
（2）例子的做法：
Embeddings的作者也给出了一个基本的提示词格式
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_444961.jpg)
前一个什么什么描述人物本身的特质，后一个着重描写服饰
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_753533.jpg)
一些标准化或内容型的提示词可以被保留下来
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_508868.jpg)
我还添加了这一系列提示词以及作者提供的一个描述句来固定生成图片的格式
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_753117.jpg)
可以同时在一次生成里激活多个Embeddings，它们不至于冲突，但产生的化学反应是需要你自己观察的
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_394529.jpg)
记得开启高清修复，能让你的图片变得更加清晰且精致
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_350667.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_812766.jpg)
当然还是有些问题的，比如在人物细节的稳定性以及转身动作的具体幅度上面
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_783326.jpg)
上面问题在后面我们接触了ControlNet、 LoRa等更为进阶的操作手法以后，都能得到更好的实现
[🚩06:52](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=412)
<strong>5.解决肢体问题</strong>
（1）Embeddings常常被我们用于解决AI绘画过程中的一个巨大的痛点：AI不会画手（不光手部问题）
<font style='color:#a5a5a5;'>因为AI是用扩散的方式来实现图像生成的，对人的正常身体结构并没有一个完整的认知，它只知道人有两只手两只脚，有时候却不知道它们是怎么拼到一起的</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_664000.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_223869.jpg)


[🚩07:29](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=448)
<strong>（2）</strong><strong><font style='font-size:17px;'>Negative Embeddings(负面词嵌入)</font></strong>
①
以EasyNegative为例子，它需要把提示词“EASYNEGATIVE”放到“负面提示词”里面激活

未加前：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_458368.jpg)
加提示词后：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_009340.jpg)
实际上EasyNegative能解决的问题远不止多一只手或少几根手指。


它是一种综合的、全方位的基于<font style='background-color:#fff359;'>负面样本</font>的<font style='background-color:#fff359;'>提炼</font>，解决的问题可能还包括肢体错乱、颜色混杂、噪点和灰度异常等<font style='color:#a5a5a5;'>（但不一定100%解决）</font>


<font style='color:#000000;'>②不同的嵌入词会有所区别：</font>
EasyNegative是基于Counterfeit去训练的，作者测试了对大多数二次元模型都有效
DeepNegative则主要是针对真人模型来训练的

[🚩08:49](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=528)
<strong><font style='font-size:24px;'>二、LoRa（秩适应模型）</font></strong>
<strong><font style='font-size:24px;'> </font></strong><font style='font-size:17px;'>（一）</font>作用：
在于帮助你向A传递、描述某一个特征准确、主体清晰的形象（固定特征点）
（二）概念：
让AI画一只“小猪佩奇”，可AI没有接触过这些概念。虽然可以通过很多个书签让它东找西找凑出一个佩奇来，但效果不一定很好
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_267403.jpg)
如果说Embeddings是轻薄便利的小书签，LoRa就好像是一张夹在里面的彩页一样，上面把“佩奇”是什么，有什么特点，用什么方式去呈现，全写出来了。这样AI对“佩奇”的理解是更加全面且准确的
<strong>（三)位置：根目录/models/Lora</strong>

[🚩10:02](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=601)
（四）使用与实例：
<strong>1.触发LoRA：</strong>
在提示词里输入<strong><lora:"LoRA名称"></strong>
例：&lt;lora:dVaOverwatch_v3&gt;
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_934840.jpg)
一些LoRA也会提供触发提示词（Trigger Word），可以把提示词也加上
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_699770.jpg)
出图对比效果：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_654586.jpg)
2.使用过程中的小问题：
因为它的训练图源复杂，所以一般也会对画风构成些微影响
<font style='color:#a5a5a5;'>（上面那张图Counterfeit带来的那种水彩涂抹的精致感就弱了一些)</font>


[🚩10:47](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=647)
<font style='color:#000000;'>3.解决问题：</font>
可以适当<font style='background-color:#fff359;'>降低</font>LoRa作为一个关键词的<font style='background-color:#fff359;'>权重</font>

<font style='background-color:#fff359;'>0.5~0.8</font>之间都可以确切地保留特征但减弱对画面风格的影响
<font style='color:#a5a5a5;'>（LoRa、Embeddings等的提示词权重调整方式与普通提示词一致）</font>
大部分LoRa作者也会给出他们认为合适的LoRa权重，抄作亚的时候还可以参考其他创作者绘制的效果
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_374918.jpg)
对比：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_009492.jpg)
<font style='color:#a5a5a5;'>（想更加深入了解LoRa，观看第九课）</font>
[🚩11:29](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=688)
<strong><font style='font-size:24px;'>三、Hypernetwork(超网络)</font></strong>
<font style='font-size:17px;'>（一）概念：</font>
超网络的原理与LoRA不同，但最后能实现的效果其实和LoRa是差不多的
说LoRa是传单，可以把Hypernetwork理解成一张小卡片，都差不多
（二）有点小区别：
Hypernetwork一般被用于改善生成图像的整体风格，这个画风比Checkpoint所定义的画风要更为精细一些
<strong>（三）位置：根目录/models/hypernetwork</strong>
[🚩12:04](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=724)
（四）例子：
<strong>Waven Chibi Style</strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_465169.jpg)
1.如何使用超网络：
<strong>（1）设置</strong>—<strong>附加网络</strong>—<strong>将超网络(Hypernetwork)添加到提示词</strong>—<strong>选择对应的超网络添加到提示词里</strong>
（2）另外<strong>超网络</strong>可以使用类似于Lora的<strong>词条模式</strong>直接激活
格式为： <font style='background-color:#fff359;'><hypernet:模型名.pt:0.8（权重）></font>
<font style='color:#a5a5a5;'>（设置完记得点击上面的保存）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_166300.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_463929.jpg)
2.其他情况：
（1）网上多数研究者对于Hypernetwork在图像生成方面的评价并不好，至少不如LoRa和Embeddings那么好
（2）超网络的作用可以部分被LoRa取代的
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_612494.jpg)
（3）在需要实现特定艺术风格时，超网络仍然可以给你提供不少帮助
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_606763.jpg)
例如雕塑风格、像素画、抽象画等，推荐你换几个不同的风格多做尝试
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_401293.jpg)

[🚩13:21](https://www.bilibili.com/video/BV1th4y1p7fH?p=1&t=801)
<strong><font style='font-size:24px;'>四、总结</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_112850.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1th4y1p7fH_610887.jpg)

