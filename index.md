---
title: Home
layout: default
excerpt_separator: "<!--more-->"
---

<div class="posts">
  {% for post in site.posts %} 
    {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
        <div class="post">
            <h1 class="post-title">
            <a href="{{ post.url | relative_url }}">
                    {{ post.title | escape }}
            </a>
            </h1>
            <p>
                {{ post.excerpt }}
            </p>
            <p class="post-meta">Posted by
                <a href="https://github.com/{{ site.github_username }}">
                    {{ site.github_username | escape }}
                </a>
                on {{ post.date | date: date_format }}
            </p>
        </div>
  {% endfor %}
</div>
<!-- Pager -->
<div class="pagination">
  {% if paginator.next_page %}
    <a class="pagination-item older" href="{{ paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' | replace: 'index.html', '' }}">Older</a>
  {% else %}
    <span class="pagination-item older">Older</span>
  {% endif %}
  {% if paginator.previous_page %}
    {% if paginator.page == 2 %}
      <a class="pagination-item newer" href="{{ site.baseurl }}">Newer</a>
    {% else %}
      <a class="pagination-item newer" href="{{ paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' }}">Newer</a>
    {% endif %}
  {% else %}
    <span class="pagination-item newer">Newer</span>
  {% endif %}
</div>