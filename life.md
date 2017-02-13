# Life
Content may appear here when I get one of these.

<ul>
  {% for post in site.categories.life %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>
