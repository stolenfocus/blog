---
layout: page
title: "부모님 연금저축 ETF"
permalink: /category/etf/
---


## 전략

{% for post in site.categories.etf %}{% if post.tags contains 'strategy' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 주간 리포트

{% for post in site.categories.etf %}{% if post.tags contains 'weekly' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}
