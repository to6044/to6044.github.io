---
layout: archive
title: "PAPER"
permalink: /paper/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.paper %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>