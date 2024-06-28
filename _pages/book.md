---
layout: archive
title: "BOOK"
permalink: /book/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.book %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>