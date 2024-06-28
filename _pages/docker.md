---
layout: archive
title: "DOCKER"
permalink: /docker/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.docker %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>