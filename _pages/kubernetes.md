---
layout: archive
title: "K8S"
permalink: /kubernetes/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.kubernetes %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>