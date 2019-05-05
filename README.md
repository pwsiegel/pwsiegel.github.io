<blockquote>
<p style="font-size:x-large">
   "Mathematics knows no races or geographical boundaries; for mathematics, the cultural world is one country."
</p>
<p align="center" style="font-size:larger">
   -David Hilbert
</p>
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
