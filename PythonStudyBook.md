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
        draw = ImageDraw.Draw(image) #创建Draw对象
        w = image.width  #获取图片的宽度
        h = image.height #获取图片的高度
        font = ImageFont.truetype("arial.ttf", 50)  # 设置字体大小
        fillcolor = ImageColor.colormap.get("red")  # 设置字体颜色
        draw.text((0.85 * w, 0.05 * h), num, font=font, fill=fillcolor)
        image.show()  # 显示图片
        #image.save(‘xxx.jpg’) #在这里写入要保存的地址
    
    def num_of_pic():#如果随机数大于99，则输出99+,大于0且小于99,正常输出
        num = random.randint(1, 200)  # 添加1到200的随机整数
        if num > 99:
            res = '99+'
        else:
            res = str(num)
        return res
    
    if __name__ == '__main__':
        filename = 'xxx上海堡垒.jpg' #在这里写入读取图片的文件地址
        print("正在读取图片……") #提示符
        res = num_of_pic()
        print(res,type(res)) #测试验证随机数的结果
        AddNumToPic(filename, res) #完成任务功能


----------


**第 0001 题：** 做为 Apple Store App 独立开发者，你要搞限时促销，为你的应用**生成激活码**（或者优惠券），使用 Python 如何生成 200 个激活码（或者优惠券）？

**大概思路：**

最简单的一种，先设定激活码为16位。用随机数生成20个数字，转换成16进制数，补齐位数为12位，再根据每次循环中cnt每次递增，将cnt加上1个设定的固定数（标志位），转为八进制数，再变成字符串，最后和前面的随机数生成的字符串拼接为最后的结果。

**代码如下：**

    import random
    #print(ord('Z')) #65~90
    #print(chr(90))
    for cnt in range(1,21):
        seed = random.randint(10000000000, 99999999999)
        num= hex(int(seed))
        cnt_1 = oct(cnt + 1000)[2:] #确保激活码是唯一的，即使随机数有重合的,因为每次循环中，cnt都唯一，总的激活码也是唯一的
        #print(cnt_1)
        str_num = num.zfill(12)
        #print(str_num)
        res = str_num + str(cnt_1)
        print(res)


----------


**第 0002 题:** 将 0001 题生成的 200 个激活码（或者优惠券）保存到 **MySQL** 关系型数据库中。 

**大概思路：**

存储到MySql中，由于MySql为关系型数据库。以二维表的形式储存，可以直接采用0001题的方法。但如果要人为建立关系，方便日后查询搜索，则根据不同用户的激活码不同以及用户表中的主键唯一性，可以在激活码和用户主键（如用户的ID）之间建立某种映射关系。大概思路如下：

 **激活码（16位）==uid（主键）=>16进制数（设置为12位，如果不够12位，开头用0填充）+随机数（四位）
APP开发人员也可以根据激活码的前12位，来推算出用户的主键（uid，用户的ID）。**

***查询如下***：
   

     SELECT column_name FROM INFORMATION_SCHEMA.`KEY_COLUMN_USAGE` 
         WHERE table_name='t_user_id_info' AND constraint_name='PRIMARY'
         AND CONSTRAINT_SCHEMA='app_info' --app_info 为自己的数据库名称
        t_user_id_info为所在的用户id的表

**背景资料：**

[Python十进制和十六进制相互转换][5]

[python为数字前面补0][6]

[主键+随机数生成唯一激活码][7]

[产生唯一随机码的方法][8]


**代码如下：**

    import random
    import string
    
    def from_id_to_num(uid):
        num = hex(int(uid))[2:] #将uid（10进制)转换为16进制,str类型，把16进制数中的0x去掉
        #print(type(num)) #测试
        str_num = num.zfill(12)
        #print(str_num) #测试
        return str_num
    
    def create_num(uid):
        num = random.randint(0, 9999)
        str_num = "{:0>4d}".format(num) #int转为str类型
        #print(str_num,type(str_num))
        str1 = from_id_to_num(uid)
        str = str1 + str_num
        #print(str)
        return str
    
    if __name__ == '__main__': #测试
        #create_num(453274261)
        file = open('xxx/UID.txt', 'r', encoding='utf8')
        count = 1
        for uid in file:
            #print(f'第{count}行：{uid}', end='')
            passward = create_num(uid)
            #file.write(passward)
            count += 1
            #print(passward)
            print(passward[0:4] +'-' +passward[4:8] +'-' +passward[8:12] + '-' + passward[12:16])
        file.close()


----------


**第 0003 题：** 将 0001 题生成的 200 个激活码（或者优惠券）保存到 **Redis** 非关系型数据库中。 

**背景资料**：

 1. 关系型和非关系型数据库的主要差异是数据存储的方式。
 2. 关系型数据天然就是表格式的，因此存储在数据表的行和列中。数据表可以彼此关联协作存储，也很容易提取数据。
 3. 非关系型数据不适合存储在数据表的行和列中，而是大块组合在一起。非关系型数据通常存储在数据集中，就像文档、键值对或者图结构。存储的数据及其特性是选择数据存储和提取方式的首要影响因素。
  
 4. 关系型数据库以二维表的形式储存，而非关数据库以k-v键值对进行储存。   
  
>  SQL：实例–>数据库–>表(table)–>记录行(row)、数据字段(column)   
>     NOSQL：实例–>数据库–>集合(collection)–>键值对(key-value)   
>     SQL需要手动建立数据库，数据表，而NOSQL不需要上述操作。

**代码如下：**

    import random
    import string
    
    def from_id_to_num(uid):
        num = hex(int(uid))[2:] #将uid（10进制)转换为16进制,str类型，把16进制数中的0x去掉
        #print(type(num)) #测试
        str_num = num.zfill(12)
        #print(str_num) #测试
        return str_num
    
    def create_num(uid):
        num = random.randint(0, 9999)
        str_num = "{:0>4d}".format(num) #int转为str类型
        #print(str_num,type(str_num))
        str1 = from_id_to_num(uid)
        str = str1 + str_num
        #print(str)
        return str
    
    if __name__ == '__main__': #测试
        #create_num(453274261)
        file = open('xxx/UID.txt', 'r', encoding='utf8')
        count = 1
        for uid in file:
            #print(f'第{count}行：{uid}', end='')
            passward = create_num(uid)
            #file.write(passward)
            count += 1
            #print(passward)
            print(passward[0:4] +'-' +passward[4:8] +'-' +passward[8:12] + '-' + passward[12:16])
        file.close()


----------


**第 0004 题：** 任一个英文的纯文本文件，统计其中的单词出现的个数。

