---
layout: page
title: recent texts
header : Post Archive
tagline: 
---
{% include JB/setup %}

hello and welcome to my texts :-)

<ul class="posts">
{% for post in site.posts limit: 15 %}
  <div class="post_info">
    <li>
            <a href="{{ post.url }}">{{ post.title }}</a>
            <span>({{ post.date | date:"%Y-%m-%d" }})</span>
    </li>
    </br> <em>{{ post.excerpt }} </em>
    </div>
  {% endfor %}
</ul>need to clean up the themes, make theme usage guides with theme-specific markup examples.


