---
layout: archive
title: "ISTIO"
permalink: /istio/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.istio %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>