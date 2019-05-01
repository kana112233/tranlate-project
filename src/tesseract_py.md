TESSERACT(1)TESSERACT(1)




名称
       
tesseract  - 命令行OCR引擎


概要
       
tesseract FILE OUTPUTBASE [OPTIONS] ... [CONFIGFILE] ...


描述
       
tesseract(1)是一种商业质量的OCR发动机，最初于1985年至1995年间在惠普开发。1995年，该发动机是
       
在UNLV评估的前三名中。
它于2005年由惠普和UNLV开源，自那时起就在谷歌开发。


输入/输出参数
       
文件
           
输入文件的名称。
这可以是图像文件或文本文件。


支持大多数图像文件格式(Leptonica可读的任何格式)。


文本文件列出所有输入图像的名称(每行一个图像名称)。
结果将合并到一个文件中
           
对于每种输出文件格式(txt，pdf，hocr，xml)。


如果FILE是stdin或 - 则使用标准输入。


OUTPUTBASE
           
输出文件的基本名称(将附加相应的扩展名)。
默认情况下，输出为a
           
将.txt添加到基本名称的文本文件，除非有一个或多个参数设置明确指定
           
期望的输出。


如果OUTPUTBASE是stdout或 - 则使用标准输出。


OPTIONS
       
-c CONFIGVAR = VALUE
           
将参数CONFIGVAR的值设置为VALUE。
允许多个-c参数。


--dpi N.
           
在DPI中为输入图像指定分辨率N. 
N的典型值是300.如果没有此选项，则为
           
从图像中包含的元数据中读取分辨率。
如果图像不包含该信息，请使用Tesseract
           
试图猜测它。


-l LANG，-l SCRIPT
           
要使用的语言或脚本。
如果未指定，则假定为eng(英语)。
可以指定多种语言，
           
用加号字符分隔。 
Tesseract使用3个字符的ISO 639-2语言代码(请参阅语言和脚本)。


--psm N.
           
将Tesseract设置为仅运行布局分析的子集并假设某种形式的图像。 
N的选项是：


0 =仅限方向和脚本检测(OSD)。
               
1 =使用OSD自动分页。
               
2 =自动页面分割，但没有OSD或OCR。 
(未实现)
               
3 =全自动页面分割，但没有OSD。 
(默认)
               
4 =假设一列可变大小的文本。
               
5 =假设一个垂直对齐文本的统一块。
               
6 =假设一个统一的文本块。
               
7 =将图像视为单个文本行。
               
8 =将图像视为单个单词。
               
9 =将图像视为圆形中的单个单词。
               
10 =将图像视为单个字符。
               
11 =稀疏文本。
找到尽可能多的文本，没有特定的顺序。
               
12 =带OSD的稀疏文本。
               
13 =原始线。
将图像视为单个文本行，
                    
绕过特定于Tesseract的黑客攻击。


--oem N.
           
指定OCR引擎模式。 
N的选项是：


0 =仅原始Tesseract。
               
1 =神经网络仅限LSTM。
               
2 = Tesseract + LSTM。
               
3 =默认，基于可用的内容。


--tessdata-dir PATH
           
指定tessdata路径的位置。


--user-patterns文件
           
指定用户模式文件的位置。


 - 用户词文件
           
指定用户词文件的位置。


CONFIGFILE
           
要使用的配置的名称。
该名称可以是tessdata / configs或tessdata / tessconfigs中的文件，也可以是绝对或
           
相对文件路径。
配置是纯文本文件，包含参数列表及其值，每行一个，
           
使用空格分隔参数值。


有趣的配置文件包括：


o alto  - 以ALTO格式输出(OUTPUTBASE.xml)。


o hocr  - 以hOCR格式输出(OUTPUTBASE.hocr)。


o pdf  - 输出PDF(OUTPUTBASE.pdf)。


o tsv  - 输出TSV(OUTPUTBASE.tsv)。


o txt  - 输出纯文本(OUTPUTBASE.txt)。


get.images  - 将处理后的输入图像写入文件(tessinput.tif)。


o logfile  - 将调试消息重定向到文件(tesseract.log)。


o lstm.train  -  LSTM培训使用的输出文件(OUTPUTBASE.lstmf)。


o makebox  - 写入框文件(OUTPUTBASE.box)。


