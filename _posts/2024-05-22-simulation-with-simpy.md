---
layout: article
title: 【系统仿真】基于Simpy的多模块反馈系统仿真
permalink: /article/:title.html
key: simulation-with-simpy
tags: 
  - Python
  - Simpy
  - MEMs
author: Yu Xiaoyuan
show_author_profile: true
license: WTFPL
---

<!-- abstract begin -->
笔者这段时间在系统仿真方面进行了一系列学习，包括一点点求解微分方程的数值方法和PID控制等。
<!-- abstract end -->

<!--more-->

<!-- begin include -->
{%- include general-variables.html -%}
<!-- end include -->

<!-- begin private variable of Liquid -->

<!-- {%- increment equation-h2-1 -%} -->
<!-- end private variable of Liquid -->

## 系统介绍

被仿真系统可以被分为下列3个模块：

1. 弹簧阻尼振子系统
2. 位移控制PID模块
3. 静电力反馈模块

其中弹簧阻尼系统和PID模块之间包括一个线性转化模块，将质量块的位移转化为电路中的电压，从而可以被PID模块使用。
PID模块输出一个1bit量化数据，用于控制静电力反馈模块的静电力方向，静电力的大小则由质量块的位移决定。
并且在一个周期内，静电力只有一部分时间启动，其余时间并没有静电力作用于弹簧阻尼系统。

整个系统的输入为外部加速度，输出为PID量化比特流。

本文不介绍细节内部理论，只讨论跟仿真相关的内容。

## 需求分析



## section 1

{{ image_dir }}

### link example 1

[link to first post]({% link _posts/2019-11-02-new-pi-4b.md %})

### link example 2

[link to first post]({% post_url 2019-11-02-new-pi-4b %})

### image example

![image]({{ image_dir }}/404.jpg "comments"){:.rounded}

### code block example

code block with line number

{% highlight c linenos %}
#include <stdio.h>

int main() {
    printf("hello world\n");

    return 0;
}
{% endhighlight %}

### embeded BiliBili video

<div> {%- include extensions/bilibili.html id="BV11d4y1j7na" -%} </div>

## section 2

{% for shit in page %}
{{ shit }}
{% endfor %}

## reference

[Liquid: Safe, customer-facing template language for flexible web apps.](https://shopify.github.io/liquid/)
