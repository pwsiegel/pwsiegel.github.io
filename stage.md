---
layout: default
---

# Stage

These posts are still in progress.
Shoo.

<ul>
   {% for post in site.posts %}
      {% unless post.tags contains "live" %}
         <li>
            <a href="{{ post.url }}">{{ post.title }}</a> {{ site[post.level] }} - {{ post.date | date: '%B %d, %Y' }}
            <p>{{ post.abstract }}</p>
         </li>
      {% endunless %}
   {% endfor %}
</ul>
