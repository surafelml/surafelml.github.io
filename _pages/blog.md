---
title: Blog Posts
permalink: /blog/
layout: splash
classes: wide
author_profile: false
---

<h1 class="archive-title">Blog & Notes</h1>

<ul class="post-list">
  {% assign posts = site.blog | sort: "date" | reverse %}
  {% for post in posts %}
    <li class="post-item">
      <h3 class="post-item-title"><span class="emoji">{{ post.emoji | default: "📝" }}</span> {% if post.external_url %}<a href="{{ post.external_url }}" target="_blank" rel="noopener">{{ post.title }} <i class="fas fa-external-link-alt entry-ext"></i></a>{% else %}<a href="{{ post.url | relative_url }}">{{ post.title }}</a>{% endif %}</h3>
      {% if post.excerpt %}<p class="post-item-desc">{{ post.excerpt | strip_html | strip }}</p>{% endif %}
      {% capture meta %}{{ post.date | date: "%b %d, %Y" }}{% if post.tags.size > 0 %} &middot; {{ post.tags | join: " · " }}{% endif %}{% endcapture %}
      <div class="post-item-meta">{{ meta }}</div>
    </li>
  {% endfor %}
</ul>

