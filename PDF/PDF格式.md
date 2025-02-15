# PDF格式学习
## PDF简介
* PDF是Portable Document Format 的缩写，可翻译为“便携文件格式”，由Adobe System Incorporated 公司在1992年发明。

* PDF文件是一种编程形式的文档格式，它所有显示的内容，都是通过相应的操作符进行绘制的。 
* PDF基本显示单元包括：文字，图片，矢量图，图片 
* PDF扩展单元包括：水印，电子署名，注释，表单，多媒体，3D 
* PDF动作单元：书签，超链接（拥有动作的单元有很多个，包括电子署名，多媒体等等）
## PDF的优点
* 一致性： 
在所有可以打开PDF的机器上，展示的效果是完全一致，不会出现段落错乱、文字乱码这些排版问题。尤其是文档中，本身可以嵌入字体，避免了客户端没有对应字体，而导致文字显示不一致的问题。所以，在印刷行业，绝大多数用的都是PDF格式。 
* 不易修改： 
用过PDF文件的人，都会知道，对已经保存之后的PDF文件，想要进行重新排版，基本上就不可能的，这就保证了从资料源发往外界的资料，不容易被篡改。 
* 安全性： 
PDF文档可以进行加密，包括以下几种加密形式：文档打开密码，文档权限密码，文档证书密码，加密的方法包括：RC4，AES，通过加密这种形式，可以达到资料防扩散等目的。 
* 不失真： 
PDF文件中，使用了矢量图，在文件浏览时，无论放大多少倍，都不会导致使用矢量图绘制的文字，图案的失真。 
* 支持多种压缩方式： 
为了减少PDF文件的size，PDF格式支持各种压缩方式： asciihex，ascii85，lzw，runlength，ccitt，jbig2，jpeg(DCT)，jpeg2000(jpx) 
* 支持多种印刷标准： 
支持PDF-A，PDF-X

## PDF格式
根据PDF官方指南，理解PDF格式可以从四个方面下手——**Objects**（对象）、**File structure**（物理文件结构）、**Document structure**（逻辑文件结构）、**Content streams**（内容流）。

### 对象

### 物理文件结构
* 整体上分为文件头（Header）、对象集合（Body）、交叉引用表（Xref table）、文件尾（Trailer）四个部分，结构如图。修改过的PDF结构会有部分变化。
* 未经修改
![未经修改](https://img-blog.csdnimg.cn/20190526170017719.png#pic_center)
* 经修改
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190526170402806.png#pic_center)
#### 文件头
* 文件头是PDF文件的第一行,格式如下: 
```
%PDF-1.7
```
* 这是个固定格式，表示这个PDF文件遵循的PDF规范版本，解析PDF的时候尽量支持高版本的规范，以保证支持大多数工具生成的PDF文件。1.7版本支持1.0-1.7之间的所有版本。

#### 对象集合
* 这是一个PDF文件最重要的部分，文件中用到的所有对象,包括文本、图象、音乐、视频、字体、超连接、加密信息、文档结构信息等等,都在这里定义。格式如下: 
```
2 0 obj
        ...
end obj
```
* 一个对象的定义包含4个部分：前面的2是**对象序号**，其用来唯一标记一个对象；0是**生成号**，按照PDF规范，如果一个PDF文件被修改，那这个数字是累加的，它和对象序号一起标记是原始对象还是修改后的对象，但是实际开发中，很少有用这种方式修改PDF的，都是重新编排对象号；obj和endobj是对象的定义范围，可以抽象的理解为这就是一个左括号和右括号；省略号部分是PDF规定的任意合法对象。
* 可以通过R关键字来引用任何一个对象，比如要引用上面的对象，可以使用2 0 R，需要主意的是，R关键字不仅可以引用一个已经定义的对象，还可以引用一个并**不存在的对象**，而且效果就和引用了一个空对象一样。
*  对象主要有下面几种
   * **booleam**
用关键字true或false表示，可以是array对象的一个元素,或dictionary对象的一个条目。也可以用在PostScript计算函数里面，做为if或if esle的一个条件。
   * **numeric**
包括整形和实型，不支持非十进制数字，不支持指数形式的数字。
    例:
    1)整数 123   4567   +111   -2
    范围:正2的31次方-1到负的2的31次方
    2)实数 12.3   0.8   +6.3   -4.01   -3.   +.03
    范围:±3.403 ×10的38次方 ±1.175 × 10的-38次方
       * 注意:如果整数超过表示范围将转化成实数,如果实数超过范围就会出错
   * **string**
