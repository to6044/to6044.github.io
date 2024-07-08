---
layout: archive
title: "AWS"
permalink: /aws/
---

{% assign entries_layout = page.entries_layout | default: 'list' %}
<div class="entries-{{ entries_layout }}">
  {% for post in site.aws %}
    {% include archive-single.html type=entries_layout %}
  {% endfor %}
</div>