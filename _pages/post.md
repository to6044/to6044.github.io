---
layout: archive
title: "POST"
permalink: /post/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.post %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>