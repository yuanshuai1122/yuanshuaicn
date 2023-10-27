---
title: AI绘画第五课：图片放大和辅助处理手段
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

<strong><font style='font-size:24px;'>第五课：图片放大和辅助处理手段</font></strong>
<font style='color:#a5a5a5;'>*喜欢的话可以一键三连</font>
[🚩00:01前言](https://www.bilibili.com/video/BV11m4y12727?p=1&t=1)
[🚩01:22高清修复原理和操作](https://www.bilibili.com/video/BV11m4y12727?p=1&t=82)
<strong><font style='font-size:24px;'>一、高清修复原理和操作</font></strong>
<font style='font-size:17px;'>（一）三种主要的放大修复方案</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_167498.jpg)
[🚩03:33](https://www.bilibili.com/video/BV11m4y12727?p=1&t=213)
（二）原理：SD会先绘制低分辨率版本，再根据第一张图绘制第二张高分辨率版本，本质就是把低分辨率成品“图生图”一次
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_116254.jpg)
<font style='color:#a5a5a5;'>*开启后绘制速度会变慢，无法突破显存大小，能画多大就是多大</font>
<font style='background-color:#fff359;'>一个比较聪明的办法：先在低分辨率下反复抽卡，当确定一个合适的画面之后，固定随机种子，再用高清修复得到相近的图片</font>
<font style='background-color:#ffffff;'>另外，和图生图一样，重绘幅度不适合太低或者太高，只是为了放大就0.3-0.5，想赋予AI更多想象力和发挥空间就0.5-0.7</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_493806.jpg)


[🚩01:37文生图：高清修复](https://www.bilibili.com/video/BV11m4y12727?p=1&t=96)
（三）文生图：高清修复
译名有好几种：高清修复（Hi-Res Fix）、高分辨率修复、超分辨率（超分）
[🚩01:49实例讲解](https://www.bilibili.com/video/BV11m4y12727?p=1&t=109)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_437737.jpg)
<font style='color:#a5a5a5;'>*分辨率设置太低，AI画出来的细节没那么丰富，在二次元图片里体现没那么充分，但到了真人图片里就暴露无遗了</font>
[🚩02:51功能介绍](https://www.bilibili.com/video/BV11m4y12727?p=1&t=170)
<strong>1.放大倍率：</strong>指将这个图片由原始分辨率放大到多少，可以按倍数、具体数值定
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_287616.jpg)
<strong>2.高清修复采样次数：</strong>等同于这个过程中进行的采样步数

<font style='color:#a5a5a5;'>注:高清修复需要经过一次重绘，因此需要设置采样步数。</font>
<font style='color:#a5a5a5;'>保持默认的0数值，它会和我们设置的采样迭代次数（20）保持一致。</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_157169.jpg)
<strong>3.重绘幅度：</strong>等同于图生图里的重绘幅度
<font style='background-color:#fff359;'>想要保持大的结构不发生变化，最好不超过0.5</font>


[🚩03:05算法讲解](https://www.bilibili.com/video/BV11m4y12727?p=1&t=184)
<strong><font style='background-color:#ffffff;'>4.放大算法：</font></strong><font style='background-color:#ffffff;'>跟上面选择的原始算法一样，决定在将那个低分辨率的版本“打回重画”的时候如何操作</font>

（1）翻译问题：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_458034.jpg)
（2）十三种算法生成的图像：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_990015.jpg)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_331053.jpg)
（3)结论：算法之间的差别没有大道让我们体会到质的差异，生成的图像结果都差不多
①带有“GAN”的算法，致力于更好的还原图像特征，不易变形
②Latent：会进一步把图像打回潜空间内重画，适合丰富细节
<font style='background-color:#fff359;'>*另外，使用GAN系列时重绘幅度不要太高，一般0.2-0.5；但Latent系列重绘幅度低了会模糊，一般0.5-0.7</font>

（4）网上比较流行的说法：用到放大算法的功能，无脑选择R-ESRGAN，二次元选后面带Animer6B
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_116457.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_831450.jpg)
（5）不同的LoFi模型作者可能会有推荐的，所以用哪个算法不一定”最好“，可以根据自己的审美多试几个，选择自己最喜欢的
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_703756.jpg)

[🚩06:22SD放大](https://www.bilibili.com/video/BV11m4y12727?p=1&t=382)
<strong><font style='font-size:24px;'>二、SD放大</font></strong>
（一）图生图放大方式简析
1.图生图本身就是一种“高清修复”，所以里面没有“高清修复”的选项
2.如果原图分辨率低，只需放到图生图，调高分辨率，就能实现高分辨率修复了
3.小技巧：在图库浏览器里，当浏览一张已经做出来的图片时，可以直接通过右下的按钮，自动把你当时输入的所有提示词、参数信息和使用的模型都同步一份
*设置中的“放大”选项中可以自行设置图生图放大过程的算法，记得点击上面的保存
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_240724.jpg)