由一系列0-255之间的字节组成，一个string总长度不能超过65535.string有以下两种方式：
     * 十六进制字串
由<>包含起来的一个16进制串，两位表示一个字符,不足两位用0补齐。
例: \<Aabb> 表示AA和BB两个字符  \<AAB> 表示AA和B0两个字符
      * 直接字串
由()包含起来的一个字串,中间可以使用转义符"/"。 
例：
 (abc) 表示abc   
 (a//) 表示a/  
转义符的定义如下：

|转义字符|	含义|	
|--------|--------|
|/n	|换行|
/r	|回车
/t	|水平制表符
/b	|退格
/f	|换页（Form feed (FF)）
/(	|左括号
/)	|右括号
//	|反斜杠
/ddd	|八进制形式的字符


* 对象类别（续）

  * **name**
    由一个前导/和后面一系列字符组成，最大长度为127。和string不同的是，name是**不可分割**的并且是**唯一**的，不可分割就是说一个name对象就是一个原子，比如/name，不能说n就是这个name的一个元素；唯一就是指两个相同的name一定代表同一个对象。从pdf1.2开始，除了ascii的0，别的都可以用一个#加两个十六进制的数字表示。
    例:  
    /name 表示name  
    /name#20is 表示name is  
    /name#200 表示name 0
   * **array**
用[]包含的一组对象，可以是任何pdf对象(包括array)。虽然pdf只支持一维array，但可以通过array的嵌套实现任意维数的array(但是一个array的元素不能超过8191)。
    例：[549   3.14   false   (Ralph)   /SomeName]  
  * **Dictionary**
    用"<<"和">>"包含的若干组条目，每组条目都由key和value组成，其中key必须是name对象，并且一个dictionary内的key是唯一的；value可以是任何pdf的合法对象(包括dictionary对象)。
    例：
    ```
    <<  /IntegerItem   12
        /StringItem   (a   string)
        /Subdictionary
        <<  /Item1   0.4
            /Item2   true
            /LastItem   (not!)
            /VeryLastItem   (OK)
        >>
    >>
    ```
   * **stream**
    由一个字典和紧跟其后面的一组关键字stream和endstream以及这组关键字中间包含一系列字节组成。内容和string很相似，但有区别：stream可以分几次读取，分开使用不同的部分，string必须作为一个整体一次全部读取使用；string有长度限制，但stream却没有这个限制。一般较大的数据都用stream表示。需要注意的是，stream必须是间接对象，并且stream的字典必须是直接对象。从1.2规范以后，stream可以以外部文件形式存在，这种情况下，解析PDF的时候stream和endstream之间的内容就被忽略掉。
     例:
      ```
      dictionary
      stream
      …data…
      endstream
      ```
     stream字典中常用的字段如下：

      |字段名	|类型|	值|
      |--------|--------|--------|
      |Length|	整形|（必须）关键字stream和endstream之间的数据长度，endstream之前可能会有一个多余的EOL标记，这个不计算在数据的长度中。
     Filter	|名字 或 数组	|（可选）Stream的编码算法名称（列表）。如果有多个，则数组中的编码算法列表顺序就是数据被编码的顺序。
     DecodeParms	|字典 或 数组	|（可选)一个参数字典或由参数字典组成的一个数组，供Filter使用。如果仅有一个Filter并且这个Filter需要参数，除非这个Filter的所有参数都已经给了默认值，否则的话   DecodeParms必须设置给Filter。如果有多个Filter，并且任意一个Filter使用了非默认的参数， DecodeParms 必须是个数组，每个元素对应一个Filter的参数列表（如果某个Filter无需参数或所有参数都有了默认值，就用空对象代替）。 如果没有Filter需要参数，或者所有Filter的参数都有默认值，DecodeParms 就被忽略了。
     F	|文件标识	|（可选)保存stream数据的文件。如果有这个字段， stream和endstream就被忽略，FFilter将会代替Filter, FDecodeParms将代替DecodeParms。Length字段还是表示stream和endstream之间数据的长度，但是通常此刻已经没有数据了，长度是0.
     FFilter	|名字 或 字典|	(可选)和filter类似，针对外部文件。
     FDecodeParms	|字典 或 数组|	(可选)和DecodeParams类似，针对外部文件。
     
   
   
