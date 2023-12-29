## Python 练习册（笔记） ##


#### 说明： ####

- https://www.zybuluo.com/susugreen/note/2512434
- [点此链接，会看到部分题目的代码，仅供参考](https://github.com/Show-Me-the-Code/python)
- 本文本文由@史江歌（shijiangge@gmail.com  QQ:499065469）根据互联网资料收集整理而成。

> Talk is cheap. Show me the code.--Linus Torvalds

----------
 
**第 0000 题：** 将你的 QQ 头像（或者微博头像）右上角加上红色的数字，类似于微信未读信息数量那种提示效果。

![此处输入图片的描述][1]

**大概思路：**

先读取头像图片，定义所画数字的属性，同时利用随机数对显示的数字进行设置，调用Pillow对图片进行处理。

**背景资料：**

[深入剖析Python最强大图片处理模块--Pillow][2]
    
[Python图像处理库PIL的ImageDraw模块介绍详解][3] 
    
[Pillow学习之ImageDraw][4]  

代码如下：

    from PIL import Image,ImageDraw,ImageFont,ImageColor
    import random #引入随机数
    
    def AddNumToPic(pic,num):#自定义函数
        image =   Image.open(pic)