[🚩07:10Upscale放大脚本](https://www.bilibili.com/video/BV11m4y12727?p=1&t=429)
（二）放大脚本（SD Upscale）：让图生图变得更大、更精细
[🚩07:16实例](https://www.bilibili.com/video/BV11m4y12727?p=1&t=436)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_181765.jpg)
1.在参数设置区域的最下方有个加载“脚本”的选项栏，里面的脚本致力于为你实现一些软件本身并不包含但却非常实用的附加功能
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_581299.jpg)
打开SD放大脚本（SD Upscale）
2.[🚩07:55功能介绍](https://www.bilibili.com/video/BV11m4y12727?p=1&t=474)
<strong>（1）缩放系数：</strong>相当于刚刚的放大倍数
<strong>（2）放大算法：</strong>与上文介绍的几种差不多的
<font style='color:#a5a5a5;'>*Anime就是动漫的意思，名字带有这个的，说明是专门为二次元画风的图片准备的</font>
<strong><font style='color:#000000;'>（3)图块重叠的像素：</font></strong><font style='color:#000000;'>起到一个“缓冲带”的作用，解决分割图片后再组合后边缘过渡问题</font>

 简单理解：四张纸粘在一起变成更大的纸，如果之间有一部分是重合的，那就可以在这里涂上一些“胶水”或者双面胶，能更好的粘合在一起。否则只能用透明粘在表面上，不仅难看，还容易漏出缝隙来。
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_963463.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_591181.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_257498.jpg)
如果觉得还是觉得有些僵硬，可以试着拉大数值
<font style='background-color:#fff359;'>*开启SD放大后，最终宽高=（设置的宽高-重叠像素）× 放大倍率</font>
因此，设置重叠像素后，要在设置宽高的基础上加上该像素值。
例：缓冲拉大到128，相应的图片宽度和高度也要加上128
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_808503.jpg)
用这个功能的代价就是单张图片大小会变大，但比起高清修复，压力已经是非常小的了
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_991096.jpg)


3.[🚩09:30SD放大的总结](https://www.bilibili.com/video/BV11m4y12727?p=1&t=570)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_056893.jpg)
优点：
●自由度比高清修复更大
●不受显存限制，可以把图片整的非常大（最高可达4倍宽高）
●画面精细度高，对细节的丰富效果出色
缺点：
●分割重绘的过程较为不可控（语义误导和分界线割裂）
●操作繁琐且相对不直观
●偶尔“加戏”，出现莫名的额外元素
●如果有人脸、身体等关键部位恰好处在"分界线"上的时候,很大概率会产生不和谐的画面
<font style='color:#a5a5a5;'>（解决方法：降低重绘幅度，增大缓冲区尺寸）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_062326.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_222948.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_283593.jpg)

[🚩10:08附加功能放大](https://www.bilibili.com/video/BV11m4y12727?p=1&t=608)
<strong><font style='font-size:24px;'>三、附加功能放大</font></strong>
<font style='font-size:17px;'>（1）位置：在附加功能（更多）标签里</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_505202.jpg)
（2)功能：可以把已经绘制成为成品的图片通过人工智能算法放大到一定的尺寸

这个功能一般用在图片生成后的处理上
<font style='background-color:#fff359;'>简单说就是一个重绘幅度为0的高清修复（跟市面上AI修复照片的原理相似）</font>
<font style='background-color:#ffffff;'>（3）操作方法：</font>
<font style='background-color:#ffffff;'>设置个倍数、再选算法</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_171805.jpg)
<font style='background-color:#fff359;'>*附加功能里支持同时利用两种算法来进行放大，放大算法2可见度就相当于提示词的权重</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_735722.jpg)
不想考虑那么多，只靠一种算法也能发挥不错的放大效果，下面其他选项更为复杂，保持默认即可
（4）效果
在拉伸放大的基础上，适当润滑了线条和色块边缘的模糊区域，但整体的精致、细腻程度不如前两种涉及了重绘的操作手法
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_779142.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_784649.jpg)

[🚩11:29附加功能的总结](https://www.bilibili.com/video/BV11m4y12727?p=1&t=688)
（5）附加功能的总结
优点：
●简单、方便、负担小、随时可以调用
●计算速度快、无重绘压力
●完全不改变图片内容
缺点：
●效果不太显著
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_440490.jpg)


[🚩11:34结尾](https://www.bilibili.com/video/BV11m4y12727?p=1&t=694)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_509138.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV11m4y12727_915931.jpg)

