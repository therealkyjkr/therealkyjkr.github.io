---
layout: page
title: 일상
permalink: /personal/
---

<ul class="post-list">
  {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
  {%- for post in site.posts %}
    {% if post.category == "personal" %}
      <li class="post-item">
        <a class="post-link" href="{{ post.url | relative_url }}">
          {{ post.title | escape }}
        </a>&nbsp;<span class="post-meta">({{ post.date | date: date_format }})</span>
      </li>
    {% endif %}
  {%- endfor %}
</ul>
