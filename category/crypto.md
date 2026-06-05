---
layout: page
title: "코인"
permalink: /category/crypto/
---


## 토큰화 시리즈

{% assign token_posts = site.categories.crypto | where_exp: "post", "post.tags contains 'tokenization'" | sort: "title" %}
{% for post in token_posts %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}

## BTC 가변 DCA

{% for post in site.categories.crypto %}{% if post.tags contains 'btc-dca' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## 기타 / 전체 글

{% assign known_tags = "tokenization,btc-dca" | split: "," %}
{% for post in site.categories.crypto %}
  {% assign show = true %}
  {% for t in known_tags %}
    {% if post.tags contains t %}{% assign show = false %}{% endif %}
  {% endfor %}
  {% if show %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
  {% endif %}
{% endfor %}
