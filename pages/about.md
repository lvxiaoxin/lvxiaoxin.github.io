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

>
> I admire all miracles made by science.
>

## Resume

**工作经历**

| 时间              | 公司      | 职位                                  |
| ----------------- | --------- | ------------------------------------- |
| 2019.05 - Now     | Microsoft | SDE (Office 365)                      |
| 2018.07 - 2019.04 | Wecash    | Python Engineer (Modeling Department) |

教育经历

| 时间              | 学校         | 学位                  |
| ----------------- | ------------ | --------------------- |
| 2014.09 - 2018.06 | 北京师范大学 | 计算机科学 - 理学学士 |




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
