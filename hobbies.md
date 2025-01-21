---
layout: page
title: 취미
permalink: /hobbies/
---

<ul class="post-list">
  {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
  {%- for post in site.posts %}
    {% if post.category == "hobbies" %}
      <li class="post-item">
        <a class="post-link" href="{{ post.url | relative_url }}">
          {{ post.title | escape }}
        </a>&nbsp;<span class="post-meta">({{ post.date | date: date_format }})</span>
      </li>
    {% endif %}
  {%- endfor %}
</ul>
