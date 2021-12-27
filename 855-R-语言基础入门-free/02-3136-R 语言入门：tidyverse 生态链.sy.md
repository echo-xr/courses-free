---
show: step
version: 1.0
enable_checker: false
---

# R 语言入门：tidyverse 生态链

## 实验介绍

#### 实验知识点

- tidyverse 生态链概述
- ggplot 与可视化
- dplyr 与数据转换

#### 实验环境

- R 3.4.1
- Rstudio

### tidyverse 生态链概述

技术的进化衍生出新的生产工具，而新的生产工具改变我们的工作方式和认知结构。蒸汽机，计算机，互联网等等发明对社会造成了深远的影响。人们总是认为这些伟大发明的来源是 Eureka 式从天而降的，但我们去深入挖掘一下就可以知道，任何发明都不是凭空而来的，而是在先前的理论与技术的基础上，发现新的需求或者新的原理，组合它们创造出来的。

今天我们实验的对象就是一组从原始 R 进化出来的工具链 Tidyverse，它是由 Hadley Wickham 主导开发的一系列 R 包的集合。Tidyverse 继承了 R 语言进行快速统计分析的优势，并实现了一些新的理念，例如 magrittr 包中的管道操作，让线性嵌套的函数组合变得更加清晰易懂。可视化方面中的 ggplot 使绘图变成搭积木式的图层叠加。

这样的小发明有的改变了分析的运作方式，有的改变了使用者的认知方式，聚在一起形成了一种新的数据分析的生态链。具体来看，Tidyverse 有如下核心组件:

- readr：读取数据
- tidyr：整理数据
- dplyr：数据转换
- ggplot：可视化
- purrr：函数式编程

这些组件与一些其他工具配合，形成了一套完整的数据分析流程，如下图所示：

![数据分析流程](http://labfile.oss.aliyuncs.com/courses/855/data-science-explore.png)

从图中可以看到，整个流程包括读取数据，整洁数据，数据探索和交流部分。经过前两部分，我们可以得到一个整理好的数据，它的每一行都是一个样本，每一列是一个变量。

然后我们就可以进入最核心的数据探索部分，数据探索包括数据转换，可视化，建模三部分。数据转换的内容包括构建新的变量，选出子集，对数据进行分组并获取统计量，进而可以通过可视化把变量或变量之间关系用图形表示出来。在对数据有大体上的认知后，可以尝试用精确的数学语言来对数据进行建模。模型的结果会给我们一些新的洞察和知识，驱动我们去提出新的问题，构成一个反馈循环。

数据探索完成后我们要把所做的工作借助文章清晰地表达出来，从而与其他人沟通。

下面我们就用一个简单的例子来看一看如何做数据探索。

### 分析汽车排放数据集

首先载入 tidyverse 包，并观察 mpg 数据的头部：

```r
library(tidyverse)
mpg
```

```txt
     ## # A tibble: 234 × 11
    ##    manufacturer      model displ  year   cyl      trans   drv   cty   hwy
    ##           <chr>      <chr> <dbl> <int> <int>      <chr> <chr> <int> <int>
    ## 1          audi         a4   1.8  1999     4   auto(l5)     f    18    29
    ## 2          audi         a4   1.8  1999     4 manual(m5)     f    21    29
    ## 3          audi         a4   2.0  2008     4 manual(m6)     f    20    31
    ## 4          audi         a4   2.0  2008     4   auto(av)     f    21    30
    ## 5          audi         a4   2.8  1999     6   auto(l5)     f    16    26
    ## 6          audi         a4   2.8  1999     6 manual(m5)     f    18    26
    ## 7          audi         a4   3.1  2008     6   auto(av)     f    18    27
    ## 8          audi a4 quattro   1.8  1999     4 manual(m5)     4    18    26
    ## 9          audi a4 quattro   1.8  1999     4   auto(l5)     4    16    25
    ## 10         audi a4 quattro   2.0  2008     4 manual(m6)     4    20    28
    ## # ... with 224 more rows, and 2 more variables: fl <chr>, class <chr>
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420689251.png/wm)

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420721083.png/wm)

mpg 数据集是刻画不同汽车的排放状况的一个数据集，总过有 234 个样本，11 个变量。这 11 个变量分别是：

- manufacture: 制造商
- model: 车型
- displ： 汽车排放量
- year: 制造年度
- cyl: 排气管数量
- trans: 排放类型
- drv: 驱动方式
- cty: 每公里耗油量（城市道路）
- hwy: 每公里耗油量（高速路）
- fl: 油的种类
- class: 车的类型

更多数据相关信息可以通过 `help(mpg)` 指令获取。

### 可视化：ggplot

先提出一个问题，汽车排放量和高速路上的每公里耗油量有什么关系？这两个变量都是数值变量可以先用散点图的形式将它们的关系展示出来：

```r
ggplot(data = mpg) + geom_point(mapping = aes(x = displ, y = hwy))
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420756221.png/wm)