**大概思路**：
读取纯文本文件，将其中的各个特殊字符提换为空格，将整个文本视为一个长字符串，再对它进行切片。统计切片后的字符串（单个单词）个数即可。

**背景资料：**

[文本字频统计][9]

[get函数用法][10]

**代码如下：**

    import string
    
    def getText():
        txt = open("C:/Users/17146/Desktop/hamlet.txt", "r").read()
        for ch in '!"#$%&()*+,-./:;<=>?@[\\]^_‘{|}~':#将各种特殊字符替换为空格，整个文本视为一个长字符串，便于后续的字符串切片
            txt=txt.replace(ch," ")
        return txt
    if __name__ =='__main__':
        Txt=getText()
        words=Txt.split()
        cnt = 0
        for word in words:
            cnt = cnt + 1
        print(cnt)


----------


**第 0005 题：** 你有一个目录，装了很多照片，把它们的尺寸变成都不大于 iPhone5 分辨率的大小。

**大概思路：**
iPhone 5 的分辨率为 1136$\times$640.
通过OS库中的listdir()方法列出所有的文件，并且通过os.path.splitext(path)分离文件名和扩展名，筛选出扩展名为**'jpg'**的文件。对选出的文件进行尺寸修改，最后保存到设定好的文件位置。

**背景资料：**

[os.path模块][11]

[Python3 os.listdir() 方法][12]


> 1.os.path.abspath(path)

> 返回path规范化的绝对路径。

> 2.os.path.split(path)
> 
> 将path分割成目录和文件名二元组返回。
> 
> 3.os.path.dirname(path)
> 
> 返回path的目录。其实就是os.path.split(path)的第一个元素。
> 
> 4.os.path.basename(path)
> 
> 返回path最后的文件名。如何path以／或\结尾，那么就会返回空值。即os.path.split(path)的第二个元素。
> 
> 5.os.path.commonprefix(list)
> 
> 返回list中，所有path共有的最长的路径。
> 
> 6.os.path.exists(path)
> 
> 如果path存在，返回True；如果path不存在，返回False。
> 
> 7.os.path.isabs(path)
> 
> 如果path是绝对路径，返回True。
> 
> 8.os.path.isfile(path)
> 
> 如果path是一个存在的文件，返回True。否则返回False。
> 
> 9.os.path.isdir(path)
> 
> 如果path是一个存在的目录，则返回True。否则返回False。
> 
> 10.os.path.join(path1[, path2[, ...]])
> 
> 将多个路径组合后返回，第一个绝对路径之前的参数将被忽略。
> 
> 11.os.path.normcase(path)
> 
> 在Linux和Mac平台上，该函数会原样返回path，在windows平台上会将路径中所有字符转换为小写，并将所有斜杠转换为反斜杠。
> 
> 12.os.path.normpath(path)
> 
> 规范化路径。
> 13.os.path.splitdrive(path)
> 
> 返回（drivername，fpath）元组
> 
> 14.os.path.splitext(path)
> 
> 分离文件名与扩展名；默认返回(fname,fextension)元组，可做分片操作
> 
> 15.os.path.getsize(path)
> 
> 返回path的文件的大小（字节）。
> 16.os.path.getatime(path)
> 
> 返回path所指向的文件或者目录的最后存取时间。
> 
> 17.os.path.getmtime(path)
> 
> 返回path所指向的文件或者目录的最后修改时间

[Python PIL Image.save()用法及代码示例][13]

**问题分析：**
[*OSError: cannot write mode RGBA as JPEG的解决办法*][14]

[Python PIL: cannot write mode RGBA as BMP][15]

**PNG有RGBA四个通道，而JPEG是RGB三个通道，所以PNG转BMP时候程序不知道A通道怎么办，就会产生错误。
解决方法就是检查通道数，舍弃A通道。**

代码如下：

    import os
    from PIL import Image
    
    def handle_photo(dir1,dir2):
        list_file = os.listdir(dir1)
        for file in list_file:
            if os.path.splitext(file)[1] == '.jpg':
                image = Image.open(dir1 + '/'+file)
                image = image.convert("RGB")#需要注意：
                image.thumbnail((1160,640))
                image.save(dir2 + '/' + file)
                print("已经过处理的图片：",file)
    if __name__ =="__main__":
        dir1 = "xxx/test1"
        dir2 = "xxx/test2"
        handle_photo(dir1,dir2)


----------


**第 0006 题：** 你有一个目录，放了你一个月的日记，都是 txt，为了避免分词的问题，假设内容都是英文，请统计出你认为每篇日记最重要的词。

**大概思路：**
通过OS库中的listdir()方法列出所有的文件，读取纯文本文件，将其中的各个特殊字符提换为空格，将整个文本视为一个长字符串，再对它进行切片。统计切片后的字符串（单个单词）个数，同时利用字典数据结构，遍历字符串的时候相同的单词如果出现，键值就会加1.最后用items()方法，将字典中的每对元素组成一个元组并且在列表中返回，对元素中的第二个字段(单词出现的次数)进行逆序排序，输出第一个元素的第一个字段，便是出现次数最多的元素。
**资料背景：**
第0004题和0005题的结合，具体可参考第0004题和0005题和**问题分析**。

