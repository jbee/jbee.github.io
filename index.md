---
layout: default
title: "Programming Wonderland"
---

# Programming Wonderland

A common theme: How to not end up in a mess -- or -- less is more, nothing is everything. In code, writing and life. 
What is essential to understand cannot be said, but learned following clues. Don't look at my finger when I'm pointing to the moon.

> I have yet to see any problem, however complicated, which, when you looked at it in the right way, did not become still more complicated. --- *[Anderson's Law](https://en.wikiquote.org/wiki/Poul_Anderson)*


## Projects

<dl>
{% for page in site.pages %}
{% if page.categories contains 'project' %}<dt><a href="{{ page.url }}">{{ page.title }}</a></dt><dd>{{page.summary}}</dd>{% endif %}
{% endfor %}
</dl>

## Writings 

<dl>
{% for page in site.pages %}
{% if page.categories contains 'writing' %}<dt><a href="{{ page.url }}">{{ page.title }}</a></dt><dd>{{page.summary}}</dd>{% endif %}
{% endfor %}
</dl>




