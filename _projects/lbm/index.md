---
layout: default
title: "Lattice Boltzmann Method Projects"
permalink: /projects/lbm/
---

## Finite Difference Method Projects

{% assign lbm_projects = site.projects | where: "method", "lbm" %}
{% for project in lbm_projects %}
<div class="project-entry">
  <h3><a href="{{ project.url }}">{{ project.title }}</a></h3>
  <p>{{ project.excerpt }}</p>
</div>
<hr>
{% endfor %}