**问题分析：**

 - ['gbk' codec can't decode byte 0x80 in position 2: illegal multibyte sequence][16] 编码格式的原因，改成"encoding='utf-8"。
 
 - [Python 中的 "%s"%用法][17]
 
 - [items.sort()][18]
 
 - sort()函数用于对列表进行排序，所以要转换成列表数据结构。
 
**代码如下：**

    import os
    
    def getText(dir,file):
        txt = open(dir +'/' + file, "r",encoding='utf-8').read()
        txt=txt.lower() #默认单词的大小写不同，但都是一个单词
        for ch in '!"#$%&()*+,-./:;<=>?@[\\]^_‘{|}~':
            txt=txt.replace(ch," ") #将特殊字符替换为空格
        return txt
    def count_words_txt(dir,file):
        Txt=getText(dir,file)
        words=Txt.split() #进行切片
        counts={}#引入字典
        for word in words: #遍历切片后产生的字符段
            counts[word]=counts.get(word,0)+1 #字典中存的数据类型为{key : value},key 为word. value为 单词出现的次数
            # items() 方法把字典中每对 key 和 value 组成一个元组，并把这些元组放在列表中返回。
        items=list(counts.items())#Python list内置sort()方法用来排序，也可以用python内置的全局sorted()方法来对可迭代的序列排序生成新的序列。
        items.sort(key=lambda x:x[1],reverse=True)## x为任意的，其他也可以。对元素第二个字段排序,逆序排序
        word,count=items[0]
        print(file,":",word)
    def handle(dir):
        list_file = os.listdir(dir)
        for file in list_file:
            if os.path.splitext(file)[1] == '.txt':
                count_words_txt(dir,file)
    if __name__ =="__main__":
        dir = 'xxx/test1'
        handle(dir)


----------


**第 0007 题：** 有个目录，里面是你自己写过的程序，统计一下你写过多少行代码。包括空行和注释，但是要分别列出来。

**大概思路：**
通过OS库中的listdir()方法列出所有的文件，读取满足要求的文件（比如.py文件）。每次读取目录中某一个文件之前先初始化。打开文件，进行读取直到读完整个文件。在读取readline()过程中，出现#号开头，说明该行为注释行；出现引号开头，该行为注释行；如果出现单个的引号开头`line.count('"""') == 1 or line.count("'''") == 1`,(注释行数加1) 就继续一直读行，直到出现下一个引号开头，每读一行，注释行数加1。在这个过程中，读行出现引号，则说明注释到此结束，注释行数加1。如果移除字符串头尾指定的字符（默认为空格或换行符）或字符序列后为空，则空行数加1。最后打印出来统计的各行数。

**背景资料：**

[如何在python中计算空行数][19]

[如何检查一行为空行][20]

 Python **strip()** 方法用于移除字符串头尾指定的字符（默认为空格或换行符）或字符序列。
   注意：该方法只能删除开头或是结尾的字符，不能删除中间部分的字符。 stip()方法语法：
   
   


    str.strip([chars]);
 **startswith()** 方法用于检查字符串是否是以指定子字符串开头，如果是则返回 True，否则返回 False。如果参数 beg 和
   end 指定值，则在指定范围内检查 startswith()方法语法：

    str.startswith(substr, beg=0,end=len(string))
    
**调用readline()可以每次读取一行内容，调用readlines()一次读取所有内容并按行返回list**

**问题分析：**
统计时空格多于实际的：原因分析，其实是多打了几个空格，全选ctrl+A,会发现最末尾有几个空格。

**代码如下:**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/10 23:46
    # @Author :Su
    # @File :test2.py
    # @software:PyCharm
    
    import os
    
    # Python strip() 方法用于移除字符串头尾指定的字符（默认为空格或换行符）或字符序列。
    # 注意：该方法只能删除开头或是结尾的字符，不能删除中间部分的字符。
    # startswith()方法用于检查字符串是否是以指定子字符串开头，如果是则返回True，否则返回False
    def list_files(dir):
        list_file = os.listdir(dir)
        for file in list_file:
          if os.path.splitext(file)[1] == '.py':
            count_sum = 0  # 初始化总行数位0
            count_code = 0  # 初始化代码行数为0
            count_space = 0  # 初始化空行数为0
            count_note = 0  # 初始化注释行数为0
            code_file = open(file, "r", encoding='utf-8')
            while True:#没到文件末尾，就一直读行
              line = code_file.readline()
              if not line:#读完文件，跳出while循环，读取结束
                break
              elif line.strip().startswith('#'):  # 出现#号开头，该行为注释行
                count_note += 1
              elif line.strip().startswith("'''") or line.strip().startswith('"""'):  # 出现引号开头，该行为注释行
                count_note += 1
                if line.count('"""') == 1 or line.count("'''") == 1:  # 如果出现单个的引号开头
                  while True:  # 继续一直读行，直到出现下一个引号开头
                    line = code_file.readline()
                    count_note += 1
                    if ("'''" in line) or ('"""' in line):  # 读行出现引号，则说明注释到此结束
                      break
              elif line.strip():
                count_code += 1
              elif line.strip() =="":
                count_space += 1
              count_sum +=1#每读一行，总行数加1
            #count_sum = count_code + count_note + count_space
            print(f"文件名：{file},总行数:{count_sum},代码行数:{count_code},注释行数:{count_note},空格行数：{count_space}")#格式化输出
            #return count_code, count_note, count_space, count_sum
    
    if __name__ == "__main__":
      dir = "E:/Python"
      list_files(dir)
    #统计时空格多于实际的：原因分析，其实是多打了几个空格，全选ctrl+A,会发现最末尾有几个空格


----------


**第 0008 题：** 一个HTML文件，找出里面的**正文**。

**大概思路：**
导入BeautifulSoup和request库，通过request方法获取网页内容,使用BeautifulSoup解析这段代码,能够得到一个 BeautifulSoup 的对象,并能按照HTML标准的缩进格式的结构输出,而且会把缺失的标签补齐.获取网页的文本信息，用Python的BeautifulSoup提取包含特定子字符串的“a”标记，打印链接。

**背景资料：**

[Python requests中的.content和.text方法的区别][21]

[爬虫之soup.select()用法浅析][22]

***注意：获取元素文本---get_text()
soup.select('#class')[0].get_text():获取文本，由于select()方法获得是list类型，必须要先获取到确定的元素，才能确定文本内容***

**代码如下：**

    import requests
    from bs4 import BeautifulSoup
    
    def search(path):
        page = requests.get(path)
        page.encoding = 'utf-8'
        #print(type(page.text))#str类型
        soup = BeautifulSoup(page.text,'html.parser')
        article = soup.select('.content')[0].text#注意[0]是必须的，因为调用select()返回的是一个list，即使只有一个匹配
        urls = soup.findAll('a')#返回的是一个列表，不是一个单一的结果
        for u in urls:
             print(u['href'])
        print(article)
    
    if __name__ == '__main__':
        search(path='http://mil.news.sina.com.cn/china/2017-04-05/doc-ifycwymx3854291.shtml')


----------


**第 0009 题：** 一个HTML文件，找出里面的**链接**。

   同第0008题


----------


**第 0010 题：** 使用 Python生成类似于下图中的**字母验证码图片**

![此处输入图片的描述][23]

- [阅读资料](http://stackoverflow.com/questions/2823316/generate-a-random-letter-in-python) 

**背景资料：**
[在python的List中使用for循环语句的技巧汇编][24]

    my_list=[ 表达式 for 循环项 in 循环体 (if 条件) ]
[python实战：如何使用python自动生成字母数字验证码图片？][25]

[python 生成验证码图片
来自 《Python项目案例开发从入门到实战》（清华大学出版社 郑秋生 夏敏捷主编）中图像处理——生成二维码和验证码][26]

**大概思路：**
**identifying_code(num, code_len)**函数中,num参数为预计生成的验证码图片数量，code_len参数为验证码的长度。如果没有存在imgs文件，则创建imgs文件。将['1','2','3',……'A','B',……a','b'……,'z']存到列表list中。列表中共62个元素，则采用随机数方法挑选列表中的字符元素并拼接成字符串chars，并借助ImageCaptcha().generate_image()生成图片。保存图片到指定目录。

**代码如下：**

    #captcha 图形验证码生成库,randint用来生成随机数
    from captcha.image import ImageCaptcha
    from random import randint
    import os
    
    def identifying_code(num, code_len):
        if not os.path.exists('imgs'):
            os.mkdir('imgs')
        # 10数字+26大写字母+26小写字母
        list = [chr(i) for i in range(48, 58)] + [chr(i) for i in range(65, 91)] + [chr(i) for i in range(97, 123)]
        for x in range(num):
            chars = ''
            for i in range(code_len):
                rand_num = randint(0, 61)
                chars += list[rand_num]
            image = ImageCaptcha().generate_image(chars)
            print(f"第{x}张:{chars}.jpg")
            image.save('./imgs/' + chars + '.jpg')
    if __name__ == '__main__':
        num = 50
        code_len = 4
        identifying_code(num, code_len)


----------


**第 0011 题：** 敏感词文本文件 filtered_words.txt，里面的内容为以下内容，当用户输入敏感词语时，则打印出 Freedom，否则打印出 Human Rights。

    北京
    程序员
    公务员
    领导
    牛比
    牛逼
    你娘
    你妈
    love
    sex
	jiangge
	
**大概思路：**
读取文本，将每一行文本去掉换行符后存储到列表中，遍历列表，如果列表中的元素出现在用户输入的文字中，则返回True,否则返回False。如果函数值为True,则输出"Freedom"，否则输出"Human Rights"。

**背景资料：**

 - **split()函数**它可以实现字符串的分割操作，并且会将最后处理好的结果保存在一个列表当中，然后再将运行结看成是一个方法返回。基本语法如下：

 split(sep=None, maxsplit=-1)

 - 在这个函数中，我们可以看到它的括号里面包含了两个参数，其中参数sep中设置的就是我们要指定的分割符号，如果不指定，在默认情况下是所有的分隔符都是为空，包括回车、空格、制表符等等；第二个参数设置的是需要分割的次数，默认情况下的分割次数是-1，就是将所有的字符串进行分割。
 
[Python的split函数用法][27]

[如何在Python中删除换行符"\n"][28]

[Python中split函数的返回值是什么？Python中split()函数实例用法讲解][29]

[Python将字符串转换为列表][30]


----------
**代码如下：**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/12 18:47
    # @Author :Su
    # @File :test4.py
    # @software:PyCharm
    
    def filter_words(path,input_words):
        file = open(path,'r',encoding='utf-8')
        words = file.read()#使用read()方法读取文件，返回整个文档的内容（字符串类型），
        list = words.split('\n')#split()返回的是列表
        #print(list)
        for key in list:
            if key in input_words:
                return True
        return False
    
    
    
    if __name__ =="__main__":
        path ="xxx/test.txt"
        while True:
            input_words = input("请输入内容：")
            if(input_words =='end'):
                break
            if filter_words(path, input_words) == True:
                print("Freedom")
            else:
                print("Human Rights")

函数的另外一种写法：

    def filter_words(path,input_words):
        file = open(path,'r',encoding='utf-8')
        list =[]
        for line in file:
            line = line.rstrip('\n')#去掉末尾的换行符
            list.append(line)
        #print(chars)
        for key in list:
            if key in input_words:
                return True
        return False


----------


**第 0012 题：** 敏感词文本文件 filtered_words.txt，里面的内容 和 0011题一样，当用户输入敏感词语，则用 星号 * 替换，例如当用户输入「北京是个好城市」，则变成「**是个好城市」。

**大概思路：**
跟第0011题类似，只需要借助[replace函数][31]替换文本内容即可。

**代码如下：**

    def filter_words(path,input_words):
        file = open(path,'r',encoding='utf-8')
        list =[]
        for line in file:
            line = line.rstrip('\n')#去掉末尾的换行符
            list.append(line)
        #print(chars)
        for key in list:
            if key in input_words:
                input_words = input_words.replace(key,'*'*len(key))
        print(input_words)
    
    
    
    if __name__ =="__main__":
        path ="xxx/test.txt"
        while True:
            input_words = input("请输入内容：")
            if(input_words =='end'):
                break
            else:
                filter_words(path, input_words)


----------


**第 0013 题：** 用 Python 写一个爬图片的程序。

- [参考代码](http://www.v2ex.com/t/61686 "参考代码")


**背景资料：**

 - '.*？'  表示匹配任意字符到下一个符合条件的字符
 - [urllib.request.urlretrieve()函数][32] 下载链接要使用urllib.urlretrieve(
   ),这个函数可以将链接资源下载到本地，如果指定目录的话会下载到目录，否则下载为临时文件。
[Python urllib][33]库用于操作网页URL，并对网页的内容进行抓取处理。
re.findall()：函数返回包含所有匹配项的**列表**。返回string中所有与pattern相匹配的全部字串，返回形式为数组。

**大概思路：**
使用 urllib.request 的 urlopen 方法来打开一个 URL，然后使用 read() 函数获取网页的 HTML 实体代码。**读取页面，读取的是字节串，而不是字符串，需要转换为'utf-8‘**。编译正则匹配模式字符串，在浏览器开发者模式中调出图片的代码格式，并用re.compile()compile ，生成一个正则表达式（ Pattern ）对象，供后续的findall()函数使用。将匹配成功的链接存储到列表中。遍历链接列表，使用urllib.urlretrieve(),将链接资源下载到本地。

**代码如下：**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/12 19:48
    # @Author :Su
    # @File :test5.py
    # @software:PyCharm
    import re
    import os
    import urllib.request
    
    #读取图片网源码，利用re.compile找到其中符合要求的img标签生成图片list，最后用request.urlretrieve下载图片到本地。
    def get_img(url):
        #text = urllib.request.urlopen(url).read()#也可以使用
        request = urllib.request.Request(url)#生成request对象
        content = urllib.request.urlopen(request)#获取页面对象
        text = content.read()#读取页面，读取的是字节串，而不是字符串，需要转换为'utf-8‘
        #text = str(text,'utf-8')
        #print(type(text))
        r = re.compile('<img class="BDE_Image" pic_type="0" .*? src="(.*?)".*?>')# 编译正则匹配模式字符串
        #img_list = r.findall(text.decode('utf-8'))#匹配所有图片链接生成列表
        img_list = re.findall(r,text.decode('utf-8'))
        if not os.path.exists('imgs'):
            os.mkdir('imgs')
        cnt = 0
        print(img_list)
        for img_url in img_list:
            cnt += 1
            print(img_url)
            urllib.request.urlretrieve(img_url,'./imgs/' + str(cnt) + '.jpg')
        print("Done")
    
    
    if __name__ =="__main__":
        url = "https://tieba.baidu.com/p/6052372202"
        get_img(url)


----------


**第 0014 题：** 纯文本文件 student.txt为学生信息, 里面的内容（包括花括号）如下所示：
![此处输入图片的描述][34]

    {
    	"1":["张三",150,120,100],
    	"2":["李四",90,99,95],
    	"3":["王五",60,66,68]
    }

请将上述内容写到 student.xls 文件中。

- [阅读资料](http://www.cnblogs.com/skynet/archive/2013/05/06/3063245.html) 腾讯游戏开发 XML 和 Excel 内容相互转换

**背景资料：**

[Python 操作Excel库xlrd与xlwt常用操作详解][35]

**大概思路：**
文本中的信息为Python字典样式的，打开文件读取文本，将读取的文本从字符串转换为字典数据结构。创建工作簿并创建一个表格，现将字典中的key写入到表格中的第一列(列数col = 0，每次行数row依次加1写入sheet)，再从第一列的基础上，从第二列(col=1)开始,每次行数row依次加1写入sheet。最后保存excel表格到指定目录下。

**代码如下：**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/13 11:14
    # @Author :Su
    # @File :test6.py
    # @software:PyCharm
    import os.path
    
    import xlwt
    
    def insert_data(path):
        text = open(path,'r',encoding='utf-8').read()
        dict = eval(text)#将字符串转换为字典
        #print(text)
        #print(dict)
        workbook = xlwt.Workbook(encoding='utf-8')
        work_sheet = workbook.add_sheet("student")
        col = 0 #列
        row = 0 #行
        for key,val in dict.items():
            work_sheet.write(row,0,key)
            col = 1
            for item in val:
                work_sheet.write(row,col,item)
                col+= 1
            row += 1
        if not os.path.exists('MyExcel'):
            os.mkdir('MyExcel')
        workbook.save('./MyExcel/student.xls')
    
    
    if __name__ =="__main__":
        path = "xxx/test.txt"
        insert_data(path)


----------


**第 0015 题：** 纯文本文件 city.txt为城市信息, 里面的内容（包括花括号）如下所示：

    {
        "1" : "上海",
        "2" : "北京",
        "3" : "成都"
    }

请将上述内容写到 city.xls 文件中，如下图所示：

![此处输入图片的描述][36]
**大概思路：**
同第0014题

**代码如下：**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/13 16:03
    # @Author :Su
    # @File :test7.py
    # @software:PyCharm
    
    import os.path
    
    import xlwt
    
    def insert_data(path):
        text = open(path,'r',encoding='utf-8').read()
        dict = eval(text)#将字符串转换为字典
        #print(text)
        #print(dict)
        workbook = xlwt.Workbook(encoding='utf-8')
        work_sheet = workbook.add_sheet("city")
        col = 0 #列
        row = 0 #行
        for key,val in dict.items():
            work_sheet.write(row,0,key)
            work_sheet.write(row,1,val)
            row += 1
    
        if not os.path.exists('MyExcel'):
            os.mkdir('MyExcel')
        workbook.save('./MyExcel/city.xls')
    
    
    if __name__ =="__main__":
        path = "xxx/test.txt"
        insert_data(path)


----------


**第 0016 题：** 纯文本文件 numbers.txt, 里面的内容（包括方括号）如下所示：

    [
    	[1, 82, 65535], 
    	[20, 90, 13],
    	[26, 809, 1024]
    ]

请将上述内容写到 numbers.xls 文件中，如下图所示：

![此处输入图片的描述][37]

**大概思路：**
思路如同第0015题。

**代码如下：**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/13 16:17
    # @Author :Su
    # @File :test8.py
    # @software:PyCharm
    
    import os.path
    
    import xlwt
    
    def insert_data(path):
        text = open(path,'r',encoding='utf-8').read()
        list = eval(text)#将字符串转换为list
        #print(text)
        #print(list)
        workbook = xlwt.Workbook(encoding='utf-8')
        work_sheet = workbook.add_sheet("numbers")
        col = 0 #列
        row = 0 #行
        for item in list:
            #print(item)
            col = 0
            for info in item:
                #print(info)
                work_sheet.write(row,col,info)
                col += 1
            row += 1
    
        if not os.path.exists('MyExcel'):
            os.mkdir('MyExcel')
        workbook.save('./MyExcel/numbers.xls')
        print("Done")
    
    if __name__ =="__main__":
        path = "xxx/test.txt"
        insert_data(path)


----------


**第 0017 题：** 将 第 0014 题中的 student.xls 文件中的内容写到 student.xml 文件中，如

下所示：

    <?xml version="1.0" encoding="UTF-8"?>
    <root>
    <students>
    <!-- 
    	学生信息表
    	"id" : [名字, 数学, 语文, 英文]
    -->
    {
    	"1" : ["张三", 150, 120, 100],
    	"2" : ["李四", 90, 99, 95],
    	"3" : ["王五", 60, 66, 68]
    }
    </students>
    </root>


**大概思路：**
首先进行预处理数据，将从Excel中获取的数据转换成xml文件中要求的格式。由于xml文件中展示的内容分为列表样式和字典样式，并且字典样式中value值的类型有列表样式和字符串样式，所以引入is_list和is_str两个参数。在预处理函数中，首先打开指定的工作簿，获取工作簿的所有表格，获取所有表格的第一个表格,如果一个表格中有几个sheet，则函数可以引入索引（这里只有一个sheet，不需要引入索引）。如果满足判断的条件，则定义datas为列表或者字典，遍历工作表，将每个单元格的数据存储到datas中。（其余详细见注释）

**背景资料：**
[用Python读写Excel操作][38]

    3.1. xlz格式
    3.1.1. 读取xls格式文件
    def read_xls_excel(url,index):
    读取xls格式文件
    参数：
    url:文件路径
    index：工作表序号（第几个工作表，传入参数从1开始数）
    返回：
    data:表格中的数据
    # 打开指定的工作簿
    workbook = xlrd.open_workbook(url)
    # 获取工作簿中的所有表格
    sheets = workbook.sheet_names()
    # 获取工作簿中所有表格中的的第 index 个表格
    worksheet = workbook.sheet_by_name(sheets[index-1])
    # 定义列表存储表格数据
    data = []
    # 遍历每一行数据
    for i in range(0, worksheet.nrows):
    # 定义表格存储每一行数据
    da = []
    # 遍历每一列数据
    for j in range(0, worksheet.ncols):
    # 将行数据存储到da列表
    da.append(worksheet.cell_value(i, j))
    # 存储每一行数据
    data.append(da)
    # 返回数据
    return data
    3.1.2. 写入xls格式文件def write_xls_excel(url,sheet_name,two_dimensional_data):
    写入xls格式文件
    参数：
    url:文件路径
    sheet_name:表名
    two_dimensional_data：将要写入表格的数据（二维列表）
    # 创建工作簿对象
    workbook = xlwt.Workbook()
    # 创建工作表对象
    sheet = workbook.add_sheet(sheet_name)
    # 遍历每一行数据
    for i in range(0,len(two_dimensional_data)):
    # 遍历每一列数据
    for j in range(0,len(two_dimensional_data[i])):
    # 写入数据
    sheet.write(i,j,two_dimensional_data[i][j])
    # 保存
    workbook.save(url)
    print("写入成功")
    3.1.3. 追加写入xls格式文件def write_xls_excel_add(url, two_dimensional_data, index):
    追加写入xls格式文件
    参数：
    url:文件路径
    two_dimensional_data：将要写入表格的数据（二维列表）
    index：指定要追加的表的序号（第几个工作表，传入参数从1开始数）
    # 打开指定的工作簿
    workbook = xlrd.open_workbook(url)
    # 获取工作簿中的所有表格
    sheets = workbook.sheet_names()
    # 获取指定的表
    worksheet = workbook.sheet_by_name(sheets[index-1])
    # 获取表格中已存在的数据的行数
    rows_old = worksheet.nrows
    # 将xlrd对象拷贝转化为xlwt对象
    new_workbook = copy(workbook)
    # 获取转化后工作簿中的第index个表格
    new_worksheet = new_workbook.get_sheet(index-1)
    # 遍历每一行数据
    for i in range(0, len(two_dimensional_data)):
    # 遍历每一列数据
    for j in range(0, len(two_dimensional_data[i])):
    # 追加写入数据，注意是从i+rows_old行开始写入
    new_worksheet.write(i+rows_old, j, two_dimensional_data[i][j])
    # 保存工作簿
    new_workbook.save(url)
    print("追加写入成功")
    3.2. xlsx格式 3.2.1. 读取xlsx格式文件def read_xlsx_excel(url, sheet_name):
    读取xlsx格式文件
    参数：
    url:文件路径
    sheet_name:表名
    返回：
    data:表格中的数据
    # 使用openpyxl加载指定路径的Excel文件并得到对应的workbook对象
    workbook = openpyxl.load_workbook(url)
    # 根据指定表名获取表格并得到对应的sheet对象
    sheet = workbook[sheet_name]
    # 定义列表存储表格数据
    data = []
    # 遍历表格的每一行
    for row in sheet.rows:
    # 定义表格存储每一行数据
    da = []
    # 从每一行中遍历每一个单元格
    for cell in row:
    # 将行数据存储到da列表
    da.append(cell.value)
    # 存储每一行数据
    data.append(da)
    # 返回数据
    return data
    3.2.2. 写入xlsx格式文件
    def write_xlsx_excel(url, sheet_name, two_dimensional_data):
    写入xlsx格式文件
    参数：
    url:文件路径
    sheet_name:表名
    two_dimensional_data：将要写入表格的数据（二维列表）
    # 创建工作簿对象
    workbook = openpyxl.Workbook()
    # 创建工作表对象
    sheet = workbook.active
    # 设置该工作表的名字
    sheet.title = sheet_name
    # 遍历表格的每一行
    for i in range(0, len(two_dimensional_data)):
    # 遍历表格的每一列
    for j in range(0, len(two_dimensional_data[i])):
    # 写入数据（注意openpyxl的行和列是从1开始的，和我们平时的认知是一样的）
    sheet.cell(row=i + 1, column=j + 1, value=str(two_dimensional_data[i][j]))
    # 保存到指定位置
    workbook.save(url)
    print("写入成功")
    3.2.3. 追加写入xlsx格式文件def write_xlsx_excel_add(url, sheet_name, two_dimensional_data):
    追加写入xlsx格式文件
    参数：
    url:文件路径
    sheet_name:表名
    two_dimensional_data：将要写入表格的数据（二维列表）
    # 使用openpyxl加载指定路径的Excel文件并得到对应的workbook对象
    workbook = openpyxl.load_workbook(url)
    # 根据指定表名获取表格并得到对应的sheet对象
    sheet = workbook[sheet_name]
    for tdd in two_dimensional_data:
    sheet.append(tdd)
    # 保存到指定位置
    workbook.save(url)
    print("追加写入成功")

[Python中json.dumps()函数的使用解析][39]

[python操作XML格式文件的一些常见方法][40]

[浅谈Python解析XML的利器——xml.etree.ElementTree][41]


**代码如下：**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/14 10:41
    # @Author :Su
    # @File :test9.py
    # @software:PyCharm
    
    #xlrd和xlwt是python的第三方库，xlrd模块实现对excel文件内容读取，xlwt模块实现对excel文件的写入
    
    import xlrd
    from lxml import etree
    import json
    import os
    #由于xml文件中展示的内容分为列表样式和字典样式，并且字典样式中value值的类型有列表样式和字符串样式，所以引入is_list和is_str
    def get_data(path,is_list,is_str):#path为xls文件的路径
        workbook =xlrd.open_workbook(path)#打开指定的工作簿
        sheets = workbook.sheet_names()#获取工作簿的所有表格
        worksheet = workbook.sheet_by_name(sheets[0])#获取所有表格的第一个表格,如果一个表格中有几个sheet，则函数可以引入索引。
        if is_list ==True and is_str == False:#对应numbers.xls
            datas = []
            for item in range(0, worksheet.nrows):  # 遍历工作表的行
                data = []  # 定义表格存储每一行数据，每用完一行直接清空
                for entry in range(0, worksheet.ncols):  # 遍历工作表的列
                    data.append(worksheet.cell_value(item, entry))  # cell()返回的是单元格的数据类型和单元格的值。cell_value()返回的只是单元格的值
                datas.append(data)
        elif is_list == False and is_str == False:#对应student.xls
            datas = {}
            for item in range(0,worksheet.nrows):
                datas[item+1] = worksheet.row_values(item)[1:]
        elif is_list == False and is_str == True:#对应city.xls
            datas = {}
            for item in range(0,worksheet.nrows):
                string = str(worksheet.row_values(item)[1])#转换为字符串
                datas[item + 1] = string
        #print(datas)
        #print(type(datas))#list or dict
        print("Done")
        return datas
    
    def convert(filename,childname,data,info):# info为xml文件的说明信息
        # 创建根节点
        root = etree.Element("root")
        # 创建子节点
        child= etree.SubElement(root, childname)
        # 添加子节点的说明信息
        child.append(etree.Comment(info))
        # 设置子节点的内容
        #json.dumps() 是把python对象转换成json对象的一个过程，生成的是字符串。json.dump() 是把python对象转换成json对象生成一个fp的文件流，和文件相关。
        child.text = json.dumps(data, ensure_ascii=False)#设置成false，可以输出中文
        # 将根节点放入xml节点中
        data_xml = etree.ElementTree(root)
        if not os.path.exists('./MyXml'):#如果没有MyXml文件夹，则创建它。
            os.mkdir('./MyXml')
        path = './MyXml' + '/' + filename
        data_xml.write(path, xml_declaration=True, encoding='utf-8')#xml文件写入
    
    if __name__ =="__main__":
    
        inputs = input("请输入：")
        if inputs =='student.xls':
            path_student = "E:/Python/MyExcel/student.xls"
            info_student = ' 学生信息表 "id" : [名字, 数学, 语文, 英文]'
            xml_file_student = "student.xml"
            child_name = "students"
            data_student = get_data(path_student, False,False)
            convert(xml_file_student, child_name,data_student, info_student)
    
        elif inputs =='city.xls':
            path_city = "E:/Python/MyExcel/city.xls"
            info_city = "城市信息"
            xml_file_city = 'city.xml'
            child_name = "cities"
            data_city = get_data(path_city, False,True)
            convert(xml_file_city,child_name, data_city, info_city)
    
        elif inputs =='numbers.xls':
            path_numbers = "E:/Python/MyExcel/numbers.xls"
            info_numbers = "数字信息"
            xml_file_numbers = 'numbers.xml'
            child_name = "numbers"
            data_numbers = get_data(path_numbers, True,False)
            convert(xml_file_numbers,child_name, data_numbers, info_numbers)
        else:
            print("无此文件")


----------


**第 0018 题：** 将 第 0015 题中的 city.xls 文件中的内容写到 city.xml 文件中，如下
所示：

```
    <?xmlversion="1.0" encoding="UTF-8"?>
    <root>
    <cities>
    <!-- 
    	城市信息
    -->
    {
    	"1" : "上海",
    	"2" : "北京",
    	"3" : "成都"
    }
    </cities>
    </root>
```
同第0017题

----------


**第 0019 题：** 将 第 0016 题中的 numbers.xls 文件中的内容写到 numbers.xml 文件中，如下

所示：

    <?xml version="1.0" encoding="UTF-8"?>
    <root>
    <numbers>
    <!-- 
    	数字信息
    -->
    
    [
    	[1, 82, 65535],
    	[20, 90, 13],
    	[26, 809, 1024]
    ]
    
    </numbers>
    </root>

同第0017题

----------


**第 0020 题：** [登陆中国联通网上营业厅](http://iservice.10010.com/index_.html) 后选择「自助服务」 --> 「详单查询」，然后选择你要查询的时间段，点击「查询」按钮，查询结果页面的最下方，点击「导出」，就会生成类似于 2014年10月01日～2014年10月31日通话详单.xls 文件。写代码，对每月通话时间做个统计。

**大概思路：**
定义时间转换函数convert和数据预处理函数pre_treatment，便于后续使用。打开指定工作簿并获取工作簿中的所有表格,定义主叫时间和被叫时间并遍历Excel表格中的数据记录下来。由于通话时间内单元格内的格式不统一，有的只有秒，有的有分和秒，（1分3秒,11秒等包含中文的数据不能直接使用），对通话时长数据进行预处理，并得到总时间（单位为秒）。如果是主叫，则调用数据预处理函数处理主叫通话数据，得到主叫总时间；如果是被叫，则调用数据预处理函数处理被叫通话数据，得到被叫总时间，最后进行格式化输出。

**参考资料：**
[python time模块之time.strptime()][42]仅供参考。
这里并不适用，因为`通话时长`每个单元格内的格式不统一，有的只有秒，有的有分和秒，所以对`通话时长`数据进行自定义函数来预处理。

[彻底搞懂Python切片操作][43] 

**代码如下：**

    # -*- codeing=utf-8 -*-
    # @Time : 2023/2/15 12:13
    # @Author :Su
    # @File :test10.py
    # @software:PyCharm
    
    import xlrd
    import time
    #time.strptime不可用,每个单元格内的格式不统一，有的只有秒，有的有分和秒，所以对通话时长数据进行预处理
    #1分3秒,11秒等包含中文的数据不能直接使用
    def pre_treatment(words):#预处理数据
        for word in '时分秒':
            words = words.replace(word,' ')
        item = words.split()#切片，分成数字样式
        length = len(item)
        total_time = 0#定义总时长
        if length == 3:#有三个元素,对应时分秒
            total_time = int(item[0])*3600 + int(item[1])*60 +int(item[2])
        elif length == 2:#有两个元素,对应分秒
            total_time = int(item[0])*60 +int(item[1])
    
        return total_time
    
    def convert(num):#时间转换工具
        hours = num // 3600
        mins = (num - hours * 3600) // 60
        sec = num - hours * 3600 - mins * 60
        return hours,mins,sec
    
    def get_msg(path):
        workbook = xlrd.open_workbook(path)#打开指定的工作簿
        sheets = workbook.sheet_names()#获取工作簿的所有表格
        worksheet = workbook.sheet_by_name(sheets[0])#获取所有表格的第一个表格,如果一个表格中有几个sheet，则函数可以引入索引。
        act_time = 0#主动通话时间
        pass_time = 0 #被动通话时间
        for item in range(1,worksheet.nrows):
            if worksheet.cell_value(item,4) == '主叫':
                act = worksheet.cell_value(item,3)
               #print(type(act_time))
                act_time += pre_treatment(act)
    
            elif worksheet.cell_value(item,4) == '被叫':
                pas = worksheet.cell_value(item, 3)
                # print(type(pass_time))
                pass_time += pre_treatment(pas)
    
        total = 0
        total = act_time + pass_time
    
        act_hours = convert(act_time)[0]
        act_mins = convert(act_time)[1]
        act_sec = convert(act_time)[2]
    
        pass_hours = convert(pass_time)[0]
        pass_mins = convert(pass_time)[1]
        pass_sec = convert(pass_time)[2]
    
        total_hours = convert(total)[0]
        total_mins = convert(total)[1]
        total_sec = convert(total)[2]
    
        print(f'主叫时间：{act_hours}时{act_mins}分{act_sec}秒\n被叫时间:{pass_hours}时{pass_mins}分{pass_sec}秒\n'
            f'通话总时间:{total_hours}时{total_mins}分{total_sec}秒')
    if __name__ =="__main__":
        get_msg("xxx/2023年01月语音通信.xls")


----------


**第 0021 题：** 通常，登陆某个网站或者 APP，需要使用用户名和密码。密码是如何加密后存储起来的呢？请使用 Python 对密码加密。

- 阅读资料 [用户密码的存储与 Python 示例](http://zhuoqiang.me/password-storage-and-python-example.html)

- 阅读资料 [Hashing Strings with Python](http://www.pythoncentral.io/hashing-strings-with-python/)

- 阅读资料 [Python's safest method to store and retrieve passwords from a database](http://stackoverflow.com/questions/2572099/pythons-safest-method-to-store-and-retrieve-passwords-from-a-database)

**第 0022 题：** iPhone 6、iPhone 6 Plus 早已上市开卖。请查看你写得 第 0005 题的代码是否可以复用。

**第 0023 题：** 使用 Python 的 Web 框架，做一个 Web 版本 留言簿 应用。

[阅读资料：Python 有哪些 Web 框架](http://v2ex.com/t/151643#reply53)

- ![!\[留言簿参考\](http://i.imgur.com/VIyCZ0i.jpg)][44]


**第 0024 题：** 使用 Python 的 Web 框架，做一个 Web 版本 TodoList 应用。

- ![!\[SpringSide 版TodoList\](http://i.imgur.com/NEf7zHp.jpg)][45]

**第 0025 题：** 使用 Python 实现：对着电脑吼一声,自动打开浏览器中的默认网站。


    例如，对着笔记本电脑吼一声“百度”，浏览器自动打开百度首页。
    
    关键字：Speech to Text
    
参考思路：    
1：获取电脑录音-->WAV文件
    python record wav

2：录音文件-->文本

    STT: Speech to Text
    
    STT API Google API

3:文本-->电脑命令


  [1]: https://camo.githubusercontent.com/2e637daaedbb872ae5949028a692575d253f84c020b7634f9dc64b786cc0f3f8/687474703a2f2f692e696d6775722e636f6d2f736732646b75592e706e673f31
  [2]: https://www.51cto.com/article/657201.html
  [3]: https://cloud.tencent.com/developer/article/1743194
  [4]: https://blog.csdn.net/Triagen/article/details/60483569
  [5]: https://blog.csdn.net/zsqianqiu/article/details/124794275
  [6]: https://blog.csdn.net/qq_39400324/article/details/127138163
  [7]: https://linsir.org/post/Creat-the-unique-activation-code-with-python/
  [8]: http://www.blogjava.net/BearRui/archive/2010/10/19/unique_random_code.html
  [9]: https://python123.io/student/courses/2971/groups/35639/problems/programmings/6925#pagetop
  [10]: https://blog.csdn.net/wzk4869/article/details/126019160
  [11]: https://blog.csdn.net/weixin_39747511/article/details/110062344
  [12]: https://www.runoob.com/python3/python3-os-listdir.html
  [13]: https://vimsky.com/examples/usage/python-pil-image-save-method.html
  [14]: http://www.zyiz.net/tech/detail-248058.html
  [15]: https://blog.csdn.net/smallflyingpig/article/details/56036771
  [16]: https://blog.csdn.net/ThePythonFucker/article/details/124057591
  [17]: https://blog.csdn.net/caizhige123/article/details/120504666
  [18]: https://blog.csdn.net/weixin_44769957/article/details/109479281
  [19]: http://www.python88.com/topic/130315
  [20]: https://www.dovov.com/python-878.html
  [21]: https://blog.csdn.net/weixin_44245595/article/details/115364128?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~OPENSEARCH~Rate-1-115364128-blog-79946377.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~OPENSEARCH~Rate-1-115364128-blog-79946377.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=1
  [22]: https://blog.csdn.net/geerniya/article/details/77842421
  [23]: https://camo.githubusercontent.com/4627b1581cdfb609a952ded951787676be46da87c3b93e36fb922720c109e4d9/687474703a2f2f692e696d6775722e636f6d2f615668626567562e6a7067
  [24]: https://blog.csdn.net/weixin_42608414/article/details/109923442
  [25]: https://www.py.cn/faq/python/26754.html
  [26]: https://www.cnblogs.com/ttweixiao-IT-program/p/13528427.html
  [27]: https://baijiahao.baidu.com/s?id=1743592339277855443&wfr=spider&for=pc
  [28]: https://www.zkxjob.com/48728
  [29]: https://www.ycpai.cn/python/8mtEDjr5.html
  [30]: https://cloud.tencent.com/developer/article/2095693?from=15425&areaSource=102001.1&traceId=jmF3-IxLu1pzWflNP9zP_
  [31]: https://www.ycpai.cn/python/LKbpzfv2.html
  [32]: https://blog.csdn.net/zh54b5n64vn64654/article/details/89079057
  [33]: https://www.runoob.com/python3/python-urllib.html
  [34]: https://camo.githubusercontent.com/482212d1c760814fd3232fd7c09b5f43c739ef37f6f87e3720938e648d77151e/687474703a2f2f692e696d6775722e636f6d2f6e50446c706d652e6a7067
  [35]: https://baijiahao.baidu.com/s?id=1692102953555208325&wfr=spider&for=pc
  [36]: https://camo.githubusercontent.com/92d72f443b5fc3656ebe1e417e1896eb3a79c566ca84d7520c4229cc8ce00e13/687474703a2f2f692e696d6775722e636f6d2f724f4862557a672e706e67
  [37]: https://camo.githubusercontent.com/c3cfb78e84c98f642397186b369a1a399fb58b77550806972591392b133b5c67/687474703a2f2f692e696d6775722e636f6d2f69757a305062762e706e67
  [38]: https://3g.163.com/dy/article/HK9FLT100519EA27.html
  [39]: https://www.jb51.net/article/212376.htm
  [40]: https://www.jb51.net/article/255885.htm
  [41]: https://zhuanlan.zhihu.com/p/502584681
  [42]: https://blog.csdn.net/sinat_37960022/article/details/110952636
  [43]: https://www.cnblogs.com/guoguo-15/p/12718640.html
  [44]: https://camo.githubusercontent.com/2d18b34367e1cf038c2ee3686db4a25cc774378411559f1a9ce74e01d69b3c62/687474703a2f2f692e696d6775722e636f6d2f564979435a30692e6a7067
  [45]: https://camo.githubusercontent.com/0f08a8e52ff727aab1e8ed5a3ee2e7af3ecdfef9dd2a1567ab22503d3dd96d8c/687474703a2f2f692e696d6775722e636f6d2f4e4566377a48702e6a7067
