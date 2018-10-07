---
layout: content
title: "Tags"
tagline: "Select a tag to view associated posts."
permalink: /tags/
image:
  feature: 
  hero: 
---
{% for tag in site.tags %} {% assign t = tag | first %} <a class="post-tags lg" href="/tags/#{{t | replace:" ","-" }}">{{ t }}</a> {% endfor %}

---

{% for tag in site.tags %}
  {% assign t = tag | first %}
  {% assign posts = tag | last %}

<h4><a name="{{t | downcase | replace:" ","-" }}"></a><a class="internal" href="/tags/#{{t | replace:" ","-" }}">{{ t | upcase }}</a></h4>
<ul>
{% for post in posts %}
  {% if post.tags contains t %}
  <li>
    <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: "%B %-d, %Y"  }}
  </li>
  {% endif %}
{% endfor %}
</ul>

---

{% endfor %}