Stream的编码算法名称（列表）。如果有多个，则数组中的编码算法列表顺序就是数据被编码的顺序。且需要被编码。编码算法主要如下：
     ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190526185703968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x5YzQ0ODEzNDE4,size_16,color_FFFFFF,t_70)
编码可视化主要显示为乱码，所以提供了隐藏信息的机会,如下图的steam内容为乱码。
     ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190526185800381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x5YzQ0ODEzNDE4,size_16,color_FFFFFF,t_70#pic_center)   
    * **NULL**
    用null表示，代表空。如果一个key的值为null，则这个key可以被忽略；如果引用一个不存在的object则等价于引用一个空对象。

#### 交叉引用表
* 交叉引用表是PDf文件内部一种特殊的文件组织方式，可以很方便的根据对象号随机访问一个对象。其格式如下: 
```
    xref
  	0 1
  	0000000000   65535   f
  	4 1
    0000000009   00000   n
  	8 3
    0000000074   00000   n
  	0000000120   00000   n
  	0000000179   00000   n
```
 * 其中,xref是开始标志,表示以下为一个交叉引用表的内容;每个交叉引用表又可以分为若干个子段，每个子段的第一行是两个数字，第一个是对象起始号，后面是连续的对象个数，接着每行是这个子段的每个对象的具体信息——每行的前10个数字代表这个这个对象**相对文件头的偏移地址**,后面的5位数字是**生成号**（用于标记PDF的更新信息，和对象的生成号作用类似），最后一位f或n表示对象是否被使用(n表示使用,f表示被删除或没有用)。上面这个交叉引用表一共有3个子段，分别有1个，1个，3个对象，第一个子段的对象不可用，其余子段对象可用。
####  文件尾
* 通过trailer可以快速的找到交叉引用表的位置，进而可以精确定位每一个对象；还可以通过它本身的字典还可以获取文件的一些全局信息（作者，关键字，标题等），加密信息，等等。具体形式如下:
```
  	trailer
  	<<
    	key1   value1
    	key2   value2
    	key3   value3
        …
  	>>
  	startxref
  	553
  	%%EOF
  ```




* trailer后面紧跟一个字典，包含若干键-值对。具体含义如下：

|键	|值类型|	值说明|
|--------|--------|--------|
|Size|	整形数字|	所有间接对象的个数。一个PDF文件，如果被更新过，则会有多个对象集合、交叉引用表、trailer，最后一个trailer的这个字段记录了之前所有对象的个数。这个值必须是直接对象。|
|Prev	|整形数字|	当文件有多个对象集合、交叉引用表和trailer时，才会有这个键，它表示前一个相对于文件头的偏移位置。这个值必须是直接对象。|
|Root	|字典	|Catalog字典（文件的逻辑入口点）的对象号。必须是间接对象。|
|Encrypt	|字典|	文档被保护时，会有这个字段，加密字典的对象号。|
|Info	|字典	|存放文档信息的字典，必须是间接对象。|
|ID	|数组	|文件的ID|

* 上面代码中的startxref：后面的数字表示最后一个交叉引用表相对于文件起始位置的偏移量   
* %%EOF：文件结束符
### 逻辑文件结构
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190526185950801.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x5YzQ0ODEzNDE4,size_16,color_FFFFFF,t_70#pic_center)

