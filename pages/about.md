---
layout: page
title: About
description: Coding Changes the World
keywords: Xin Lyu, 吕鑫
comments: true
menu: 关于
permalink: /about/
---

## Me

lvxiaoxin

一只在刚刚踏上技术大陆的萌新宝宝

从积水潭女子师专（北京师范大学）初出茅庐

在风控模型搞研发(Wecash Inc.)

> I admire all miracles made by science.

<br />

You can find my CV here.

[CV](https://lvxiaoxin.github.io/CV.pdf)


## Contact

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
