---
layout: page
title: Tags
permalink: /tags/
---

{% assign tags = site.tags | sort %}

<div class="tag-cloud">
  {% for tag in tags %}
    <a href="#{{ tag[0] | slugify }}" class="tag-link">
      {{ tag[0] }} ({{ tag[1].size }})
    </a>
  {% endfor %}
</div>

<hr>

{% for tag in tags %}
  <h2 id="{{ tag[0] | slugify }}">{{ tag[0] }}</h2>
  <ul>
    {% for post in tag[1] %}
      <li>
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        <span class="post-meta"> - {{ post.date | date_to_string }}</span>
      </li>
    {% endfor %}
  </ul>
{% endfor %}