#### catalog根节点
* catalog是整个PDF逻辑结构的根节点，这个可以通过trailer的Root字段定位，虽然简单，但是相当重要，因为这里是PDF文件物理结构和逻辑结构的连接点。Catalog字典包含的信息非常多，这里仅就最主要的几个字段做个说明。
     *  Pages字段
这是个必须字段，是PDF里面所有页面的描述集合。Pages字段本身是个字典，它里面又包含了一下几个主要字段：
    
    |字段	|类型	|值|
    |--------|--------|--------|
    Type	|name|	(必须)只能为Pages 。
    Parent	|dictionary	|(如果不是catalog里面指定的跟节点，则必须有，并且必须是间接对象) 当前节点的直接父节点。
    Kids	|array	|(必须)一个间接对象组成的数组，节点可能是page或page tree。
    Count	|integer|	(必须) page tree里面所包含叶子节点（page 对象）的个数。
    
    从以上字段可以看出，Pages最主要的功能就是组织所有的page对象。Page对象描述了一个PDF页面的属性、资源等信息。Page对象是一个字典，它主要包含一下几个重要的属性：
    
 |字段	|类型	|值|
|--------|--------|--------|
Type	|name	|(必须)必须是Page。
Parent	|dictionary|	(必须；并且只能是间接对象)当前page节点的直接父节点page tree 。
LastModified|	date|	(如果存在PieceInfo字段，就必须有，否则可选)记录当前页面被最后一次修改的日期和时间。
Resources|	dictionary|	(必须; 可继承)记录了当前page用到的所有资源。如果当前页不用任何资源，则这是个空字典。忽略所有字段则表示继承父节点的资源。
MediaBox	|rectangle|	(必须; 可继承)定义了要显示或打印页面的物理媒介的区域（default user space units）
CropBox	|rectangle|	(可选; 可继承)定义了一个可视区域，当前页被显示或打印的时候，它的内容会被这个区域裁剪。默认值就是 MediaBox。
BleedBox|rectangle	|(可选) 定义了一个区域，当输出设备是个生产环境（ production environment）的时候，页面显示的内容会被裁剪。默认值是 CropBox.
Contents	|stream or array|	(可选) 描述页面内容的流。如果这个字段缺省，则页面上什么也不会显示。这个值可以是一个流，也可以是由几个流组成的一个数组。如果是数组，实际效果相当于所有的流是按顺序连在一起的一个流，这就允许PDF生成的时候可以随时插入图片或其他资源。流之间的分割只是词汇上的一个分割，并不是逻辑上或者组织形式的切割。
Rotate	|integer|	(可选; 可继承) 顺时钟旋转的角度数，这个必须是90的整数倍，默认是0。
Thumb|	stream	|(可选)定义当前页的缩略图。
Annots|	array|	(可选) 和当前页面关联的注释。
Metadata	|stream|	(可选) 当前页包含的元数据。
一个简单例子：
```
3 0 obj
       << /Type /Page
           /Parent 4 0 R
           /MediaBox [ 0 0 612 792 ]
           /Resources <</Font<<
                        /F3 7 0 R /F5 9 0 R /F7 11 0 R
                             >>
                        /ProcSet [ /PDF ]
                       >>
           /Contents 12 0 R
           /Thumb 14 0 R
           /Annots [ 23 0 R 24 0 R]
         >> 
endobj
```
* Outlines字段
Outline是PDF里面为了方便用户从PDF的一部分跳转到另外一部分而设计的，有时候也叫书签（Bookmark），它是一个树状结构，可以直观的把PDF文件结构展现给用户。用户可以通过鼠标点击来打开或者关闭某个outline项来实现交互，当打开一个outline时，用户可以看到它的所有子节点，关闭一个outline的时候，这个outline的所有子节点会自动隐藏。并且，在点击的时候，阅读器会自动跳转到outline对应的页面位置。Outlines包含以下几个字段：

 |字段	|类型	|值|
|--------|--------|--------|
Type	|name	|(可选)如果这个字段有值，则必须是Outlines。
First	|dictionary	|(必须;必须是间接对象) 第一个顶层Outline item。
Last	|dictionary	|(必须;必须是间接对象)最后一个顶层outline item。
Count	|integer	|(必须)outline的所有层次的item的总数。

