---
layout: page
title: "배당 포트폴리오"
permalink: /category/dividend/
---

# 배당 포트폴리오

## 전략

{% for post in site.categories.dividend %}{% if post.tags contains 'strategy' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 매수/매도 기록

{% for post in site.categories.dividend %}{% if post.tags contains 'trade' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}
