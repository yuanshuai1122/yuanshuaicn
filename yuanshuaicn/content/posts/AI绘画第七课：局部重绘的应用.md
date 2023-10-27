---
title: AI绘画第七课：局部重绘的应用
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

<strong><font style='font-size:24px;'>第七课：局部重绘的应用</font></strong>
<font style='color:#a5a5a5;'>*喜欢的话可以一键三连</font>
<font style='color:#a5a5a5;'>笔记下载看这篇专栏cv25267334</font>
[🚩00:01前言](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=1)
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_498146.jpg)

[🚩01:19](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=79)
<strong><font style='font-size:24px;'>一、局部重绘基本操作</font></strong>
<strong>（一）问题：99%的部分满意，1%不满意，怎么改？</strong>
加提示词<font style='background-color:#fff359;'>重画</font>修改，随机种子固定
	缺点：
	（1）新生成的图大概率跟原图不同，且不一定修改好
	（2）若图片经过高清修复或者放大等大分辨率图，重画费时间易爆显存<font style='color:#a5a5a5;'>（相对显存小的来说）</font>
（二）局部重绘实例：
[🚩02:13](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=132)
1.进入局部重绘：
（1）图生图标签下的局部重绘功能
（2）图库浏览器点开图片右下角的局部重绘按钮
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_975419.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_816596.jpg)

[🚩02:40](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=160)
2.实例开始：
（1）原来的提示词不变，加入后面加入Closed eyes(闭眼），给上1.2权重，将下方的图生图重绘幅度开到一个比较高的数值<font style='color:#a5a5a5;'>（0.7-0.8）</font><font style='color:#000000;'>，新功能暂时维持默认</font>
[🚩03:32](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=212)
（2）见证效果：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_301256.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_547747.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_630223.jpg)


<strong>3.原理：</strong>
整张图片都经历了一个重新加噪并去噪的过程，而眼睛的部分被强调了<font style='color:#a5a5a5;'>（加了提示词和权重）</font><font style='color:#000000;'>，</font>保留了和原来相对一致的外观

涂抹的区域相当于把这一块拿出来单独“图生图”一下，最后又拼回原图里
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_458224.jpg)

[🚩04:06](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=246)
<strong><font style='color:#000000;font-size:24px;'>二、功能介绍：</font></strong>
[🚩02:52](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=171)
<font style='color:#000000;'>（一）当鼠标移动到图片区域时，出现一个黑色圆圈，这就是画笔，按住鼠标左键，可以在图片上涂出</font><font style='color:#000000;background-color:#fff359;'>黑色区域</font><font style='color:#000000;'>，</font><font style='color:#000000;background-color:#fff359;'>这是AI重画的地方</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_307005.jpg)
右上角的按钮功能分别为：撤销、取消图片、画笔大小<font style='color:#a5a5a5;'>（新版WebUI加入橡皮擦功能了）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_535754.jpg)

[🚩04:14](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=253)
（二）核心参数解析
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_367349.jpg)
<strong>1.蒙版</strong>：它泛指一些用以限定处理区域的范围对象，字面意义上理解就是一个“蒙"住了某些关键区域的“版"子
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_234717.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_519610.jpg)
2.重绘蒙版内容：把涂黑部分进行重画
3.重绘非蒙版内容：把涂黑部分以外的进行重画
4.蒙版蒙住的内容：
	（1）原图：遮住的地方就是黑的，AI从头开始画
	（2）填充：把蒙住的内容高度模糊，从模糊中重画，让AI多一点发挥空间
	（3）潜变量噪声和数值零：两种涉及潜变量、潜空间的生成方式原理较为复杂，简单说图生图方式更复杂了，加入加噪、去噪的过程，理论上对图像的改变会更显著
[🚩05:14](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=313)
（4）对比：
<font style='color:#a5a5a5;'>和选择放大算法时一样,有点看缘分</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_609119.jpg)