![1](http://labfile.oss.aliyuncs.com/courses/855/unnamed-chunk-2-1.png)

这是我们第一次接触 ggplot 的语法，这行代码虽然很短，却已经包含几个绘图的核心要素：

- 数据：ggplot 的数据集必须是一个数据框，这里我们的数据是 mpg。
- 图形属性映射：将数据变量映射到图形中，我们这里使用 `aes(x = displ, y = hwy)` 把 x 坐标映射到排气量，y 坐标映射到每公里耗油量。
- 几何对象：geom 代表几何对象，比如我们这里想画散点图，就用 `geom_point` 来生成散点图。

从这张图我们可以发现排气量与耗油成反向关系，排气量越大，耗油越少。它们的关系大致是线性的，但也有一些例外，比如左上和右上的一些点。很容易想到，耗油量不仅与排气量有关，还与车的类型有关，我们可以尝试把车型的信息加入到图中：

```r
ggplot(data = mpg) + geom_point(mapping = aes(x = displ, y = hwy, color = class))
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420790487.png/wm)

![1](http://labfile.oss.aliyuncs.com/courses/855/unnamed-chunk-3-1.png)

在属性映射中加入 `color=class` 参数后，我们可以看到每个点的汽车对应的类型被用不同颜色表现了出来，对于散点图还有 size(大小) , shape（形状）等等参数可以用于确定点的属性。

这样我们就成功地在一个二维的图形上展示了三个变量之间的关系，可以看到排气量较大但耗油量也大的大多属于 2seater （2 个座位的跑车）这一类型，类型与耗油确实有很大关系。

为了进一步地分析类型与耗油的关系，我们会想到把不同的类型的车的数据分离开来，而不是画在一张图上，我们可以使用 `facet_wrap` 把他们分离开来：

```r
ggplot(data = mpg) +
  geom_point(mapping = aes(x = displ, y = hwy)) +
  facet_wrap(~ class)
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420819018.png/wm)

![1](http://labfile.oss.aliyuncs.com/courses/855/unnamed-chunk-4-1.png)

简单的一句话就将不同类型分离开来 ， ggplot 的绘画能力真是令人惊叹。

现在我们回到最初的地方，分析排气量和耗油量的关系，来看一看不同的图层是如何叠加的。从原来的图上我们可以看到一种强烈的线性关系，能不能拟合一条曲线并把它画到图上呢？

```r
ggplot(data = mpg) +
  geom_point(mapping = aes(x = displ, y = hwy)) +
  geom_smooth(mapping = aes(x = displ, y = hwy))
```

```txt
    ## `geom_smooth()` using method = 'loess'
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420837776.png/wm)

![1](http://labfile.oss.aliyuncs.com/courses/855/unnamed-chunk-5-1.png)

上面拟合所用的方法是 loess，翻译成中文就是近邻多项式回归，是一种非参数方法，所以由于几种跑车的存在，曲线右边翘了起来。曲线的阴影部分是置信区间的上下界。

如果我们想拟合普通的线性回归，我们可以改变 method 参数：

```r
ggplot(mpg , aes(x = displ, y = hwy)) +
  geom_point() +
  geom_smooth(method = "lm")
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420921498.png/wm)

