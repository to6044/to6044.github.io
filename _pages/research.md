---
layout: archive
title: "RESEARCH"
permalink: /research/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.research %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>