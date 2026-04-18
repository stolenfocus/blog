---
layout: page
title: "코인"
permalink: /category/crypto/
---

# 코인

## 토큰화 시리즈

{% assign token_posts = site.categories.crypto | where_exp: "post", "post.tags contains 'tokenization'" | sort: "title" %}
{% for post in token_posts %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}

## BTC 가변 DCA

{% for post in site.categories.crypto %}{% if post.tags contains 'btc-dca' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}
