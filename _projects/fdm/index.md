---
# layout: default
title: "Finite Difference Projects"
permalink: /projects/fdm/
---

## Finite Difference Projects

{% assign fdm_projects = site.projects | where: "method", "fdm" %}
<ul>
  {% for project in fdm_projects %}
    <li><a href="{{ project.url }}">{{ project.title }}</a></li>
  {% endfor %}
</ul>
