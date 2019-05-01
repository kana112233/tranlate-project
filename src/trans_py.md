＃如何使用提供的工具来训练Tesseract 4.00


**对培训过程有疑问？**如果您在培训期间遇到一些问题

培训过程中你需要帮助，使用

[的tesseract-OCR(https://groups.google.com/forum/#!forum/tesseract-ocr)

邮件列表，询问您的问题。 
**请不要**报告您的问题和

提出有关培训的问题

[问题](https://github.com/tesseract-ocr/tesseract/issues)！


* [简介](TrainingTesseract-4.00.md＃introduction)
   
* [开始之前](TrainingTesseract-4.00.md＃在你开始之前)
   
* [需要额外的库](TrainingTesseract-4.00.md＃additional-libraries-required)
   
* [构建培训工具](TrainingTesseract-4.00.md＃building-the-training-tools)
   
* [硬件 - 软件要求](TrainingTesseract-4.00.md＃hardware-software-requirements)
   
* [培训文本要求](TrainingTesseract-4.00.md＃training-text-requirements)
   
* [培训过程概述](TrainingTesseract-4.00.md＃overview-of-training-process)
   
* [了解培训期间使用的各种文件](TrainingTesseract-4.00.md＃understanding-the-various-files-used-training-training)
   
* [LSTMTraining命令行](TrainingTesseract-4.00.md #lstmtraining-command-line)
      
* [Unicharset Compression-recoding](TrainingTesseract-4.00.md #unicharset-compression-recoding)
      
* [随机训练数据和顺序训练](TrainingTesseract-4.00.md #sendomized-training-data-and-sequential_training)
      
* [型号输出](TrainingTesseract-4.00.md #model-output)
      
* [网络模式和优化](TrainingTesseract-4.00.md #net-mode-and-optimization)
      
* [完美样本延迟](TrainingTesseract-4.00.md＃perfect-sample-delay)
      
* [调试间隔和可视调试](TrainingTesseract-4.00.md＃debug-interval-and-visual-debugging)
   
* [TessTutorial](TrainingTesseract-4.00.md #stsstutorial)
      
* [TessTutorial的一次性设置](TrainingTesseract-4.00.md＃one-time-setup-for-tesstutorial)
      
* [创建培训数据](TrainingTesseract-4.00.md #creating-training-data)
         
* [制作盒子文件](TrainingTesseract-4.00.md＃制作盒子文件)
         
* [使用tesstrain.sh](TrainingTesseract-4.00.md＃using-tesstrainsh)
      
* [lstmtraining教程指南](TrainingTesseract-4.00.md＃tutorial-guide-to-lstmtraining)
         
* [创建Starter Traineddata](TrainingTesseract-4.00.md＃creating-starter-traineddata)
         
* [从零开始训练](TrainingTesseract-4.00.md＃training-from-scratch)
         
* [影响微调](TrainingTesseract-4.00.md＃fine-tuning-for-impact)
         
* [微调几个字符](TrainingTesseract-4.00.md＃微调几个字符)
         
* [训练几层](TrainingTesseract-4.00.md #training-just-a-few-layers)
      
* [来自培训的错误消息](TrainingTesseract-4.00.md #error-messages-from-training)
   
* [组合输出文件](TrainingTesseract-4.00.md＃combined-the-output-files)
   
* [幻觉效果](TrainingTesseract-4.00.md＃the-hallucination-effect)


＃ 介绍


Tesseract 4.00包括一个新的基于神经网络的识别引擎

提供比以前更高的准确度(在文档图像上)

版本，以换取所需计算能力的显着增加。
上

然而，复杂的语言实际上可能比基础Tesseract快*。


神经网络需要更多的训练数据和训练*很多*

比基础Tesseract慢。
对于基于拉丁语的语言，现有的模型数据

所提供的已经接受过约400,000条文本线的培训

字体](https://github.com/tesseract-ocr/tesseract/issues/654#issuecomment-274574951)。

对于其他脚本，没有那么多字体可用，但它们仍然存在

训练过类似数量的文本。
而不是花几分钟来

几个小时的训练，Tesseract 4.00需要几个*天*到几个

*周。*即使有了所有这些新的培训数据，您也可能觉得它不适合

你的特殊问题，因此你在这里想重新训练它。


培训有多种选择：


*   微调。
从现有的训练有素的语言开始，训练你的
    
具体的附加数据。
这可能适用于接近的问题
    
现有的训练数据，但有些微妙的方式不同，如a
    
特别不寻常的字体
甚至可以进行少量培训
    
数据。

*从网络中切掉顶层(或一些任意数量的层)
    
并使用新数据重新训练新的顶层。
如果微调不起作用，
    
这很可能是下一个最佳选择。
切掉顶层可以
    
如果你开始，仍然可以训练一种全新的语言或脚本
    
与最相似的外观脚本。

*从头开始。
这是一项艰巨的任务，除非你有一个非常好的任务
    
针对您的问题的代表性和足够大的培训。
如果不，
    
你可能最终得到一个非常好的过度拟合的网络
    
关于训练数据，但不是关于实际数据。


虽然上述选项可能听起来不同，但实际上是训练步骤

几乎完全相同，除了命令行，所以它相对容易尝试

它是所有方式，给定时间或硬件并行运行它们。


至少4.00，旧的识别引擎仍然存在，也可以

经过培训，但已弃用，除非有充分理由保留，否则可以

将来的版本中删除。


＃ 在你开始之前


你不需要神经网络的任何背景来训练Tesseract 4.00，但它

可能有助于理解培训选项之间的差异。
请

在钻研之前阅读[实施介绍](NeuralNetsInTesseract4.00)

深入培训过程，以及与培训相同的注意事项

Tesseract 3.04适用：


**重要说明**：在您花费时间和精力培训Tesseract之前，它

强烈建议您阅读[ImproveQuality](ImproveQuality)页面。


＃需要额外的库


从3.03开始，需要额外的库来构建培训

工具。


```
sudo apt-get install libicu-dev
sudo apt-get install libpango1.0-dev
sudo apt-get install libcairo2-dev
```

＃构建培训工具


从3.03开始，如果您要从源代码编译Tesseract，则需要进行编译

并使用单独的make命令安装培训工具。
一旦上述

已安装其他库，从Tesseract运行以下命令

源目录：


```
make
make training
sudo make training-install
```

构建[ScrollView.jar](ViewerDebugging)也很有用，但不是必需的：


```
make ScrollView.jar
export SCROLLVIEW_PATH=$PWD/java
```

＃硬件 - 软件要求


在撰写本文时，培训仅适用于Linux。 
(macOS几乎可以工作;它需要

对shell脚本进行轻微攻击以解释旧版本的“bash”吧

在`mktemp`中提供和区别。)Windows是未知的，但需要msys或Cygwin。


至于运行Tesseract 4.0.0，它是有用的，但不是必须拥有一个多核(4是好的)

机器，OpenMP和Intel Intrinsics支持SSE / AVX扩展。

基本上它仍会在具有足够内存的任何东西上运行，但是高端运行

你的处理器越快。
不需要GPU。 
(没有支持。)记忆

use可以通过--max_image_MB命令行选项控制，但你是

可能需要至少1GB的内存，超过您的操作系统所占用的内存。


＃培训文本要求


对于基于拉丁语的语言，提供的现有模型数据已经过培训

约[400000个字体跨越约4500

字体](https://github.com/tesseract-ocr/tesseract/issues/654#issuecomment-274574951)。

对于其他脚本，没有那么多字体可用，但它们仍然存在

训练过类似数量的文本。


请注意，拥有更多培训文本并制作更多页面是有益的

但是，由于神经网络不能概括，需要对某些东西进行训练

类似于他们将要运行的。
如果目标域严重

有限的，然后所有关于需要大量训练数据的可怕警告可能不会

适用，但可能需要更改网络规范。


＃培训流程概述


整个培训过程类似于[培训3.04](TrainingTesseract)。


概念上相同：


1. [准备培训文本。](https://github.com/tesseract-ocr/tesseract/issues/654#issuecomment-274574951)

1.将文本渲染到图像+框文件。 
(或者为现有的创建手工制作的盒子文件
    
图像数据。)

1.制作unicharset文件。 
(可以部分指定，即手动创建)。

1. [从unicharset和可选字典制作一个入门训练数据
     
数据。](＃创建起动-traineddata)

1.运行tesseract处理图像+框文件以进行训练数据集。

1.运行培训数据集培训。

1.合并数据文件。


关键的区别是：


*这些框只需要在* textline级别。*因此*更容易*
    
从现有图像数据制作训练数据。

* .tr文件由.lstmf数据文件替换。

*字体*可以而且应该自由混合*而不是分开。

*更换聚类步骤(mtraraining，cntraining，shapeclustering)
    
只需一个缓慢的训练步骤。


培训不能像3.04培训那样自动化

原因：


*慢速训练步骤不适合从脚本中间运行
    
如果停止可以重新启动，并且很难自动判断它是什么时候
    
完了。

*如何训练网络有多种选择(见上文)。

*允许语言模型和unicharset不同
    
由基地Tesseract使用，但不一定是。

*没有必要使用与语言相同的基础Tesseract
    
神经网络Tesseract。


＃了解培训期间使用的各种文件


与基础Tesseract一样，完整的LSTM模型以及它所需的一切都是

收集在`traineddata`文件中。
与基础Tesseract不同，它是一个“首发”

trainingdata`文件在培训期间提供，必须提前设置。
它

可以包含：


*配置文件提供控制参数。

* ** Unicharset **定义字符集。

* ** Unicharcompress，**又名编码器，它将unicharset进一步映射到
    
神经网络识别器实际使用的代码。

*标点符号模式dawg，允许在单词周围使用标点符号。

* Word dawg。
系统单词列表语言模型。

*数字dawg，允许的数字模式。


必须提供大胆的元素**。
其他是可选的，但如果有任何瑕疵

如果提供，还必须提供标点符号。
一个新工具：

提供`combine_lang_model`来从a创建`starter traineddata`

`unicharset`和可选单词列表。


在培训期间，培训师会编写检查点文件，这是一个标准

神经网络训练员的行为。
这样可以停止训练

如果需要，稍后再继续。
任何检查点都可以转换为完整

`traineddata`用于使用`--stop_training`命令行标志进行识别。


培训师还会定期将检查点文件写入新的bests

在训练中。


可以修改网络并重新训练其中的一部分或微调

通过告诉，获取特定的训练数据(即使是修改过的unicharset！)

训练员将`--continue_from`作为现有的检查点文件，或者来自裸体

已从现有`traineddata`文件中提取的LSTM模型文件

使用`combine_tessdata` _提供它尚未转换为整数._


如果在`--traineddata`标志中更改了unicharset，则与之相比

通过`--continue_from`提供的模型中使用的，然后是

`--old_traineddata`标志必须提供相应的`traineddata`

保存`unicharset`和`rewder的文件。这使训练师能够

计算字符集之间的映射。


训练数据通过`.lstmf`文件提供，这些文件是序列化的

`DocumentData`它们包含一个图像和相应的UTF8文本

转录，并可以使用Tesseract在tif / box文件对中生成

类似于为旧引擎创建`.tr`文件的方式。


#LSTMTraining命令行


训练程序是一种用于训练神经网络的多功能工具。

下表描述了其命令行选项：


| 
**旗帜** | 
**输入** | 
**默认** | 
**说明** |

| 
：--------------------- | 
：------：| 
：---------：| 
：----------------- |

| 
`traineddata` | 
`string` |
没有|
入门训练数据文件的路径，包含单字符，重新编码器和可选语言模型。 
|

| 
`net_spec` | 
`string` |
没有|
指定网络的拓扑。 
|

| 
`model_output` | 
`string` |
没有|
输出模型文件/检查点的基本路径。 
|

| 
`max_image_MB` | 
`int` | 
`6000` |
用于缓存图像的最大内存量。 
| 
`learning_rate` | 
`double` | 
`10e-4` | 
SGD算法的初始学习率。 
|

| 
`sequential_training` | 
`bool` | 
`false` |
设置为true以进行顺序培训。
默认是以循环方式处理所有训练数据。 
|

| 
`net_mode` | 
`int` | 
`192` |
来自`NetworkFlags`in`network.h`的标志。
可能的值：'128`用于Adam优化而不是动量; 
`64`允许不同的层有自己的学习率，自动发现。 
|

| 
`perfect_sample_delay` | 
`int` | 
`0` |
当网络变得良好时，自从允许最后一个完美的样本以来，在看到许多不完美的样本之后，只能提供完美的样本。 
|

| 
`debug_interval` | 
`int` | 
`0` |
如果非零，则每隔多次迭代显示可视调试。 
|

| 
`weight_range` | 
`double` | 
`0.1` |
用于初始化权重的随机值范围。 
|

| 
`动量`| 
`double` | 
`0.5` | 
α平滑渐变的动量。 
|

| 
`adam_beta` | 
`double` | 
`0.999` | 
ADAM算法中的平滑因子平方梯度。 
|

| 
`max_iterations` | 
`int` | 
`0` |
经过多次迭代后停止训练。 
|

| 
`target_error_rate` | 
`double` | 
`0.01` |
如果平均百分比错误率低于此值，请停止培训。 
|

| 
`continue_from` | 
`string` |
没有|
上一个检查点的路径，可以从中继续训练或微调。 
|

| 
`stop_training` | 
`bool` | 
`false` |
将`--continue_from`中的训练检查点转换为识别模型。 
|

| 
`convert_to_int` | 
`bool` | 
`false` |
使用`stop_training`，转换为8位整数以获得更高的速度，精度稍差。 
|

| 
`append_index` | 
`int` | 
`-1` |
在给定的索引处切断网络的头部并附加`--net_spec`网络代替切断部分。 
|

| 
`train_listfile` | 
`string` |
没有|
列出培训数据文件的文件的文件名。 
|

| 
`eval_listfile` | 
`string` |
没有|
列出评估数据文件的文件的文件名，该评估数据文件用于独立于训练数据评估模型。 
|


大多数标志都使用默认值，只有几个标志需要

下面列出的特定操作，但首先是一些详细的评论

复杂的旗帜：


## Unicharset Compression-recoding


LSTM很擅长学习序列，但是当数量很多时，它会减慢很多*

国家太大了。
有实证结果表明它更好

要求LSTM学习一个长序列而不是很多类的短序列，所以

对于复杂的脚本(Han，Hangul和印度语脚本)，最好是

将每个符号重新编码为来自少数类的短序列代码

比拥有大量的课程。 
`combine_lang_model`命令有这个

默认情况下打开功能。
它将每个汉字编码为可变长度

1-5个代码的序列，Hangul使用Jamo编码作为3个代码的序列，

和其他脚本作为其unicode组件的序列。
对于脚本

使用_virama_字符生成合取辅音，(所有印度语

脚本加缅甸和高棉语)函数`NormalizeCleanAndSegmentUTF8`

将virama与适当的邻居配对以生成更加面向字形的

在unicharset编码。
为了充分利用这种改进，

应为`combine_lang_model`设置`--pass_through_recoder`标志

脚本。


##随机训练数据和顺序训练


为了使随机梯度下降能够正常工作，可以使用训练数据

在所有样本文件中随机拖曳，以便教练可以阅读它

顺便通过每个文件，当它到达时返回到第一个文件

结束。
这完全违背了Tesseract基础训练的方式！


如果使用渲染代码(通过`tesstrain.sh`)，那么它将随机播放

在每个文件中示出文本行，但每个文件都会得到一组文件

包含单一字体的训练样本。
要添加更均匀的混合，

默认是依次处理每个文件中的一个样本，即'循环'样式。

如果您以其他方式生成了训练数据，或者它们都是相同的

风格(例如手写的手稿)然后你可以使用

`--sesttial_training`标志为`lstmtraining。这是更高效的内存

因为它一次只从两个文件加载数据，然后处理它们

序列。 
(第二个文件是预读的，因此在需要时就可以使用了。)


##模型输出


培训师使用`--model_output`作为基本名称定期保存检查点。

因此，可以在任何时候停止训练，并使用重新启动它

相同的命令行，它将继续。
要强制重新启动，请使用其他方法

`--model_output`或删除所有文件。


##网络模式和优化


“128”标志启用了Adam优化，这似乎比它更好

平淡的气势。


`64`标志启用自动特定于图层的学习速率。
进步时

摊位，培训师调查哪一层应该有他们的学习率

独立减少，并可能降低一个或多个学习率继续

学习。


`192`的`net_mode`的默认值启用Adam和特定于层的

学习率。


##完美的样本延迟


对“简单”样品进行培训不一定是个好主意，因为它是浪费

时间，但网络不应该被允许忘记如何处理它们，所以它

如果它们过于频繁地出现，可以丢弃一些简单的样品。
该

`--perfect_sample_delay`参数丢弃完美样本(如果没有)

自上次完美样本以来看到的许多不完美的。
目前的默认值

零值使用所有样本。
在实践中，价值似乎没有

巨大的影响，如果允许训练运行足够长的时间，零就会产生

最好的结果。


## Debug Interval和Visual Debugging


使用零(默认)` -  debug_interval`，培训师输出进度报告

每100次迭代，类似于以下示例。


```
At iteration 61239/65000/65015, Mean rms=1.62%, delta=8.587%, char train=16.786%, word train=36.633%, skip ratio=0.1%,  wrote checkpoint.

在迭代61332/65100/65115，平均rms = 1.601％，delta = 8.347％，char train = 16.497％，单词串= 36.24％，跳跃比= 0.1％，写检查点。


2％改进时间= 44606，最佳误差为17.77 @ 16817

警告：LSTMTrainer反序列化LSTMRecognizer！

在迭代61423/65200/65215，平均rms = 1.559％，delta = 7.841％，char train = 15.7％，单词串= 35.68％，跳跃比= 0.1％，新的最佳char错误= 15.7At迭代45481，阶段0， 
Eval Char错误率= 6.9447893，Word错误率= 27.039255写的最佳型号：./ SANLAYER / LAYER15.7_61423.checkpoint写了检查点。


```

使用`--debug_interval -1`，培训师为每个输出详细的调试文本

训练迭代。
文本调试信息包括真实文本，识别文本，

迭代次数，训练样本id(lstmf文件和行)和平均值

几个错误指标。
在所有情况下都会显示该行的“GROUND TRUTH”。

`ALIGNED TRUTH`和`BEST OCR TEXT`仅在与...不同时显示

“地面真相”。


```
Iteration 455038: GROUND  TRUTH : उप॑ त्वाग्ने दि॒वेदि॑वे॒ दोषा॑वस्तर्धि॒या व॒यम् ।
File /tmp/san-2019-03-28.jsY/san.Mangal.exp0.lstmf line 451 (Perfect):
Mean rms=1.267%, delta=4.155%, train=11.308%(32.421%), skip ratio=0%
Iteration 455039: GROUND  TRUTH : मे अपराध और बैठे दुकानों नाम सकते अधिवक्ता, दोबारा साधन विषैले लगाने पर प्रयोगकर्ताओं भागे
File /tmp/san-2019-04-04.H4m/san.FreeSerif.exp0.lstmf line 28 (Perfect):
Mean rms=1.267%, delta=4.153%, train=11.3%(32.396%), skip ratio=0%
```

```
Iteration 1526: GROUND  TRUTH : 𒃻 𒀸 𒆳𒆳 𒅘𒊏𒀀𒋾
Iteration 1526: ALIGNED TRUTH : 𒃻 𒀸 𒆳𒆳 𒅘𒊏𒊏𒀀𒋾
Iteration 1526: BEST OCR TEXT :    𒀀𒋾
File /tmp/eng-2019-04-06.Ieb/eng.CuneiformComposite.exp0.lstmf line 19587 :
Mean rms=0.941%, delta=12.319%, train=56.134%(99.965%), skip ratio=0.6%
Iteration 1527: GROUND  TRUTH : 𒀭𒌋𒐊
Iteration 1527: BEST OCR TEXT : 𒀭𒌋
File /tmp/eng-2019-04-06.Ieb/eng.CuneiformOB.exp0.lstmf line 7771 :
Mean rms=0.941%, delta=12.329%, train=56.116%(99.965%), skip ratio=0.6%
```

使用`--debug_interval> 0`，培训师会显示几个调试窗口

有关网络层的信息。
在特殊情况下

`--debug_interval 1`它等待在'LSTMForward`窗口中点击之前

继续下一次迭代，但对于所有其他迭代，它只是继续并绘制

请求频率的信息。


**请注意，要使用--debug_interval> 0，您必须构建

[ScrollView.jar](ViewerDebugging)以及其他培训工具。**见

[构建培训工具](#building-the-training-tools)


可视化调试信息包括：


每个网络层的前向和后向窗口。
大多数只是随机的

噪音，但`输出/输出返回`和`ConvNL`窗口值得一看。

`Output`显示最终Softmax的输出，该输出以黄色开始

null字符的行，并在每个点逐渐形成黄色标记

它认为有一个角色。 
(x轴是图像x坐标，和

y轴是字符类。)`Output-back`窗口显示差异

实际输出和目标之间使用相同的布局，但是带黄色

因为“给我更多这个”而蓝色则是“少给我这个”。
作为网络

学习，`ConvNL`窗口开发出典型的边缘检测器结果

期待从底层。


`LSTMForward`显示训练图像上整个网络的输出。

`LSTMTraining`显示训练图像上的训练目标。
在两者中，绿色

绘制线条以显示每个角色和角色的峰值输出

本身被划线右侧。


值得关注的另外两个窗口是“CTC输出”和“CTC目标”。

这些显示网络的当前输出和目标作为线图

输出强度对图像x坐标。
而不是热像，像

`Output`窗口，为每个字符类绘制一条不同颜色的线条

y轴是输出强度。


#TessTutorial


[创建训练数据](＃creating-training-data)的过程是

记录如下，然后是[教程指南

lstmtraining](＃tutorial-guide-to-lstmtraining)，介绍了

主要的培训过程，使用已经过实际测试的命令行。
上

至少Linux，您应该能够将命令行复制粘贴到

你的终端。


为了使`tesstrain.sh`脚本有效，有必要

要么设置`PATH`来包含你的本地`training`和`api`目录，要么使用

`make install`。


## TessTutorial的一次性安装


为了成功运行TessTutorial，您需要有一个工作

安装tesseract和培训工具，并有培训脚本和

在某些目录中需要训练有素的数据文件。

这些说明仅涵盖从字体渲染的情况，

所以必须先安装[所需字体](字体)。

请注意，您的字体位置可能有所不同


```
sudo apt update
sudo apt install ttf-mscorefonts-installer
sudo apt install fonts-dejavu
fc-cache -vf
```

请按照以下说明操作

为TessTutorial做第一次设置。


```
mkdir ~/tesstutorial
cd ~/tesstutorial

mkdir langdata

cd langdata

wget https://raw.githubusercontent.com/tesseract-ocr/langdata_lstm/master/radical-stroke.txt

wget https://raw.githubusercontent.com/tesseract-ocr/langdata_lstm/master/common.punc

wget https://raw.githubusercontent.com/tesseract-ocr/langdata_lstm/master/font_properties

wget https://raw.githubusercontent.com/tesseract-ocr/langdata_lstm/master/Latin.unicharset

wget https://raw.githubusercontent.com/tesseract-ocr/langdata_lstm/master/Latin.xheights

mkdir eng

cd eng

wget https://raw.githubusercontent.com/tesseract-ocr/langdata/master/eng/eng.training_text

wget https://raw.githubusercontent.com/tesseract-ocr/langdata/master/eng/eng.punc

wget https://raw.githubusercontent.com/tesseract-ocr/langdata/master/eng/eng.numbers

wget https://raw.githubusercontent.com/tesseract-ocr/langdata/master/eng/eng.wordlist


cd~ / tesstutorial

git clone --depth 1 https://github.com/tesseract-ocr/tesseract.git

cd tesseract / tessdata

mkdir最好

cd最好

wget https://github.com/tesseract-ocr/tessdata_best/raw/master/eng.traineddata

wget https://github.com/tesseract-ocr/tessdata_best/raw/master/heb.traineddata

wget https://github.com/tesseract-ocr/tessdata_best/raw/master/chi_sim.traineddata


```

##创建培训数据


与基础Tesseract一样，可以在渲染合成训练之间进行选择

来自字体的数据，或标记一些预先存在的图像(如古代手稿)

例如)。
  

在任何一种情况下，所需的格式仍然是tiff / box文件

对，除了盒子只需要覆盖文本行而不是个人

字符。


###制作盒子文件


Tesseract 4接受多种格式的盒子文件进行LSTM培训，

虽然它们与Tesseract 3使用的不同

([细节](https://github.com/tesseract-ocr/tesseract/issues/2357))。


框文件中的每一行都匹配tiff图像中的“字符”(字形)。


`<symbol> <left> <bottom> <right> <top> <page>`

其中`<left> <bottom> <right> <top> <page>`可以是边界框坐标

单个字形或整个文本行([参见示例](https://github.com/tesseract-ocr/tesseract/issues/2357#issuecomment-477239316))。


要标记文本行尾，必须在一系列行后插入一个特殊行。


`<tab> <left> <bottom> <right> <top> <page>`


请注意，在所有情况下，即使是从右到左的语言，例如阿拉伯语，

行的文本转录，*应从左到右排序。*换句话说，网络

无论语言如何，都将从左到右学习

从右到左/比迪的处理发生在Tesseract内部的更高层次。




###使用tesstrain.sh


运行tesstrain.sh的设置是

与基础Tesseract相同。
使用`--linedata_only`选项进行LSTM培训。

请注意，接受更多培训是有益的

文本和制作更多页面，因为神经网络也没有概括和

需要训练类似于他们将要运行的东西。
如果

目标域严重受限，然后所有关于需要的可怕警告

许多培训数据可能不适用，但可能需要网络规范

改变。


使用[tesstrain.sh](https://github.com/tesseract-ocr/tesseract/blob/master/src/training/tesstrain.sh)创建训练数据

如下：


```
src/training/tesstrain.sh --fonts_dir /usr/share/fonts --lang eng --linedata_only \
  --noextract_font_properties --langdata_dir ../langdata \
  --tessdata_dir ./tessdata --output_dir ~/tesstutorial/engtrain
```

上述命令使LSTM训练数据等同于用于训练的数据

基础Tesseract英语。
为了制作通用的基于LSTM的OCR引擎，

这是非常不足够的，但是做了一个很好的教程演示。


现在尝试这个来制作'Impact'字体的eval数据：


```
src/training/tesstrain.sh --fonts_dir /usr/share/fonts --lang eng --linedata_only \
  --noextract_font_properties --langdata_dir ../langdata \
  --tessdata_dir ./tessdata \
  --fontlist "Impact Condensed" --output_dir ~/tesstutorial/engeval
```
We will use that data later to demonstrate tuning.

## lstmtraining教程指南


###创建Starter Traineddata


注意：这是一个新的步骤！


而不是`unicharset`和`script_dir，``lstmtraining`现在需要一个

`trainingdata`文件在其命令行上，以获取它需要的所有信息

关于要学习的语言。 
`traineddata` *必须*至少包含一个

`lstm-unicharset`和`lstm-recoder`组件，也可能包含三个

dawg文件：`lstm-punc-dawg lstm-word-dawg lstm-number-dawg``config`文件是

也是可选的。
其他组件(如果存在)将被忽略和未使用。


没有工具可以直接创建`lstm-recoder`。
相反，有一个新的

工具，`combine_lang_model`，它输入`input_unicharset`和

`script_dir`(`script_dir`指向`langdata`目录)和`lang`(`lang`是

正在使用的语言)和可选的单词列表文件。
它创造了`lstm-recoder`

来自`input_unicharset`并创建所有dawgs，如果提供了单词列表，

把所有东西放在一个`训练的数据'文件中。




###从头开始训练


以下示例显示了从头开始进行培训的命令行。
试试吧

使用上面的命令行创建的默认训练数据。


```
mkdir -p ~/tesstutorial/engoutput
training/lstmtraining --debug_interval 100 \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --net_spec '[1,36,0,1 Ct3,3,16 Mp3,3 Lfys48 Lfx96 Lrx96 Lfx256 O1c111]' \
  --model_output ~/tesstutorial/engoutput/base --learning_rate 20e-4 \
  --train_listfile ~/tesstutorial/engtrain/eng.training_files.txt \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt \
  --max_iterations 5000 &>~/tesstutorial/engoutput/basetrain.log
```

在单独的窗口中监视日志文件：


```
tail -f ~/tesstutorial/engoutput/basetrain.log
```

(如果您之前尝试过本教程，您可能会注意到这些数字有

改变。
这是一个略小的网络的结果，并添加了

ADAM优化器，可实现更高的学习速度。)


您应该观察到600次迭代，空格(白色)开始

显示在“CTC输出”窗口上，并通过1300次迭代显示绿线

`LSTMForward`窗口，图像中有空格。


经过1300次迭代，“CTC输出”中出现明显的非空间颠簸

请注意，从所有相同高度开始的`CTC Targets'现在都是变化的

由于空间的确定输出和一些和暂定的高度

其他字符的输出。
同时，人物和定位

`LSTMTraining`窗口中的绿线不如它们准确

最初，因为网络的部分输出混淆了CTC

算法。 
(CTC假设不同的统计独立性

x坐标，但它们显然不是独立的。)


通过2000次迭代，在“输出”窗口中应该清楚有些微弱

黄色标记似乎表明有一些增长的产量

非空和非空格，字符开始出现在

`LSTMForward`窗口。


在3700次迭代之后，字符错误率降至50％以下，并且降低到5000

大约13％，它将终止。 
(约20分钟的电流

带AVX的高端机器。)


请注意，此引擎使用与使用的相同数量的训练数据进行训练

传统的Tesseract引擎，但其在其他字体上的准确性可能非常高

较差的。
对'Impact'字体运行独立测试：


```
training/lstmeval --model ~/tesstutorial/engoutput/base_checkpoint \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt
```

85％的字符错误率？
不太好！


现在，基础Tesseract在“影响”方面表现不佳，但它包括在内

4500左右的字体用于训练新的LSTM版本，所以如果你可以运行那个

比较：


```
training/lstmeval --model tessdata/best/eng.traineddata \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt
```

2.45％的字符错误率？
好多了！


在下一节中供参考，也可以在上面运行完整模型的测试

我们一直在使用的训练集：


```
training/lstmeval --model tessdata/best/eng.traineddata \
  --eval_listfile ~/tesstutorial/engtrain/eng.training_files.txt
```

字符错误率= 0.25047642，字错误率= 0.63389585


(如果你之前运行过，并注意错误率远高于

之前的alpha版本，这是由于形状报价的使用发生了变化。

它之前没有计算引号形状中的错误，但现在确实如此。)


你可以训练另外5000次迭代，并获得错误率

训练设置低很多，但它对“Impact”字体没有多大帮助：


```
mkdir -p ~/tesstutorial/engoutput
training/lstmtraining \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --net_spec '[1,36,0,1 Ct3,3,16 Mp3,3 Lfys48 Lfx96 Lrx96 Lfx256 O1c111]' \
  --model_output ~/tesstutorial/engoutput/base --learning_rate 20e-4 \
  --train_listfile ~/tesstutorial/engtrain/eng.training_files.txt \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt \
  --max_iterations 10000 &>>~/tesstutorial/engoutput/basetrain.log
```

“影响”现在的字符错误率> 100％，即使错误率也是如此

训练集已下降到2.68％字符/ 10.01％字：


```
training/lstmeval --model ~/tesstutorial/engoutput/base_checkpoint \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt
```

这表明该模型完全过度适应所提供的训练

组！
它很好地说明了训练集时会发生什么

不包括目标数据中的所需变化。


总之，从头开始的培训需要一个非常有限的问题，很多

训练数据，或者你需要通过减少一些来缩小网络

上面`--net_spec`中图层的大小。
或者，你可以尝试

调整...


###微调影响力


微调是在没有新数据的情况下训练现有模型的过程

改变网络的任何部分，虽然你**现在可以**添加

字符集中的字符。 
(参见[微调几下]
        
字符](＃微调换 - α-几个字符))。


```
training/lstmtraining --model_output /path/to/output [--max_image_MB 6000] \
  --continue_from /path/to/existing/model \
  --traineddata /path/to/original/traineddata \
  [--perfect_sample_delay 0] [--debug_interval 0] \
  [--max_iterations 0] [--target_error_rate 0.01] \
  --train_listfile /path/to/list/of/filenames.txt
```

**注意**`--continue_from`arg可以指向训练检查点

**或**识别模型，*即使文件格式不同。*

训练检查点是以`--model_output`开头的文件

在`checkpoint`。
可以从现有的提取中识别模型

使用`combine_tessdata训练的数据文件。注意它也是必要的

提供原始的训练数据文件，因为它包含unicharset

和重新编码。
让我们首先微调我们之前构建的模型，看看是否

我们可以使它适用于'影响'：


```
mkdir -p ~/tesstutorial/impact_from_small
training/lstmtraining --model_output ~/tesstutorial/impact_from_small/impact \
  --continue_from ~/tesstutorial/engoutput/base_checkpoint \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --train_listfile ~/tesstutorial/engeval/eng.training_files.txt \
  --max_iterations 1200
```

在100次迭代后，它的字符/单词错误为22.36％/ 50.0％并且下降

在1200处达到0.3％/ 1.2％。现在是一个独立的测试：


```
training/lstmeval --model ~/tesstutorial/impact_from_small/impact_checkpoint \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt
```

由于培训师的平均值，这显示了0.0086％/ 0.057％的更好结果

超过1000次迭代，并且一直在改进。
这不是代表

因为我们正在测试训练数据，所以“Impact”字体的结果！


这是一个玩具的例子。
微调的想法实际上是应用它

到一个训练有素的现有模型：


```
mkdir -p ~/tesstutorial/impact_from_full
training/combine_tessdata -e tessdata/best/eng.traineddata \
  ~/tesstutorial/impact_from_full/eng.lstm
training/lstmtraining --model_output ~/tesstutorial/impact_from_full/impact \
  --continue_from ~/tesstutorial/impact_from_full/eng.lstm \
  --traineddata tessdata/best/eng.traineddata \
  --train_listfile ~/tesstutorial/engeval/eng.training_files.txt \
  --max_iterations 400
```

在100次迭代之后，它具有1.35％/ 4.56％的字符/字错误并且下降到

在400时，0.533％/ 1.633％。再次，独立测试给出了更好的结果：


```
training/lstmeval --model ~/tesstutorial/impact_from_full/impact_checkpoint \
  --traineddata tessdata/best/eng.traineddata \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt
```

字符错误0.017％，单词0.120％然而，更有趣的是效果

其他字体，所以在我们一直使用的基础训练集上运行测试：


```
training/lstmeval --model ~/tesstutorial/impact_from_full/impact_checkpoint \
  --traineddata tessdata/best/eng.traineddata \
  --eval_listfile ~/tesstutorial/engtrain/eng.training_files.txt
```

字符错误率= 0.25548592，字错误率= 0.82523491


它只是稍微差一点，尽管已经达到接近零误差

训练集，仅在400次迭代中实现。 
**进一步注意

超过400次迭代的训练会使基础上的错误更高。**


总之，预训练模型可以微调或适应小数据

设置，**对其一般准确性没有太大的伤害。**它仍然非常

然而，重要的是避免过度拟合。


###微调几个字符


**新功能**可以在字符集中添加一些新字符

并通过微调训练他们，没有大量的训练数据。


培训需要一个新的unicharset /重新编码器，可选的语言模型，和

旧的训练数据文件包含旧的unicharset /重新编码器。


```
training/lstmtraining --model_output /path/to/output [--max_image_MB 6000] \
  --continue_from /path/to/existing/model \
  --traineddata /path/to/traineddata/with/new/unicharset \
  --old_traineddata /path/to/existing/traineddata \
  [--perfect_sample_delay 0] [--debug_interval 0] \
  [--max_iterations 0] [--target_error_rate 0.01] \
  --train_listfile /path/to/list/of/filenames.txt
```

让我们尝试将加号 - 减号(±)添加到现有的英语模型中。
修改

`langdata / eng / eng.training_text`包含一些±的样本。
我插了14个

他们，如下图所示：


```
grep ± ../langdata/eng/eng.training_text
alkoxy of LEAVES ±1.84% by Buying curved RESISTANCE MARKED Your (Vol. SPANIEL
TRAVELED ±85¢ , reliable Events THOUSANDS TRADITIONS. ANTI-US Bedroom Leadership
Inc. with DESIGNS self; ball changed. MANHATTAN Harvey's ±1.31 POPSET Os—C(11)
VOLVO abdomen, ±65°C, AEROMEXICO SUMMONER = (1961) About WASHING Missouri
PATENTSCOPE® # © HOME SECOND HAI Business most COLETTI, ±14¢ Flujo Gilbert
Dresdner Yesterday's Dilated SYSTEMS Your FOUR ±90° Gogol PARTIALLY BOARDS ﬁrm
Email ACTUAL QUEENSLAND Carl's Unruly ±8.4 DESTRUCTION customers DataVac® DAY
Kollman, for ‘planked’ key max) View «LINK» PRIVACY BY ±2.96% Ask! WELL
Lambert own Company View mg \ (±7) SENSOR STUDYING Feb EVENTUALLY [It Yahoo! Tv
United by #DEFINE Rebel PERFORMED ±500Gb Oliver Forums Many | ©2003-2008 Used OF
Avoidance Moosejaw pm* ±18 note: PROBE Jailbroken RAISE Fountains Write Goods (±6)
Oberﬂachen source.” CULTURED CUTTING Home 06-13-2008, § ±44.01189673355 €
netting Bookmark of WE MORE) STRENGTH IDENTICAL ±2? activity PROPERTY MAINTAINED
```

现在生成新的培训和评估数据：


```
src/training/tesstrain.sh --fonts_dir /usr/share/fonts --lang eng --linedata_only \
  --noextract_font_properties --langdata_dir ../langdata \
  --tessdata_dir ./tessdata --output_dir ~/tesstutorial/trainplusminus
src/training/tesstrain.sh --fonts_dir /usr/share/fonts --lang eng --linedata_only \
  --noextract_font_properties --langdata_dir ../langdata \
  --tessdata_dir ./tessdata \
  --fontlist "Impact Condensed" --output_dir ~/tesstutorial/evalplusminus
```

对新的训练数据进行微调。
这需要更多的迭代

只有一些新目标角色的样本可以继续：


```
training/combine_tessdata -e tessdata/best/eng.traineddata \
  ~/tesstutorial/trainplusminus/eng.lstm
training/lstmtraining --model_output ~/tesstutorial/trainplusminus/plusminus \
  --continue_from ~/tesstutorial/trainplusminus/eng.lstm \
  --traineddata ~/tesstutorial/trainplusminus/eng/eng.traineddata \
  --old_traineddata tessdata/best/eng.traineddata \
  --train_listfile ~/tesstutorial/trainplusminus/eng.training_files.txt \
  --max_iterations 3600
```

在100次迭代之后，它具有1.26％/ 3.98％的字符/字错误并且下降到

在3600时为0.041％/ 0.185％。再次，独立测试给出了更好的结果：


```
training/lstmeval --model ~/tesstutorial/trainplusminus/plusminus_checkpoint \
  --traineddata ~/tesstutorial/trainplusminus/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/trainplusminus/eng.training_files.txt
```

字符错误0.0326％，字0.128％。
更有趣的是，是否

新的字符可以在'Impact'字体中识别，因此运行测试

影响评估集：


```
training/lstmeval --model ~/tesstutorial/trainplusminus/plusminus_checkpoint \
  --traineddata ~/tesstutorial/trainplusminus/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/evalplusminus/eng.training_files.txt
```

字符错误率= 2.3767074，字错误率= 8.3829474


这与原始模型的原始测试相比非常好

影响数据集。
此外，如果您检查错误：


```
training/lstmeval --model ~/tesstutorial/trainplusminus/plusminus_checkpoint \
  --traineddata ~/tesstutorial/trainplusminus/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/evalplusminus/eng.training_files.txt 2>&1 |
  grep ±
```

......你应该看到所有的±符号都是正确的！ 
(每条真相都是如此

包含±也在相应的OCR线上包含±，并且没有

在grep输出中没有匹配的OCR行的真值行。)


这是个好消息！
这意味着可以添加一个或多个新字符

在不影响现有准确性的情况下，**和**能够识别新的准确性

角色至少会在某种程度上推广到其他字体！


注意：微调时，重要的是要试验数量

迭代，因为对小数据集的过度训练将导致

过拟合。 
ADAM非常适合查找必要的功能组合

得到那个罕见的类是正确的，但它确实似乎过于简单

优化。


###培训几个层次


如果您只想添加新的字体样式或需要几个字体样式，则可以进行微调

新角色，但如果你想为克林贡火车怎么办？
你不太可能

有很多训练数据，它不同于其他任何东西，所以你做什么？
您

可以尝试删除现有网络模型的一些顶层，替换

其中一些具有新的随机层，并训练您的数据。
该

命令行与[训练来自

刮擦](＃training-from-scratch)，但另外你必须提供一个模型

`--continue_from`和`--append_index`。


`--append_index`参数告诉它删除**层上方的所有层**

使用给定的索引(从零开始，在最外面的系列中)然后

将给定的`--net_spec`参数附加到剩下的内容中。
虽然这个索引

系统不是引用网络层的完美方式，这是一个结果

大大简化的网络规范语言。
建设者会

输出与其生成的网络相对应的字符串

合理地容易检查索引是否涉及预期的层。


4.00 alpha的一个新功能是combine_tessdata可以列出a的内容

traineddata文件及其版本字符串。
在大多数情况下，版本字符串

包括用于训练的net_spec：


```
training/combine_tessdata -d tessdata/best/heb.traineddata
Version string:4.00.00alpha:heb:synth20170629:[1,36,0,1Ct3,3,16Mp3,3Lfys48Lfx96Lrx96Lfx192O1c1]
17:lstm:size=3022651, offset=192
18:lstm-punc-dawg:size=3022651, offset=3022843
19:lstm-word-dawg:size=673826, offset=3024221
20:lstm-number-dawg:size=625, offset=3698047
21:lstm-unicharset:size=1673826, offset=3703368
22:lstm-recoder:size=4023, offset=3703368
23:version:size=80, offset=3703993
```

并为chi_sim：


```
training/combine_tessdata -d tessdata/best/chi_sim.traineddata
Version string:4.00.00alpha:chi_sim:synth20170629:[1,48,0,1Ct3,3,16Mp3,3Lfys64Lfx96Lrx96Lfx512O1c1]
0:config:size=1966, offset=192
17:lstm:size=12152851, offset=2158
18:lstm-punc-dawg:size=282, offset=12155009
19:lstm-word-dawg:size=590634, offset=12155291
20:lstm-number-dawg:size=82, offset=12745925
21:lstm-unicharset:size=258834, offset=12746007
22:lstm-recoder:size=72494, offset=13004841
23:version:size=84, offset=13077335
```

请注意，层数相同，但只有大小不同。
因此

在这些模型中，`--append_index`的以下值将保留

关联的最后一层，并附加在上面：


**指数** | 
**层**

：-------：| 
：-----------

`0` |
输入

`1` | 
`Ct3,3,16`

`2` | 
`Mp3,3`

`3` | 
`Lfys48 / 64`

`4` | 
`Lfx96`

`5` | 
`Lrx96`

`6` | 
`Lfx192 / 512`


现有模型剩余部分的权重最初保持不变，

但允许通过新的培训数据进行修改。


举个例子，让我们尝试将现有的chi_sim模型转换为eng。
我们会

切断了最后一个LSTM层(对于chi_sim而言，它比以前更大

训练eng模型)和softmax，取代较小的LSTM层和a

新的softmax：


```
mkdir -p ~/tesstutorial/eng_from_chi
training/combine_tessdata -e tessdata/best/chi_sim.traineddata \
  ~/tesstutorial/eng_from_chi/eng.lstm
training/lstmtraining --debug_interval 100 \
  --continue_from ~/tesstutorial/eng_from_chi/eng.lstm \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --append_index 5 --net_spec '[Lfx256 O1c111]' \
  --model_output ~/tesstutorial/eng_from_chi/base \
  --train_listfile ~/tesstutorial/engtrain/eng.training_files.txt \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt \
  --max_iterations 3000 &>~/tesstutorial/eng_from_chi/basetrain.log
```

由于较低层已经训练过，因此学习速度要快一些

从头开始训练。
在600次迭代时，它突然开始产生输出

到了800，它已经让大多数字符正确。
到它停止的时候

在3000次迭代时，它应该是6.00％字符/ 22.42％字。


尝试完整训练集的常规测试：


```
training/lstmeval --model ~/tesstutorial/eng_from_chi/base_checkpoint \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/engtrain/eng.training_files.txt
```

和`Impact`字体的独立测试：


```
training/lstmeval --model ~/tesstutorial/eng_from_chi/base_checkpoint \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --eval_listfile ~/tesstutorial/engeval/eng.training_files.txt
```

在完整的训练集上，我们得到5.557％/ 20.43％和“影响”36.67％/ 83.23％，

这比从刮刮训练要好得多，但仍然很糟糕

过度拟合。


总之，可以切断现有网络的顶层

火车，好像从头开始，但仍有相当多的训练数据

要求避免过度配合。


##来自培训的错误消息


一些人在运行培训时可能会出现各种错误消息

其中可能很重要，而其他则不是很重要：


`编码字符串失败！`结果是训练图像的文本字符串

无法使用给定的unicharset进行编码。
可能的原因是：


文字中有一个没有代表性的字符，比如英镑符号
    
那不是你的unicharset。

1.文本中的一个迷路的不可打印字符(如制表符或控制字符)。

1.案文中没有代表性的印度语字素/阿克萨拉语。


无论如何，它将导致训练者忽略训练图像。

如果错误不常见，则无害，但可能表明您的错误

unicharset不足以代表您正在训练的语言。


`Unichar xxx太长，不能编码！`(很可能只是印度语)。
有一个

可以在重新编码器中使用的unicode字符长度的上限，

这简化了LSTM引擎的unicharset。
它会继续下去

把Aksara从可识别的集合中删除，但如果有很多，那么你

有麻烦了。


`boxfile字符串中的坏框坐标！`LSTM培训师只需要边界

完整文本行的框信息，而不是字符级别，但是如果

你在框字符串中放置空格，如下所示：


```
<text for line including spaces> <left> <bottom> <right> <top> <page>
```

解析器会混淆并给你错误信息。


当训练输入不是LSTM格式时，会发生“反序列化标题失败”

或者文件不可读。
检查您的文件列表文件以查看它是否包含

有效的文件名。


`没有块重叠的文本行：`当布局分析无法正确时出现

分割作为训练数据给出的图像。
文本行被删除。
不

如果没有很多问题，但如果有很多问题，可能会有很多问题

训练文本或渲染过程有问题。


`<Undecodable>`可以在早期的ALIGNED_TRUTH或OCR TEXT输出中出现

在培训中。
这是unicharset压缩和CTC训练的结果。

(参见上面的Unicharset Compression和train_mode)。
这应该是无害的

可以安全地忽略。
随着训练的进行，它的频率应该下降。


＃组合输出文件


lstmtraining程序输出两种检查点文件：


*`<model_base> _checkpoint`是最新的模型文件。

*`<model_base> <char_error> _ <iteration> .checkpoint`定期写为
    
具有最佳训练误差的模型。
这是一个训练转储就像
    
检查点，但较小，因为它没有要使用的备份模型
    
如果训练发生分歧。


这些文件中的任何一个都可以转换为标准的训练数据文件

如下：


```
training/lstmtraining --stop_training \
  --continue_from ~/tesstutorial/eng_from_chi/base_checkpoint \
  --traineddata ~/tesstutorial/engtrain/eng/eng.traineddata \
  --model_output ~/tesstutorial/eng_from_chi/eng.traineddata
```

这将从训练转储中提取识别模型，并将其插入

进入--traineddata论证，以及unicharset，重新编码和任何

在训练期间提供的dawgs。


**注意** Tesseract 4.00现在可以使用训练有素的数据文件快乐地运行

包含* just *`lang.lstm`，`lang.lstm-unicharset`和`lang.lstm-recoder`。
该

`lstm  -  *  -  dawgs`是可选的，*不需要其他组件或

与OEM_LSTM_ONLY一起用作OCR引擎模式。*没有bigrams，unichar ambigs或

如果存在，则需要任何其他组件或甚至具有任何效果。
该

只做其他事情的组件是`lang.config`，它可以影响

布局分析和子语言。


如果添加到现有的Tesseract traineddata文件中，则为“lstm-unicharset”

不必匹配Tesseract`unicharset`，但同样的unicharset必须

用于训练LSTM并构建`lstm  -  *  -  dawgs`文件。


＃幻觉效应


如果您发现您的模型行为不端，例如：

*在某些单词的开头添加“Capital”字母而不是“Small”字母。

*添加“空间”不应该这样做。

*等......


[然后阅读幻觉话题。](https://github.com/tesseract-ocr/tesseract/wiki/The-Hallucination-Effect)