[🚩05:18](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=318)
5.重绘区域：
<strong>全图：</strong>AI会基于你的新的要求（提示词、参数）把整张图重新画一遍 ，但最后只保留你画出来的这一块区域拼回去
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_715737.jpg)
<strong>仅蒙版：</strong>AI就会只画你框出来的这块区域附近，把它当成一张完整的图画，然后再拼回去
<font style='background-color:#fff359;color:#000000;'>绘制速度较快，但它没法读取你的图像全貌，经常出现拼上去以后变得奇奇怪怪的问题</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_717803.jpg)
<strong>但并不是没有用</strong>
一些针对性强的修改反而会希望缩小图片尺幅，这个时候你需要降低重绘幅度避免变形，并且对提示词做净化处理<font style='color:#a5a5a5;'>（比较进阶的内容）</font>
<strong>仅蒙版模式的边缘预留像素：</strong>会发挥和放大修复那节课里提到过的“缓冲带"类似的作用
<font style='color:#a5a5a5;'>（用于仅蒙版模式）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_896148.jpg)
默认数值32可以保证比较好的拼合效果，重绘区域大，就增大数值；反之减小
<strong>蒙版模糊：</strong>决定了重绘区域边缘和其他部分是如何接触的，类似图像处理软件里面的“羽化”功能，令选区、蒙版的边缘柔和
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_870293.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_876700.jpg)
蒙版模糊=0，四周接触边缘较为生硬，过度较为不自然
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_076394.jpg)
一个10以下的模糊数值可以让重绘区域拼接进去的过程变得更加丝滑
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_182988.jpg)
蒙版模糊太大会影响内部区域的读取或者影响到周边其他区域
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_438403.jpg)

[🚩07:01](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=421)
<strong><font style='font-size:24px;'>三、绘制功能应用</font></strong>
（一）局部重绘的问题：即便区域被精准地限制住，但重新生成的过程仍然充满了<font style='background-color:#fff359;'>不确定性</font>，它能给你画坏一次，那就有信心给你画坏两次三次无数次
（二）局部重绘（手涂蒙版）<font style='background-color:#fff359;'>（新）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_218916.jpg)
新功能：拾色器
<font style='background-color:#fff359;'>（拾色器用不了的，换个浏览器，如Chrome、Edge等内核较新的浏览器）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_134632.jpg)
能自由更改、调整画笔颜色，这里画笔的作用就<strong><font style='background-color:#fff359;'>不再仅仅是“划出范围"了，而是具有了一部分输入内容的能力</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_751808.jpg)

[🚩08:04](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=484)
（三）口罩实例：
1.加一个口罩：用黑色覆盖半个面部，再画两根系在耳朵上的绑带
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_502831.jpg)
与通常的图生图一样，还需要添加关于黑色口罩的提示词，权重、重绘幅度可以拉高点，
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_531192.jpg)
结果：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_773839.jpg)
2.画一个蓝色带白色爱心的口罩：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_248868.jpg)
画完后提示词也要修改
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_437618.jpg)
因为重画的内容变复杂了，需要降低权重和重绘幅度，让它能更稳定地传递我的绘画内容
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_570050.jpg)
结果：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_590817.jpg)
画上去的颜色会同时构成这个画面的一部分，并参与到图生图的过程里
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_958707.jpg)

[🚩09:34](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=573)
（四）画手实例：
1.使用拾色器里的吸管吸取背景墙颜色
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_447138.jpg)
2.直接把原来画坏了的手完全覆盖起来
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_072838.jpg)
3.再吸取一个肉色
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_427817.jpg)
4.用肉色把手的轮廓勾勒出来
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_031937.jpg)
5.加提示词
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_282404.jpg)
负面提示词加入上上一课讲的Negative Embeddings，权重1.2，进一步抑制错手的现象
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_586260.jpg)
<strong><font style='background-color:#fff359;'>蒙版透明度：</font></strong>简单理解就是这些颜色印在你的画面上的显著程度。一般默认维持0（<font style='color:#a5a5a5;'>完全不透明）</font>，觉得颜色重了，可以适当开大一点，让它稍微透明、变弱一些
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_056655.jpg)
6.适当降低重绘幅度，太大的重绘幅度会令我们勾勒出来的手部线条被模糊
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_356130.jpg)
同理上面的蒙版模糊也不宜太大，这里保持默认4
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_828837.jpg)
7.结果 ：
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_276581.jpg)
如果结果不满意，就把随机种子放开了多试几次，并按照我们讲的参数细节反复调教一下

