---
layout: page
title: "코인"
permalink: /category/crypto/
---

# 코인

## 토큰화 시리즈

{% for post in site.categories.crypto %}{% if post.tags contains 'tokenization' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}

## BTC 가변 DCA

{% for post in site.categories.crypto %}{% if post.tags contains 'btc-dca' %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endif %}{% endfor %}
