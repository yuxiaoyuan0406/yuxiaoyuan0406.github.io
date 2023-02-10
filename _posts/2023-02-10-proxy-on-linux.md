---
layout: article
title: 【some topic】main title
permalink: /article/:title.html
key: template-key
tags: 
  - blog
  - Liquid
  - jekyll
author: Yu Xiaoyuan
show_author_profile: true
license: WTFPL
---

<!-- abstract begin -->
This is a template for future posts
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
