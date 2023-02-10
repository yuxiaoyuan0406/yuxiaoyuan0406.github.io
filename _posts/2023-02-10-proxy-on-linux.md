---
layout: article
title: 【一点点Linux】在Linux上设置Proxy代理
permalink: /article/:title.html
key: proxy-on-linux
tags: 
  - Linux
  - git
  - proxy
  - bash
  - zsh
author: Yu Xiaoyuan
show_author_profile: true
license: CC-BY-NC-ND-4.0
---

<!-- abstract begin -->
在Linux上访问某些内容或者pull/push到GitHub时总会遇到代理设置的问题，系统的代理配置有时候不能反应到命令行中。
本文主要介绍了如何给命令行的指令、git和ssh配置代理，并且通过文件进行管理。
<!-- abstract end -->

<!--more-->

<!-- begin include -->
{%- include general-variables.html -%}
<!-- end include -->

<!-- begin private variable of Liquid -->

<!-- {%- increment equation-h2-1 -%} -->
<!-- end private variable of Liquid -->

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

## section 2

{% for shit in page %}
{{ shit }}
{% endfor %}

## reference

[Liquid: Safe, customer-facing template language for flexible web apps.](https://shopify.github.io/liquid/)
