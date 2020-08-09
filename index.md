---
layout: default
title: "a common theme"
dark: false
---

<nav>
<h2>Projects</h2> {% for page in site.pages %}{% if page.categories contains 'project' %}<a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a><br/> {% endif %}{% endfor %}
</nav>

> I have yet to see any problem, however complicated, which, when you looked at it in the right way, did not become still more complicated. --- *[Anderson's Law](https://en.wikiquote.org/wiki/Poul_Anderson)*

<!--
**Writings**: {% for page in site.pages %}{% if page.categories contains 'writing' %}<a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a>, {% endif %}{% endfor %} ...
-->