![1](http://labfile.oss.aliyuncs.com/courses/855/unnamed-chunk-6-1.png)

在这个语句中，我们把数据映射写到了第一个括号里，意思是后面的所有的几何对象都共享 这一映射，这样代码得到了简化。

通过上面的一系列的分析，我们基本掌握了 ggplot 的核心思想。如果想了解更多的几何对象，可以参考 ggplot 的 [cheatsheet](https://rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)，如果要进一步了解 ggplot 背后的图层理论，可以参考书籍 [ggplot2](https://www.springer.com/gp/book/9780387981413)。

### 数据转换：dplyr

- 管道操作

在介绍 dplyr 前，首先应了解管道操作 `%>%` 的含义，管道由是由 Stefan Milton Bache 制作的 magrittr 包介绍的，主要作用在于简化多次线性操作。

例如我们有一个多层嵌套的操作：

```r
func_3(func_2(func_1(data, arg1 = haha),arg2 = huhu), arg3 = hoho)
```

这样书写看起来一团糟，而且容易弄混，如果我们用管道操作，可以表达为：

```r
data %>% func_1(arg1 = haha) %>% func_2(arg2 = huhu) %>% func_3(arg3 = hoho)
```

逻辑链条变得清晰易懂。

- `filter()`

`filter()` 是一个用于筛选行的函数，例如我们想筛出排量大于等于 5，高速路每公里耗油小于 20 的车：

```r
mpg %>% filter(displ >=5 , hwy < 20)
```

```txt
    ## # A tibble: 29 × 11
    ##    manufacturer              model displ  year   cyl      trans   drv
    ##           <chr>              <chr> <dbl> <int> <int>      <chr> <chr>
    ## 1     chevrolet c1500 suburban 2wd   5.3  2008     8   auto(l4)     r
    ## 2     chevrolet c1500 suburban 2wd   5.7  1999     8   auto(l4)     r
    ## 3     chevrolet c1500 suburban 2wd   6.0  2008     8   auto(l4)     r
    ## 4     chevrolet    k1500 tahoe 4wd   5.3  2008     8   auto(l4)     4
    ## 5     chevrolet    k1500 tahoe 4wd   5.3  2008     8   auto(l4)     4
    ## 6     chevrolet    k1500 tahoe 4wd   5.7  1999     8   auto(l4)     4
    ## 7     chevrolet    k1500 tahoe 4wd   6.5  1999     8   auto(l4)     4
    ## 8         dodge  dakota pickup 4wd   5.2  1999     8 manual(m5)     4
    ## 9         dodge  dakota pickup 4wd   5.2  1999     8   auto(l4)     4
    ## 10        dodge        durango 4wd   5.2  1999     8   auto(l4)     4
    ## # ... with 19 more rows, and 4 more variables: cty <int>, hwy <int>,
    ## #   fl <chr>, class <chr>
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499420892254.png/wm)

- `arrange()`

`arrange()` 是一个用于排序的函数，例如我们得到了这些排量较大，耗油较小的车，想按照生产日期降序排列，耗油量升序排列。

```r
mpg %>% filter(displ >=5 , hwy < 20) %>% arrange(desc(year) , hwy)
```

```txt
    ## # A tibble: 29 × 11
    ##    manufacturer               model displ  year   cyl    trans   drv   cty
    ##           <chr>               <chr> <dbl> <int> <int>    <chr> <chr> <int>
    ## 1     chevrolet     k1500 tahoe 4wd   5.3  2008     8 auto(l4)     4    11
    ## 2          jeep  grand cherokee 4wd   6.1  2008     8 auto(l5)     4    11
    ## 3     chevrolet  c1500 suburban 2wd   5.3  2008     8 auto(l4)     r    11
    ## 4     chevrolet  c1500 suburban 2wd   6.0  2008     8 auto(l4)     r    12
    ## 5         dodge ram 1500 pickup 4wd   5.7  2008     8 auto(l5)     4    13
    ## 6          ford     f150 pickup 4wd   5.4  2008     8 auto(l4)     4    13
    ## 7         dodge         durango 4wd   5.7  2008     8 auto(l5)     4    13
    ## 8          ford      expedition 2wd   5.4  2008     8 auto(l6)     r    12
    ## 9          jeep  grand cherokee 4wd   5.7  2008     8 auto(l5)     4    13
    ## 10      lincoln       navigator 2wd   5.4  2008     8 auto(l6)     r    12
    ## # ... with 19 more rows, and 3 more variables: hwy <int>, fl <chr>,
    ## #   class <chr>
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499421022593.png/wm)

- `select()`

这 11 个变量太多，我们只关心车型，那么可以通过 `select()` 函数把这一个变量单独提出来：

```r
mpg %>% filter(displ >=5 , hwy < 20) %>% arrange(desc(year) , hwy) %>% select(model)
```

```txt
    ## # A tibble: 29 × 1
    ##                  model
    ##                  <chr>
    ## 1      k1500 tahoe 4wd
    ## 2   grand cherokee 4wd
    ## 3   c1500 suburban 2wd
    ## 4   c1500 suburban 2wd
    ## 5  ram 1500 pickup 4wd
    ## 6      f150 pickup 4wd
    ## 7          durango 4wd
    ## 8       expedition 2wd
    ## 9   grand cherokee 4wd
    ## 10       navigator 2wd
    ## # ... with 19 more rows
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499421048774.png/wm)

- `mutate()`

我们回到原来的 mpg 数据集，按照常识，排气管越多，排量越大，我们想生成一个新变量来看一看每根排气管的平均排气量是不是很接近。

```r
mpg %>% mutate(ave_displ= displ / cyl)  %>% select(ave_displ)
```

```txt
    ## # A tibble: 234 × 1
    ##    ave_displ
    ##        <dbl>
    ## 1  0.4500000
    ## 2  0.4500000
    ## 3  0.5000000
    ## 4  0.5000000
    ## 5  0.4666667
    ## 6  0.4666667
    ## 7  0.5166667
    ## 8  0.4500000
    ## 9  0.4500000
    ## 10 0.5000000
    ## # ... with 224 more rows
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499421091156.png/wm)

