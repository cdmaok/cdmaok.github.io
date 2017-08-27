---
layout: post
title: Tags
permalink: /tags/
---

<section id="archive">
<h2><i class="fa fa-file-archive-o"></i>&nbsp;Tags</h2>
  <ul>
{% for category in site.categories %}
    <!--
<li style="font-size: {{ category | last | size | times: 100 | divided_by: site.categories.size }}%">
	-->
    <li>
        <a href="/{{ category | first | slugize }}/">
            {{ category | first }}
        </a>
    </li>
{% endfor %}
  </ul>

</section>
