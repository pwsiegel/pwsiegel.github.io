# Data Science

<ul>
  {% for post in site.categories.data_science %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
    </li>
  {% endfor %}
</ul>
