> Typora是一款开源写作神器，如果还不会使用Typora，强烈建议花你最宝贵的30分钟去学习一下，就30钟就可以让你学会Typora。

> 相信我往下看完，你就会有不一样的收获

## Typora介绍

Typora是一款免费的轻量级Markdown编辑器，它没有[Mou](http://25.io/mou/)，[Haroopad](http://pad.haroopress.com/)等Markdown编辑器那么大名鼎鼎，但算是较为小众的一款产品。

Typora作为一款离线Markdown无疑是非常棒的， 如果作为笔记工具的话，推荐你使用 cmd Markdown，因人而异。

下载地址：[**Typora**](https://typora.io/)

@[TOC](目录（Contens）)

## 标题

#一阶标题 （快捷键Ctrl+1）

##二阶标题 （快捷键Ctrl+2）

###三阶标题 （快捷键Ctrl+3）

####四阶标题 （快捷键Ctrl+4）

#####五阶标题 （快捷键Ctrl+5）

######六阶标题 （快捷键Ctrl+6）

## 如何生成目录

````
在文章开始地方输入[toc]，即可在对应位置插入目录
@[TOC]目录

以下不用写，直接写@[TOC](目录)即可自动获到目录中
#一阶标题 （快捷键Ctrl+1）
##二阶标题 （快捷键Ctrl+2）
###三阶标题 （快捷键Ctrl+3）
####四阶标题 （快捷键Ctrl+4）
#####五阶标题 （快捷键Ctrl+5）
######六阶标题 （快捷键Ctrl+6）
注：凡是文章标题带有#（1-n个）的都会被捕获到目录中。
````



<center\>这是要居中的文本内容\</center\>

**注：**Typora目前并不会直接预览居中效果——相应的效果只有输出文本的时候才会显现。

## 字体、字号、颜色设置

```reStructuredText
<font face="微软雅黑" >微软雅黑字体</font>
<font face="黑体" >黑体</font>
<font size=3 >3号字</font>
<font size=4 >4号字</font>
<font color=#FF0000 >红色</font>
<font color=#008000 >绿色</font>
<font color=#0000FF >蓝色</font>
```

## 背景色设置

目前csdn博客写作中只能是借助 table, tr, td 等表格标签的 bgcolor 属性来实现背景色的功能。

```reStructuredText
<table><tr><td bgcolor=orange> 背景色是 1 orange</td></tr></table>
<table><tr><td bgcolor= BlueViolet > 背景色2 BlueViolet </td></tr></table>
```



## 下划线

下划线使用格式
<u>下划线的内容<\u> 或者快捷键Ctrl+U

下划线在Typora显示形式是
<u>这就是我亲测的下划线</u>



## 删除线

删除线使用格式：~~
~~删除线的内容~~



## 字体加粗

前面某个字段使用两个*，**加粗字体** 或者快捷键Ctrl+B



## 字体倾斜

使用一个”星“，*字体倾斜了* 或者快捷键Ctrl+I



## 图片的插入

直接拖你想要图片进来即可



## 超链接

* 使用快捷键Ctrl+K

* 使用2个反斜杠"[]()"，

  ````
  [百度][https://www.baidu.com/]
  ````

[百度一下](https://www.baidu.com/)

**注：**按住Ctrl键+点击上面链接就可以直接访问该链接



## 代码区域

4个（`）+编程语言即可

````java
//设置线程名字
thread.setName("线程1"); 
thread1.setName("线程2");
````



## 表格的使用

第一种：快捷键**Ctrl+T**

第二种：|ID|name|age|回车即可

|   学号   |     姓名      | 年龄 |
| :------: | :-----------: | :--: |
| 20200506 | MarkerHunJava |  35  |



## 任务列表

\- [ ] 文字 （**注：**注意用空格隔开）

- [x] Java
- [x] 大数据
- [ ] 人工智能
- [ ] 机器学习



## 有序无序列表

**创建无序列** :\+ 、- 、* （后面加空格）

**多行无序列表**:

* Java

  * 容器

    * HashMap

      

**有序列表**:<u>(1.)</u>空格

1. Java
2. Biodata

**多行有序列表：**

```undefined
1. Java
2. Biodata
    1. Java
    2. Biodata
```



## 水平分割线

````
***或者- - -
````



## 引用的使用格式

````
>+空格
````



## 表情

````
:单词
````



## 数学表达式

Typora支持加入用LaTeX写成的数学公式，并且在软件界面下用MathJax直接渲染，数学公式分为两种参考[**Mathpix Snip**](https://mathpix.com/)

* 行内公式 `$ ... $`

* 行间公式 `$$ ... $$`,（或者$$+回车）

  **注：**行间公式形式是将数学式插在文本行之间（多行公式、公式组和微积分方程等复杂的数学式都是采用行间）

  **注：**行内公式形式是将数学式插入文本行之内（适合编写简 短的数学式）

* 如：将公式插入到本行内，符号：`$公式内容$`，$xyz$或“$$”+回车即可

  

#### 1、上标、下标、求和、括号、分式、根号

**语法**：行内公式输入在两个$$之间，行外公公式**$$公式内容$$（$$+回车）**即可输入。

|      |         Typora语法         |            显示效果            |
| :--: | :------------------------: | :----------------------------: |
| 上标 |       x^2、y^x、e^3        |        $x^2、y^x、e^3$         |
| 下标 |       x_0、a_1、T_1        |      $x_0$、$a_1$、$T_1$       |
| 括号 | [a+b]、\lbrace x-y \rbrace | $[a+b]$、$\lbrace x-y \rbrace$ |
| 求和 |            \sum            |             $\sum$             |
| 根号 |     \sqrt{2}、\sqrt{3}     |     $\sqrt{2}$、$\sqrt{3}$     |

#### 2、基本运算符

|            |           Typora/markdown语法           |                   显示效果                    |
| :--------: | :-------------------------------------: | :-------------------------------------------: |
|    加减    |                   \mp                   |                     $\mp$                     |
|    减加    |                   \pm                   |                     $\pm$                     |
|     乘     |                 \times                  |                   $\times$                    |
|     除     |                  \div                   |                    $\div$                     |
|    求和    |                  \sum                   |                    $\sum$                     |
|    求积    |                  \prod                  |                    $\prod$                    |
|    微分    |                \partial                 |                  $\partial$                   |
|    积分    |         \int、\displaystylw\int         |                    $\int$                     |
| 不大于等于 |               x+y\ngeq z                |                 $x+y\ngeq z$                  |
|    a点b    |                a \cdot b                |                  $a \cdot b$                  |
|    a星b    |                 a\ast b                 |                   $a\ast b$                   |
|    分式    | \frac{1}{2}、\frac{b}{a}、\frac{x}{x+y} | $\frac{1}{2}$、$\frac{b}{a}$、$\frac{x}{x+y}$ |

#### 3、三角函数、指数、对数

|      | Typora/markdown语法 |  显示效果   |
| :--: | :-----------------: | :---------: |
| sin  |       \sin(x)       |  $\sin(x)$  |
| cos  |       \cos(x)       |  $\cos(x)$  |
| tan  |       \tan(x)       |  $\tan(x)$  |
| log  |      \log_2 10      | $\log_2 10$ |
|  ln  |        \ln2         |   $\ln2$    |

#### 4、高等数学相关运算符

|               |                     Typora/markdown语法                      |                           显示效果                           |
| :-----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     无穷      |                            \infty                            |                           $\infty$                           |
|     矢量      |                       \vec{a}、\vec{b}                       |                     $\vec{a}$、$\vec{b}$                     |
| 一阶/二阶导数 |                      \dot{x}、\ddot{x}                       |                    $\dot{x}$、$\ddot{x}$                     |
|  求和上下标   |     \sum_0^3 、\sum_0^{\infty} 、\sum_{-\infty}^{\infty}     |    $\sum_0^3 、\sum_0^{\infty} 、\sum_{-\infty}^{\infty}$    |
|  算数平均值   |                           \bar{a}                            |                          $\bar{a}$                           |
|   概率分布    |                           \hat{a}                            |                          $\hat{a}$                           |
|  平均数运算   |                        \overline{xyz}                        |                       $\overline{xyz}$                       |
| 开二次方运算  |                           \sqrt x                            |                          $\sqrt 8$                           |
|     开方      |                   \sqrt[开方数]{被开方数}                    |                       $\sqrt[3]{x+y}$                        |
|     极限      |             \lim{a+b}、lim{n\rightarrow+\infty}              |            $\lim{a+b}、lim{n\rightarrow+\infty}$             |
|   微分运算    | \frac{\partial x}{\partial y}、\frac{\partial^2x}{\partial y^2} | $\frac{\partial x}{\partial y}$、$\frac{\partial^2x}{\partial y^2}$ |
|     求和      |          \sum^{x \to \infty}_{y \to 0}{\frac{x}{y}}          |         $\sum^{x \to \infty}_{y \to 0}{\frac{x}{y}}$         |
|     矩阵      | \begin{pmatrix}<br/>a & b & c \\<br/>d & e & f \\<br/>g & h & i <br/>\end{pmatrix} | $\begin{pmatrix}<br/>a & b & c \\<br/>d & e & f \\<br/>g & h & i <br/>\end{pmatrix}$ |

#### 5、集合运算符

|                |              Typora/markdown语法               |                        显示效果                        |
| :------------: | :--------------------------------------------: | :----------------------------------------------------: |
|  属于/不属于   |                  \in、\notin                   |                    $\in$、$\notin$                     |
|  子集/真子集   | x \subset y、x \supset y、\subseteq、\supseteq | $x \subset y$、$x \supset y$、$\subseteq$、$\supseteq$ |
| 并集/交集/差集 |             \cup、\cap、\setminus              |              $\cup$、$\cap$、$\setminus$               |
| 同或/同与/异惑 |         \bigodot、\bigodot、\bigoplus          |          $\bigodot$、$\bigodot$、$\bigoplus$           |

#### 对数

| Typora/markdown语法 | 效果 |
| :-----------------: | :--: |
|        \ln2         |      |
|       \log_28       |      |
|        \lg10        |      |



#### 6、希腊字母

| 大写字母（显示效果） | Typora/markdown语法 | 小写字母   | Typora/markdown语法 |
| -------------------- | ------------------- | ---------- | ------------------- |
| $A$                  | A                   | $\alpha$   | \alpha              |
| $B$                  | B                   | $\beta$    | \beta               |
| $\Gamma$             | \Gamma              | $\gamma$   | \gamma              |
| $\Delta$             | \Delta              | $\delta$   | \delta              |
| $E$                  | E                   | $\epsilon$ | \epsilon            |
| $Z$                  | Z                   | $\zeta$    | \zeta               |
| $H$                  | H                   | $\eta$     | \eta                |
| $\Theta$             | \Theta              | $\theta$   | \theta              |
| $\Lambda$            | \Lambda             | $\lambda$  | \lambda             |
| $\mu$                | \mu                 | $\xi$      | \xi                 |
| $\Xi$                | \Xi                 | $\omicron$ | \omicron            |
| $\Pi$                | \Pi                 | $\pi$      | \pi                 |
| $\rho$               | \rho                | $\Sigma$   | \Sigma              |
| $\sigma$             | \sigma              | $\Upsilon$ | \Upsilon            |
| $T$                  | T                   | $\tau$     | \tau                |
| $\Phi$               | \Phi                | $\phi$     | \phi                |
| $X$                  | X                   | $\chi$     | \chi                |
| $\Psi$               | \Psi                | $\psi$     | \psi                |
| $\Omega$             | \Omega              | $\omega$   | \omega              |

## 甘特图

````java
​```mermaid
	gantt
	        dateFormat  YYYY-MM-DD
	        title Adding GANTT diagram functionality to mermaid
	        section 现有任务
	        已完成               :done,    des1, 2014-01-06,2014-01-08
	        进行中               :active,  des2, 2014-01-09, 3d
	        计划一               :         des3, after des2, 5d
	        计划二               :         des4, after des3, 5d
```
````

​```mermaid
	gantt
	        dateFormat  YYYY-MM-DD
	        title 架构之路
	        section 现有任务
	        已完成               :done,    des1, 2014-01-06,2014-01-08
	        进行中               :active,  des2, 2014-01-09, 3d
	        计划一               :         des3, after des2, 5d
	        计划二               :         des4, after des3, 5d
```
## Mermaid流程图

````
​```mermaid
	graph LR
	graph LR
	A[老鹰] -- 吃 --> B((小鸡))
	A -- 吃 --> C(蛇)
	B -- 吃--> D{虫}
	C --> D
```
````

​```mermaid
graph LR
	A[老鹰] -- 吃 --> B((小鸡))
	A -- 吃 --> C(蛇)
	B -- 吃--> D{虫}
	C --> D
```

[更多参考文档](https://mermaidjs.github.io/#/flowchart?id=graph)

## Flowchart流程图

[更多参考](http://flowchart.js.org/)

# markdown编辑器语法-颜色名列表

| **颜色名** | **十六进制颜色值** | **颜色**           |
| ---------- | ------------------ | ------------------ |
| AliceBlue  | #F0F8FF            | rgb(240, 248, 255) |
|            |                    |                    |
|            |                    |                    |
|            |                    |                    |
|            |                    |                    |
|            |                    |                    |
|            |                    |                    |
|            |                    |                    |
|            |                    |                    |





> 写作不易，如果帮到你记得来个三连【点赞+关注+分享】。

> 如有错误，还请各位赶紧说句话，非常感谢！

