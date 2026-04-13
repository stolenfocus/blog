---
layout: default
title: "주간 리포트"
permalink: /category/weekly-report/
---

# 주간 리포트

{% for post in site.categories.weekly-report %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}
