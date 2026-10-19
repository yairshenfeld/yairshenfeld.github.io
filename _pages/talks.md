---
title: Talks
permalink: /talks/
class: talks-page
---

{% for section in site.data.talks %}
<h2 class="talk-section-title">{{ section.title | escape }}</h2>
{% for talk in section.talks %}
{% include talk.html talk=talk %}
{% endfor %}
{% endfor %}
