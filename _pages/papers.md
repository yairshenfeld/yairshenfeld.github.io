---
title: Papers
permalink: /papers/
class: papers-page
---

{% for paper in site.data.papers %}
{% include paper.html paper=paper %}
{% endfor %}

<div class="author-order-legend" aria-label="Author-order notation">
  <div><sup aria-hidden="true">†</sup> Authors listed in alphabetical order.</div>
  <div><sup aria-hidden="true">‡</sup> Authors listed in non-alphabetical order.</div>
</div>
