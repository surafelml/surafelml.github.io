---
title: "Research & Publications"
permalink: /research/
layout: splash
classes: wide
author_profile: false
---

<h1 class="archive-title">Research & Publications</h1>

{% assign papers = site.data.research | where_exp: "p", "p.type != 'patent'" %}
{% assign patents = site.data.research | where: "type", "patent" %}

{% assign grouped = papers | group_by: "year" | sort: "name" | reverse %}
{% for group in grouped %}
  <h2 class="year-heading">{{ group.name }}</h2>
  <div class="research-list">
    {% for project in group.items %}
      {% include research-item.html project=project %}
    {% endfor %}
  </div>
{% endfor %}

{% if patents.size > 0 %}
  <h2 class="year-heading">Patents</h2>
  <div class="research-list">
    {% for project in patents %}
      {% include research-item.html project=project %}
    {% endfor %}
  </div>
{% endif %}
