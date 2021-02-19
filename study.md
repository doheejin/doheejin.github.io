---
layout: page
title: "Study"
permalink: /study/
main_nav: true
---

{% for category in site.categories %}
  {% capture cat %}{{ category | first }}{% endcapture %}
  {% if cat == "paper-review" or cat == "neural-machine-translation" %}
  <h2 id="{{cat}}">{{ cat | capitalize }}</h2>
  {% for desc in site.descriptions %}
  {% if desc.cat == cat %}<p class="desc"><em>{{ desc.desc }}</em></p>{% endif %}
  {% endfor %}
  <ul class="posts-list">{% for post in site.categories[cat] %}
  <li><strong><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></strong>
  <span class="post-date">- {{ post.date | date_to_long_string }}</span></li>
  {% endfor %}</ul>
  {% if forloop.last == false %}<hr>{% endif %}{% endif %}{% endfor %}
<br>