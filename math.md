## Math
This page is where I keep my math...

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }} - {{ page.date | date: '%B %d, %Y' }}</a>
    </li>
  {% endfor %}
</ul>
