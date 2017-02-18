# Stage

Staging area for posts that are in progress.

<ul>
  {% for post in site.categories.stage %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
      <p>{{ post.abstract }}</p>
    </li>
  {% endfor %}
</ul>
