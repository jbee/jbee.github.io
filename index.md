---
layout: default
title: jbee.github.io - Jan Bernitt
dark: false
---

# jbee.github.io

Welcome on my person page.

Read my <a href="/cv/">CV</a>,
<p>or have a look at bit of extra documentation and background on my main pet projects:
<ul>
{% for page in site.pages %}{% if page.categories contains 'project' %}<li><a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a>: {{ page.summary }}</li> {% endif %}{% endfor %}
</ul>
</p>

<!--
## Posts

{% for page in site.pages %}{% if page.categories contains 'writing' %}<a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a><br/>{% endif %}{% endfor %}
-->
