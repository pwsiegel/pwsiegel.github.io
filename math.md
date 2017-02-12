## Math
This page is where I keep my math.

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }} - {{ post.date }}</a>
    </li>
  {% endfor %}
</ul>