o quiet  - 将调试消息重定向到/ dev / null。


可以选择几个配置文件，例如tesseract image.png demo alto hocr pdf txt将创建四个
       
输出文件demo.alto，demo.hocr，demo.pdf和demo.txt以及OCR结果。


Nota bene：选项-l LANG，-l SCRIPT和--psm N必须在任何CONFIGFILE之前出现。


单一选项
       
-h， -  help
           
显示帮助信息。


--help-EXTRA
           
为高级用户显示额外帮助。


--help-PSM
           
显示页面分割模式。


--help-OEM
           
显示OCR引擎模式。


-v， -  version
           
返回tesseract(1)可执行文件的当前版本。


--list-LAN​​GS
           
列出tesseract引擎的可用语言。
可以与--tessdata-dirPATH一起使用。


--print参数
           
打印tesseract参数。


语言和文字
       
要使用Tesseract识别某些文本，通常需要指定文本的语言或脚本(除非
       
它是使用-l LANG或-l SCRIPT的默认支持的英文文本。


自动选择语言还会选择特定于语言的字符集和字典(单词列表)。


选择脚本通常会选择该脚本的所有字符，这些字符可以来自不同的语言。
词典
       
其中包含来自不同语言的混合。
在大多数情况下，脚本也支持英语。
就是这样
       
可以通过使用训练有素的数据来识别未经过专门培训的语言
       
写的。


使用+可以指定多种语言或脚本。
示例：tesseract myimage.png myimage -l eng + deu + fra。


https://github.com/tesseract-ocr/tessdata_fast提供快速语言和脚本模型，它们也是Linux的一部分
       
分布。


对于Tesseract 4，tessdata_fast包含以下语言的训练数据文件：


afr(南非荷兰语)，amh(阿姆哈拉语)，ara(阿拉伯语)，asm(阿萨姆语)，aze(阿塞拜疆语)，aze_cyrl(阿塞拜疆语 -  Cyrilic)，bel
       
(白俄罗斯语)，本(孟加拉语)，bod(藏语)，bos(波斯尼亚语)，bre(布列塔尼语)，bul(保加利亚语)，cat(加泰罗尼亚语;巴伦西亚语)，ceb
       
(Cebuano)，ces(捷克语)，chi_sim(简体中文)，chi_tra(繁体中文)，chr(切诺基)，cym(威尔士语)，dan
       
(丹麦语)，deu(德语)，dzo(Dzongkha)，ell(希腊语，现代，1453-)，英语(英语)，enm(英语，中级，1100-1500)，epo
       
(世界语)，equ(数学/方程检测模块)，est(爱沙尼亚语)，eus(巴斯克语)，fas(波斯语)，fin(芬兰语)，fra
       
(法语)，frk(法兰克语)，frm(法语，中间，约1400-1600)，gle(爱尔兰语)，glg(加利西亚语)，grc(希腊语，古代，至1453年)，
       
guj(古吉拉特语)，帽子(海地;海地克里奥尔语)，希伯来语(希伯来语)，欣(印地语)，hrv(克罗地亚语)，hun(匈牙利语)，iku(Inuktitut)，
       
ind(印度尼西亚语)，isl(冰岛语)，ita(意大利语)，ita_old(意大利语 - 旧语)，jav(爪哇语)，jpn(日语)，kan(Kannada)，
       
kat(格鲁吉亚)，kat_old(格鲁吉亚 - 旧)，kaz(哈萨克斯坦)，khm(中央高棉)，kir(吉尔吉斯;吉尔吉斯斯坦)，kmr(库尔德库尔曼吉)，
       
kor(韩国)，kor_vert(韩国垂直)，kur(库尔德语)，老挝语(老挝语)，lat(拉丁语)，lav(拉脱维亚语)，lit(立陶宛语)，ltz
       
(卢森堡语)，mal(马拉雅拉姆语)，mar(马拉地语)，mkd(马其顿语)，mlt(马耳他语)，蒙古语(蒙古语)，mri(毛利语)，msa(马来语)，
       
mya(缅甸语)，nep(尼泊尔语)，nld(荷兰语;佛兰芒语)，nor(挪威语)，oci(Occitan post 1500)，ori(Oriya)，osd(Orientation)
       
和脚本检测模块)，泛(Panjabi;旁遮普语)，pol(波兰语)，por(葡萄牙语)，pus(普什图语;普什图语)，que(克丘亚语)，
       
