---
permalink: /
classes: wide index-page
layout: splash
author_profile: false
--- 

<div class="hero">
  <img class="hero-avatar" src="/assets/images/surafelml_gh_avatar.jpeg" alt="Surafel M. Lakew">
  <h1 class="hero-name">Surafel M. Lakew</h1>
  <p class="hero-nav">
    <a href="#news">News</a> &middot; <a href="#blog">Blog</a> &middot; <a href="#research">Research</a> &middot; <a href="#projects">Projects</a>
  </p>
  <p class="hero-links">
    <a href="https://github.com/surafelml"><i class="fab fa-fw fa-github"></i> GitHub</a> &nbsp;
    <a href="https://scholar.google.com/citations?user=GEB4LhgAAAAJ&hl=en"><i class="fas fa-fw fa-graduation-cap"></i> Scholar</a> &nbsp;
    <a href="https://www.linkedin.com/in/surafelml/"><i class="fab fa-fw fa-linkedin"></i> LinkedIn</a> &nbsp;
    <a href="mailto:surawinfo@gmail.com"><i class="fas fa-fw fa-envelope"></i> Email</a>
  </p>
</div>

I'm a researcher at [Amazon AWS AI](https://www.amazon.science/author/surafel-melaku-lakew), where I currently contribute to [Amazon Quick](https://aws.amazon.com/quick/) - an agentic AI assistant for work, previously on [Amazon Q](https://aws.amazon.com/q/business/) - an enterprise conversational assistant, and [Amazon Translate](https://aws.amazon.com/translate/) - custom translation and automatic dubbing. I hold a PhD from [University of Trento](http://ict.unitn.it/) & [FBK Institute](https://www.fbk.eu/), where my thesis focuses on [multilingual and custom neural machine translation](https://github.com/surafelml/phd-thesis).

<p class="focus-label">Research Focus</p>
<ul class="focus-list">
  <li><span class="emoji">🛡️</span> <span class="topic">AI Safety / Responsible AI (RAI)</span></li>
  <li><span class="emoji">📊</span> <span class="topic">Generative AI Evaluation (Agentic Eval)</span></li>
  <li><span class="emoji">🔍</span> <span class="topic">Retrieval-Augmented Generation (RAG)</span></li>
  <li><span class="emoji">🌐</span> <span class="topic">Neural Translation, Multilingual NLP & Automatic Dubbing</span></li>
</ul>

---

<h2 id="news">News & Updates</h2>

<ul class="news-list">
  {% for item in site.data.news limit:7 %}
    <li><span class="date">{{ item.date }}</span> <span class="emoji">{{ item.emoji }}</span> <span class="topic">{{ item.text | markdownify | remove: '<p>' | remove: '</p>' }}</span></li>
  {% endfor %}
</ul>

[View all news &rarr;](/news/){: .view-all }

---

<h2 id="blog">Blog & Notes</h2>

<ul class="entry-list">
  {% assign featured_posts = site.blog | where: "featured", true | sort: "date" | reverse %}
  {% assign rest_posts = site.blog | where_exp: "p", "p.featured != true" | sort: "date" | reverse %}
  {% assign blog_posts = featured_posts | concat: rest_posts %}
  {% for post in blog_posts limit:3 %}
    <li class="entry-item">
      <div class="entry-head"><span class="emoji">{{ post.emoji | default: "📝" }}</span>{% if post.external_url %}<a class="entry-title" href="{{ post.external_url }}" target="_blank" rel="noopener">{{ post.title }} <i class="fas fa-external-link-alt entry-ext"></i></a>{% else %}<a class="entry-title" href="{{ post.url | relative_url }}">{{ post.title }}</a>{% endif %}</div>
      <div class="entry-meta">{{ post.date | date: "%b %Y" }}</div>
    </li>
  {% endfor %}
</ul>

[Read all posts &rarr;](/blog/){: .view-all }

---

<h2 id="research">Research & Publications</h2>

<div class="research-list">
  {% assign featured = site.data.research | where: "featured", true %}
  {% for project in featured limit:7 %}
    {% include research-item.html project=project %}
  {% endfor %}
</div>

[View all research & publications &rarr;](/research/){: .view-all }

---

<h2 id="projects">R&amp;D Projects</h2>

{% if site.data.projects.size > 0 %}
<ul class="entry-list">
  {% for p in site.data.projects %}
    <li class="entry-item">
      <div class="entry-head"><span class="emoji">{{ p.emoji | default: "🛠️" }}</span>{% if p.url %}<a class="entry-title" href="{{ p.url }}">{{ p.title }}</a>{% else %}<span class="entry-title">{{ p.title }}</span>{% endif %}{% if p.description %} — <span class="entry-desc">{{ p.description | markdownify | remove: '<p>' | remove: '</p>' }}</span>{% endif %}</div>
      {% capture meta %}{% if p.year %}{{ p.year }}{% endif %}{% if p.tech %}{% if p.year %} &middot; {% endif %}{{ p.tech }}{% endif %}{% if p.status %}{% if p.year or p.tech %} &middot; {% endif %}{{ p.status }}{% endif %}{% endcapture %}
      {% if meta != "" %}<div class="entry-meta">{{ meta }}</div>{% endif %}
    </li>
  {% endfor %}
</ul>
{% else %}
<p class="empty-state">Coming soon — selected open-source and R&amp;D projects will appear here.</p>
{% endif %}

---

<div class="service-section">
  <h2 id="service">Community Contributions</h2>
  <ul>
    <li><strong>Program Committee / Reviewer 2017–*</strong>: ACL, IWSLT, EMNLP, COLM, ICLR, ICML, NeurIPS.</li>
    <li><strong>Mentorship</strong>: Mentoring junior researchers and students in Neural MT & NLP.</li>
    <li><strong>Open Source</strong>: see R&D Projects section.</li>
  </ul>
</div>