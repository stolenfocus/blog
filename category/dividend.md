---
layout: page
title: "배당 포트폴리오"
permalink: /category/dividend/
---


## 전략

{% for post in site.categories.dividend %}{% if post.tags contains 'strategy' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 월간 DCA

{% for post in site.categories.dividend %}{% if post.tags contains 'dca' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 매수/매도 기록

{% for post in site.categories.dividend %}{% if post.tags contains 'trade' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 주간 리포트

{% for post in site.categories.dividend %}{% if post.tags contains 'weekly' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 기타 / 전체 글

{% assign known_tags = "strategy,dca,trade,weekly" | split: "," %}
{% for post in site.categories.dividend %}
  {% assign show = true %}
  {% for t in known_tags %}
    {% if post.tags contains t %}{% assign show = false %}{% endif %}
  {% endfor %}
  {% if show %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
  {% endif %}
{% endfor %}
