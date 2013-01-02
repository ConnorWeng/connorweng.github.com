---
layout: page
title: Jungo Lab
tagline: Supporting tagline
---
{% include JB/setup %}

<img src="assets/themes/dinky/images/self-banner.jpg"/>

## All Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}" target="_blank">{{ post.title }}</a></li>
  {% endfor %}
</ul>
