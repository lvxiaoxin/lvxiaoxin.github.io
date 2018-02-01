---
layout: page
title: About
description: Coding Changes the World
keywords: Xin Lyu, 吕鑫
comments: true
menu: 关于
permalink: /about/
---

I'm Xin Lyu, a student majoring in Computer Science at Beijing Normal University.

I admire all miracles made by science.

I look forward to using my technology to change the world.

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