[🚩10:57](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=657)
（五）绘图
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_764355.jpg)
1.绘制过程基本一样，但因为<font style='background-color:#fff359;'>没有了局部重绘</font>，它会把你新绘制的内容加进图片里，然后重新对整张图做个<font style='background-color:#fff359;'>完整的图生图</font>，肯定会导致其他部分的细节发生变化。想不变就用局部重绘
2.实现“灵魂画手”
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_629166.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_068342.jpg)

[🚩11:27](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=687)
<strong><font style='font-size:24px;'>四、上传蒙版功能应用</font></strong>
（一）蒙版功能：通过图像处理软件制作蒙版，能更精确控制重绘位置
上方放重绘的图片，下方放蒙版图片
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_522074.jpg)

默认下白色区域是重绘区域，黑色部分保留<font style='color:#a5a5a5;'>（可以通过下方按钮交换功能）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_660785.jpg)

[🚩12:19](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=739)
（二）PS制作蒙版<strong>（选学）</strong><font style='color:#a5a5a5;'>（下面基本上都是这个了，不想学的可以直接拉到底）</font>
<strong>【选择】-【主体】</strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_611890.jpg)

Photoshop就会智能地为你生成一圈像这样包围人物的“蚂蚁线”【选区】<font style='color:#a5a5a5;'>（前提是有比较明显的主体）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_512794.jpg)
如果画面中人物比较多，画面复杂，可以使用工具栏中的“对象选择工具”
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_851952.jpg)
按住鼠标左键拉出一个框框住人物，它就会识别这个区域内的可选择对象并生成类似的选区
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_365439.jpg)
如果生成的选区有残缺或者多出来的部分，就切换到套索工具<font style='color:#a5a5a5;'>（套索工具可以帮助你以“圈地”一样的方式增加或删除选区）</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_114371.jpg)
<font style='background-color:#fff359;'>添加新的选区：按住Shift+鼠标拖拽</font>
<font style='background-color:#fff359;'>从原选区减去选区：按住Alt+鼠标拖拽</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_935628.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_879877.jpg)
<font style='background-color:#fff359;'>选择好重绘的地方后</font>，在最上方的菜单栏里依次选中<strong>【图层】-【新建填充图层】-【纯色】</strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_296583.jpg)
点击确定
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_079624.jpg)
在跳出来的拾色器里，选择白色
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_360610.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_986156.jpg)
在图层窗口里选中这个填充图层
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_683882.jpg)
复制选中图层：按住Ctrl+J
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_881440.jpg)
双击最上面的图层前面的白色小方块，把填充图层颜色改为黑色
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_716383.jpg)
单击一下后面的长方形，这个东西，其实就是PS当中的蒙版了
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_431923.jpg)
在选中后（周围有一圈选框），按住Ctrl+I，交换蒙版区域，黑色和白色的部分就被换过来了
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_665331.jpg)
左上角【文件】-【存储副本】
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_521232.jpg)
格式可以选中<strong>PNG</strong>或者<strong>JPG，</strong>然后一路默参数认即可
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_668677.jpg)
导入黑白图到WebUI的上传蒙版区域
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_088792.jpg)
<font style='color:#a5a5a5;'>用这个上传蒙版的动作，代替了在WebUl里的那些涂涂画画</font>
<font style='color:#000000;'>下面的参数设置与前十分钟讲的是完全一样的</font>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_197622.jpg)

[🚩14:21](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=861)
（三）结果：
现在AI就可以十分精确地为你重绘这一块被白色标记出来的区域了
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_043403.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_636518.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_063996.jpg)

[🚩14:38](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=877)
（四）其他进阶玩法（挖坑待更）
 例如在Photoshop里导入手部贴图、局部裁剪导出重绘等等，在经由Upscale输出的高分辨率下实现更为精确的手部修复
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_956791.jpg)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_109126.jpg)
对比
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_147210.jpg)


![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_101759.jpg)

[🚩14:57](https://www.bilibili.com/video/BV1uL411e7Uk?p=1&t=897)
<strong><font style='font-size:24px;'>五、总结</font></strong>
![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/BV1uL411e7Uk_201515.jpg)

