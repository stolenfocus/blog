---
layout: default
title: "코인 — BTC 가변 DCA"
permalink: /category/crypto/
---

# 코인 — BTC 가변 DCA

{% for post in site.categories.crypto %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}
