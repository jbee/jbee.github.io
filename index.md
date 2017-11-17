---
layout: default
title: "Programming Wonderland"
---

# Programming Wonderland

A common theme: How to not end up in a mess -- or -- less is more, nothing is everything. In code, writing and life. 
What is essential to understand cannot be said, but learned following clues. Don't look at my finger when I'm pointing to the moon.

> I have yet to see any problem, however complicated, which, when you looked at it in the right way, did not become still more complicated. --- *[Anderson's Law](https://en.wikiquote.org/wiki/Poul_Anderson)*


## Projects

* [silk](http://jbee.github.io/silk/): Java dependency injection through code
* [Linear expressions](lex/): simple and fast pattern matching
* ... (more... but it still evolves a lot)


## Writings 

<ul>
{% for page in site.pages %}
{% if page.categories contains 'writing' %}<li><a href="{{ page.url }}">{{ page.title }}</a>: {{page.summary}}</li>{% endif %}
{% endfor %}
</ul>




