---
layout: default
title: "a common theme"
dark: true
---

<nav>
<h2>Projects</h2> {% for page in site.pages %}{% if page.categories contains 'project' %}<a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a><br/> {% endif %}{% endfor %}
</nav>

**a common theme**: how to not end up in a mess -- or -- less is more, nothing is everything. In code, writing and life. 
What is essential to understand cannot be said, but learned following clues; like: _better not look at my finger when I'm pointing to the moon_.

In light of this,

> I have yet to see any problem, however complicated, which, when you looked at it in the right way, did not become still more complicated. --- *[Anderson's Law](https://en.wikiquote.org/wiki/Poul_Anderson)*

Seeing technologies rise and fall on planet Wyh Minsky thought:

> I can help


<!--
**Writings**: {% for page in site.pages %}{% if page.categories contains 'writing' %}<a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a>, {% endif %}{% endfor %} ...
-->

