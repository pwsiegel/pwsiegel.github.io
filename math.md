# Math

<ul>
  {% for post in site.categories.math %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
      <p>{{ post.abstract }}</p>
    </li>
  {% endfor %}
</ul>