```r
var(mpg %>% mutate(ave_displ= displ / cyl)  %>% select(ave_displ))
```

```txt
    ##             ave_displ
    ## ave_displ 0.007758093
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499421098555.png/wm)

可以发现我们的猜想大致正确， 多数车的平均排气量都在 0.5 到 0.7 之间，计算出来的方差也非常小。

- `group_by()`

有的时候我们不想看单个样本，而是想按照某个标准把数据分成几组，再来分别看这些组的统计特征有什么差异，那么我们可以先用 `group_by()` 按照条件分组，再用 `summarise()` 算出每组组内的统计特征。

例如我们想看不同车型的平均排气量和平均耗油量：

```r
mpg %>% group_by(class) %>% summarise(mean(displ) , mean(hwy))
```

```txt
    ## # A tibble: 7 × 3
    ##        class    mean(displ)   mean(hwy)
    ##        <chr>         <dbl>       <dbl>
    ## 1    2seater      6.160000    24.80000
    ## 2    compact      2.325532    28.29787
    ## 3    midsize      2.921951    27.29268
    ## 4    minivan      3.390909    22.36364
    ## 5     pickup      4.418182    16.87879
    ## 6 subcompact      2.660000    28.14286
    ## 7        suv      4.456452    18.12903
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid276733labid3136timestamp1499421123989.png/wm)

上面的几个函数是 dplyr 包的核心函数，如果要进一步了解 dplyr 的其他函数可以参考 dplyr 的 [cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)。

## 总结

今天的实验围绕 ggplot 和 dplyr 两个工具对 mpg 数据集进行了简单的分析，我们看到了 ggplot 强大的表现力以及 dplyr 链式表达的简洁，熟练掌握这两个工具使得分析中的可视化和数据转换变得轻松愉快。

tidyverse 中的其他工具大多上手容易或使用面较少，有兴趣的同学可以阅读 [R for data science](https://r4ds.had.co.nz/index.html) 做进一步地了解。
