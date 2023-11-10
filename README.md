<blockquote>
<p style="font-size:x-large">
   "Mathematics knows no races or geographical boundaries; for mathematics, the cultural world is one country."
</p>
<p align="right" style="font-size:larger">
   -David Hilbert
</p>
</blockquote>

## Latest posts

{% assign live = site.posts | where:"tags", "live" %}

<ul style="list-style-type:none">
    {% for post in live limit:5 %}
        {% unless post.categories contains "commentary" %}
            <li>
                <h3>
                    <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
                </h3>
                <p>{{ post.abstract }}</p>
            </li>
        {% endunless %}
    {% endfor %}
</ul>
