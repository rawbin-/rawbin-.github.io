---
layout: page
title: 主页
tagline: 文章列表
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
	{% unless post.title == empty %}
		<li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
	{% endunless %}	
  {% endfor %}
</ul>


