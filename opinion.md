---
layout: default
title: Opinion 
category: opinion
---

# Opinion

Welcome to the opinion section of this blog.

You may notice that there is no link to this page or any of the posts within elsewhere in the site.
This is very much intentional: the rest of the site is dedicated to subjects like mathematics and technology which I believe sit apart from the realm of ordinary cultural and ideological commitments.
The thoughts that I express here are intended to be challenging and provocative, in contrast to the collaborative and educational spirit of the rest of the site.

Whether you find my ideas persuasive or not, I hope you at least find them useful!

<ul>
    {% for post in site.categories[page.category] %}
    <li>
        <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
        <p>{{ post.abstract }}</p>
    </li>
    {% endfor %}
</ul>

