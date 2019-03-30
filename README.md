<blockquote>
<p>*"Everything in moderation, including moderation."*</p>
<p align="right">-Oscar Wilde</p>
</blockquote>

# Welcome

This is a blog about math, data science, and technology.

![Mayer-Vietoris principle in analytic surgery](/resources/phd-diagram.png)

## Latest blog posts

{% assign live = site.posts | where:"tags", "live" %}

<ul>
   {% for post in live limit:5 %}
      <li>
         <a href="{{ post.url }}">{{ post.title }}</a> {{ site[post.level] }} - {{ post.date | date: '%B %d, %Y' }}
         <p>{{ post.abstract }}</p>
      </li>
   {% endfor %}
</ul>