ron(罗马尼亚语;摩尔多瓦语;摩尔多瓦语)，rus(俄语)，san(梵文)，sin(僧伽罗语;僧伽罗语)，slk(斯洛伐克语)，slv(斯洛文尼亚语)，
       
snd(Sindhi)，spa(西班牙语; Castilian)，spa_old(西班牙语; Castilian  -  Old)，sqi(阿尔巴尼亚语)，srp(塞尔维亚语)，srp_latn(塞尔维亚语)
       
 - 拉丁语)，sun(Sundanese)，swa(斯瓦希里语)，swe(瑞典语)，syr(叙利亚语)，tam(泰米尔语)，tat(塔塔尔语)，tel(泰卢固语)，tgk(塔吉克语)，
       
tgl(他加禄语)，tha(泰国语)，tir(提格里尼亚)，ton(汤加)，tur(土耳其语)，uig(维吾尔语;维吾尔语)，ukr(乌克兰语)，urd(乌尔都语)，
       
uzb(乌兹别克语)，uzb_cyrl(乌兹别克语 -  Cyrilic)，vie(越南语)，yid(意第绪语)，yor(约鲁巴语)


要使用名为foo.traineddata的非标准语言包，请设置TESSDATA_PREFIX环境变量，以便文件可以
       
在TESSDATA_PREFIX / tessdata / foo.traineddata中找到并给Tesseract提供参数-l foo。


对于Tesseract 4，tessdata_fast包含以下脚本的训练数据文件：


阿拉伯语，亚美尼亚语，孟加拉语，加拿大语_原住民，切诺基，西里尔语，梵文，Ethiopic，Fraktur，格鲁吉亚语，希腊语，
       
古吉拉特语，Gurmukhi，HanS(简汉)，HanS_vert(Han简化，垂直)，HanT(Han传统)，HanT_vert(Han
       
传统的，垂直的)，Hangul，Hangul_vert(Hangul垂直)，希伯来语，日语，Japanese_vert(日语垂直)，Kannada，
       
高棉，老挝，拉丁语，马拉雅拉姆语，缅甸，奥里亚(奥迪亚)，僧伽罗语，叙利亚语，泰米尔语，泰卢固语，塔纳，泰语，西藏语，越南语。


可以从https://github.com/tesseract-ocr/tessdata_best获得相同的语言和脚本。 
tessdata_best提供
       
慢的语言和脚本模型。
培训需要这些模型。
他们也可以提供更好的OCR结果，但是
       
认可需要更多时间。


tessdata_fast和tessdata_best都只支持LSTM OCR引擎。


还有第三个存储库，https：//github.com/tesseract-ocr/tessdata，其模型支持Tesseract 3
       
传统的OCR引擎和Tesseract 4 LSTM OCR引擎。


配置文件和用户数据增加
       
Tesseract配置文件由具有参数 - 值对的行组成(空格分隔)。
参数记录为标志
       
在tesseractclass.h中的以下源代码中：


STRING_VAR_H(tessedit_char_blacklist，“”，“黑名单不能识别”);


这些参数可以启用或禁用引擎的各种功能，并且可以使其加载(或不加载)各种数据。
       
例如，假设您想要使用英语进行OCR，但是禁止使用普通字典并加载替代单词列表
       
以及另一种模式列表 - 这两个文件是最常用的额外数据文件。


如果您的语言包位于/path/to/eng.traineddata中，并且hocr配置位于/ path / to / configs / hocr中，则创建三个新语言包
       
文件：


/path/to/eng.user-words：


该
           
快
           
棕色
           
狐狸
           
跳下


/path/to/eng.user-patterns：


1- \ d \ d \ d-GOOG-411
           
WWW。\ n \\\ *。com


/路径/到/ CONFIGS /集市：


load_system_dawg F.
           
load_freq_dawg F.
           
user_words_suffix用户词
           
user_patterns_suffix用户模式


现在，如果您将单词bazaar作为CONFIGFILE传递给Tesseract，Tesseract将不会费心加载系统字典也不会
       
频繁单词的字典，将加载和使用您提供的eng.user-words和eng.user-patterns文件。
该
       
