---
title: "News & Updates Archive"
permalink: /news/
layout: splash
classes: wide
author_profile: false
---

<h1 class="archive-title">News & Updates</h1>

<ul class="news-list">
  {% for item in site.data.news %}
    <li><span class="date">{{ item.date }}</span> <span class="emoji">{{ item.emoji }}</span> <span class="topic">{{ item.text | markdownify | remove: '<p>' | remove: '</p>' }}</span></li>
  {% endfor %}
</ul>