Outline是一个管理outline item的顶层对象，我们看到的，其实是outline item，这个里面才包含了文字、行为、目标区域等等。一个outline item主要有一下几个字段：

 |字段	|类型	|值|
|--------|--------|--------|
Title	|text string|	(必须)当前item要显示的标题。
Parent	|dictionary	|(必须;必须是间接对象) outline层级中，当前item的父对象。如果item本身是顶级item，则父对象就是它本身。
Prev|	dictionary|	(除了每层的第一个item外，其他item必须有这个字段;必须是间接对象)当前层级中，此item的前一个item。
Next	|dictionary|	(除了每层的最后一个item外，其他item必须有这个字段;必须是间接对象)当前层级中，此item的后一个item。
First	|dictionary|	(如果当前item有任何子节点，则这个字段是必须的;必须是间接对象) 当前item的第一个直接子节点。
Last	|dictionary|	(如果当前item有任何子节点，则这个字段是必须的;必须是间接对象) 当前item的最后一个直接子节点。
Dest	|name,byte string, or array	|(可选; 如果A字段存在，则这个不能被会略)当前的outline item被激活的时候，要显示的区域。
A	|dictionary|	(可选; 如果Dest 字段存在，则这个不能被忽略)当前的outline item被激活的时候，要执行的动作。

* URI字段
URI（uniform resource identifier)，定义了文档级别的统一资源标识符和相关链接信息。目录和文档中的链接就是通过这个字段来处理的.
* Metadata字段
文档的一些附带信息，用xml表示，符合adobe的xmp规范。这个可以方便程序不用解析整个文件就能获得文件的大致信息。
* 其他
Catalog字典中，常用的字段一般有以下一些：

 |字段	|类型	|值|
|--------|--------|--------|
Type	|name|	(必须)必须为Catalog。
Version|	name	|(可选)PDF文件所遵循的版本号（如果比文件头指定的版本号高的话）。如果这个字段缺省或者文件头指定的版本比这里的高，那就以文件头为准。一个PDF生成程序可以通过更新这个字段的值来修改PDF文件版本号。
Pages	|dictionary|	(必须并且必须为间接对象)当前文档的页面集合入口。
PageLabels	|number tree|	(可选) number tree，定义了页面和页面label对应关系。
Names|	dictionary|	(可选)文档的name字典。
Dests	|dictionary	|(可选;必须是间接对象)name和相应目标对应关系字典。
ViewerPreferences|	dictionary|	(可选)阅读参数配置字典，定义了文档被打开时候的行为。如果缺省，则使用阅读器自己的配置。
PageLayout	|name	|(可选) 指定文档被打开的时候页面的布局方式。SinglePageDisplay 单页OneColumnDisplay 单列TwoColumnLeftDisplay 双列，奇数页在左TwoColumnRightDisplay 双列，奇数页在右TwoPageLeft 双页，奇数页在左TwoPageRight 双页，奇数页在右缺省值: SinglePage.
PageMode	|name|	(可选) 当文档被打开时，指定文档怎么显示UseNone  目录和缩略图都不显示UseOutlines 显示目录UseThumbs  显示缩略图FullScreen  全屏模式，没有菜单，任何其他窗口UseOC     显示Optional content group 面板UseAttachments显示附件面板缺省值: UseNone.
Outlines	|dictionary|	(可选；必须为间接对象)文档的目录字典
Threads	|array	|(可选；必须为间接对象)文章线索字典组成的数组。
OpenAction	|array or dictionary|	(可选) 指定一个区域或一个action，在文档打开的时候显示（区域）或者执行（action）。如果缺省，则会用默认缩放率显示第一页的顶部。
AA	|dictionary|	(可选)一个附加的动作字典，在全局范围内定义了响应各种事件的action。
URI|	dictionary	|(可选)一个URI字典包含了文档级别的URI action信息。
AcroForm|	dictionary	|(可选)文档的交互式form (AcroForm)字典。
Metadata	|stream	|(可选;必须是间接对象)文档包含的元数据流。


