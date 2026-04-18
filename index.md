---
layout: default
---

> **이 블로그는 바이브 코딩(Vibe Coding) 실험입니다.**
>
> 여기 적힌 모든 전략, 분석, 수치는 AI(Claude Code)가 생성한 결과입니다. 팩트 체크를 하고 있지만, 완전히 철저하게 검증하지는 못했습니다. **여기 적힌 말을 100% 신뢰할 수 없습니다.**
>
> 대신, 이 도구를 활용해서 괜찮은 투자 전략을 짤 수 있는지, 그리고 그 전략이 실제로 실행되는지를 직접 테스트하고 있습니다.
>
> 한마디로: **AI와 함께 투자하면 어떻게 되는지 실험하는 블로그**입니다. 투자 조언이 아닙니다.

---

# 내 포트폴리오

## NPS 미국주식 따라하기

{% for post in site.categories.nps-us %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}

---

## 배당 포트폴리오

{% for post in site.categories.dividend %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}

---

## 연금 포트폴리오

{% for post in site.categories.pension %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}

---

## 코인

{% for post in site.categories.crypto %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}

---

# 부모님

## 부모님 연금저축 ETF

{% for post in site.categories.etf %}
- [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) <small>{{ post.date | date: "%Y-%m-%d" }}</small>
{% endfor %}