前者是一个简单的单词列表，每行一个。
后者的格式记录在read_pattern_list()的dict / trie.h中。


环境变量
       
TESSDATA_PREFIX
           
如果TESSDATA_PREFIX设置为路径，那么该路径用于查找带有语言和脚本的tessdata目录
           
识别模型和配置文件。
使用--tessdata-dir PATH是推荐的替代方法。


OMP_THREAD_LIMIT
           
如果tesseract可执行文件是使用多线程支持构建的，它通常会为OCR使用四个CPU内核
           
处理。
虽然对于单个图像来说这可能会更快，但如果主机提供的图像不足，则会导致性能下降
           
四个CPU核心或如果为许多图像制作OCR。 
OMP_THREAD_LIMIT = 1只使用一个CPU内核。


历史
       
该发动机于1985年在Hewlett Packard Laboratories Bristol和Hewlett Packard Co，Greeley Colorado开发
       
和1994年，1996年进行了一些更改，移植到Windows，以及1998年的一些C ++。很多代码都写了
       
在C中，然后更多的是用C ++编写的。 
C ++代码大量使用使用宏的列表系统。
这早于STL，
       
在STL之前是可移植的，并且比STL列表更有效，但是如果你得到分段则有很大的负面影响
       
违规，很难调试。


版本2.00带来了Unicode(UTF-8)支持，六种语言，并且能够训练Tesseract。


Tesseract被纳入UNLV的第四次OCR准确性年度测试。
看到
       
https://github.com/tesseract-ocr/docs/blob/master/AT-1995.pdf。
从Tesseract 2.00开始，现在包含脚本以允许
       
任何人都可以重现一些这些测试。
有关更多信息，请参阅https://github.com/tesseract-ocr/tesseract/wiki/TestingTesseract
       
细节。


Tesseract 3.00增加了许多新语言，包括中文，日文和韩文。
它还介绍了一个新的，
       
基于单文件的管理语言数据的系统。


Tesseract 3.02增加了双向文本支持，能够在单个图像中识别多种语言并进行了改进
       
布局分析。


Tesseract 4增加了一个新的基于神经网络(LSTM)的OCR引擎，专注于线路识别，但仍然支持
       
Tesseract 3的传统Tesseract OCR引擎，通过识别字符模式来工作。
与Tesseract 3的兼容性是
       
由--oem 0启用。这还需要支持传统引擎的训练数据文件，例如来自tessdata的引擎
       
存储库(https://github.com/tesseract-ocr/tessdata)。


有关更多详细信息，请参阅Tesseract wiki中的发行说明
       
(https://github.com/tesseract-ocr/tesseract/wiki/ReleaseNotes)。


资源
       
主要网站：https：//github.com/tesseract-ocr用户论坛：http：//groups.google.com/group/tesseract-ocr Wiki：
       
https://github.com/tesseract-ocr/tesseract/wiki有关培训的信息：
       
https://github.com/tesseract-ocr/tesseract/wiki/TrainingTesseract


也可以看看
       
ambiguous_words(1)，cntraining(1)，combine_tessdata(1)，dawg2wordlist(1)，shape_training(1)，mftraining(1)，
       
unicharambigs(5)，unicharset(5)，unicharset_extractor(1)，wordlist2dawg(1)


作者
       
由Ray Smith在Hewlett-Packard和Google领导Tesseract开发。
开发团队包括：


Ahmad Abdulkader，Chris Newton，Dan Johnson，Dar-Shyang Lee，David Eger，Eric Wiseblatt，Faisal Shafait，Hiroshi Takenaka，
       
Joe Liu，Joern Wanke，Mark Seaman，Mickey Namiki，Nicholas Beato，Oded Fuhrmann，Phil Cheatle，Pingping Xiu，Pong
       
Eksombatchai(Chantat)，Ranjith Unnikrishnan，Raquel Romano，Ray Smith，Rika Antonova，Robert Moss，Samuel Charron，Sheelagh
       
Lloyd，Shobhit Saxena和Thomas Kielbus。


有关贡献者的列表，请参阅https://github.com/tesseract-ocr/tesseract/blob/master/AUTHORS。


复印
       
根据Apache许可证2.0版获得许可




04/14/2019 TESSERACT(1)


