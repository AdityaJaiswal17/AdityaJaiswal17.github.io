---
layout: default
title: "Laticce Boltzmann Projects"
permalink: /projects/lbm/
---

## Laticce Boltzmann Projects

{% assign lbm_projects = site.projects | where: "method", "lbm" %}
<ul>
  {% for project in lbm_projects %}
    <li><a href="{{ project.url }}">{{ project.title }}</a></li>
  {% endfor %}
</ul>
