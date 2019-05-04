<blockquote>
<p>*"Everything in moderation, including moderation."*</p>
<p align="center">-Oscar Wilde</p>
</blockquote>

## Latest posts

{% assign live = site.posts | where:"tags", "live" %}

<ul>
   {% for post in live limit:5 %}
      <li>
         <h3>
            <a href="{{ post.url }}">{{ post.title }}</a> {{ site[post.level] }} - {{ post.date | date: '%B %d, %Y' }}
         </h3>
         <p>{{ post.abstract }}</p>
      </li>
   {% endfor %}
</ul>
