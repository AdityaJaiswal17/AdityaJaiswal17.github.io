---
layout: default
title: "Finite Difference Method Projects"
permalink: /projects/fdm/
---

## Finite Difference Method Projects

  {% assign fdm_projects = site.projects | where: "method", "fdm" %}
  {% for project in fdm_projects %}
  <div class="project-entry">
    <h3><a href="{{ project.url }}">{{ project.title }}</a></h3>
    <p>{{ project.excerpt }}</p>
  </div>
  <hr>
{% endfor %}
