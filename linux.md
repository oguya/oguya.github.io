---
layout: page
title: Linux Posts
---

{% for post in site.posts %}
<!-- seems like you cant do regex on jinja2: re.match('linux',post.category, re.IGNORECASE) -->
{% if post.category == 'Linux' %}
  <p><a href="{{ post.url }}"> >> {{ post.title }}</a></p>
  <p>{{ post.excerpt }}</p>
  -------
{% endif %}
{% endfor %}

<div class="pagination">
  {% if paginator.next_page %}
    <a class="pagination-item older" href="{{ site.baseurl }}page{{paginator.next_page}}">Older</a>
  {% else %}
    <span class="pagination-item older">Older</span>
  {% endif %}
  {% if paginator.previous_page %}
    {% if paginator.page == 2 %}
      <a class="pagination-item newer" href="{{ site.baseurl }}">Newer</a>
    {% else %}
      <a class="pagination-item newer" href="{{ site.baseurl }}page{{paginator.previous_page}}">Newer</a>
    {% endif %}
  {% else %}
    <span class="pagination-item newer">Newer</span>
  {% endif %}
</div>
