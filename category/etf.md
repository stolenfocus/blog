---
layout: page
title: "부모님 연금저축 ETF"
permalink: /category/etf/
---


## 전략

{% for post in site.categories.etf %}{% if post.tags contains 'strategy' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 월간 DCA

{% for post in site.categories.etf %}{% if post.tags contains 'dca' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 주간 리포트

{% for post in site.categories.etf %}{% if post.tags contains 'weekly' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 기타 / 전체 글

{% assign known_tags = "strategy,dca,weekly" | split: "," %}
{% for post in site.categories.etf %}
  {% assign show = true %}
  {% for t in known_tags %}
    {% if post.tags contains t %}{% assign show = false %}{% endif %}
  {% endfor %}
  {% if show %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
  {% endif %}
{% endfor %}
