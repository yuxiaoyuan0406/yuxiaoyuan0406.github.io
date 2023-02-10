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

## 网络环境

在之前的一篇文章中笔者在树莓派上配置了[局域网代理服务器]( {% post_url 2022-08-18-local-proxy-server %} )。  
另一篇文章中配置了树莓派的防火墙以[允许代理服务器端口通过]({% post_url 2022-12-08-firewall-on-pi %}) 。  
树莓派上的一些基础配置和网络环境配置如下表所示。

| 树莓派域名 | IPv4 | 子网掩码 | Proxy代理协议 | 监听端口 |
|:---:|:---:|:---:|:---:|:---:|
| `raspberrypi.local` | `192.168.1.2` | `255.255.254.0` | http | `7890` |

这些配置非常重要，在应用配置环节需要对应参数进行设置。

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
