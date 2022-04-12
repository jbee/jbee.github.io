---
layout: default
title: jbee.github.io - Jan Bernitt
dark: false
---

# jbee.github.io

Welcome on my personal page.
<p>This page is dedicated to my adventues in programming.</p>

Read my <a href="/cv/">CV</a>, or have a look at bit of extra documentation and background on my main pet projects...

<dl>
	<dt>See Also:</dt>
	<dd>
		{% for page in site.pages %}{% if page.categories contains 'project' %}
		<a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a> &nbsp; {{ page.summary }}<br/>
		{% endif %}{% endfor %}
	</dd>
</dl>

<!--
## Posts

{% for page in site.pages %}{% if page.categories contains 'writing' %}<a href="{{ page.url }}" title="{{ page.summary }}">{{ page.title }}</a><br/>{% endif %}{% endfor %}
-->
