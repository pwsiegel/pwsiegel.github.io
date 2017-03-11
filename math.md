# Math

Each post is marked with a cat face indicating its difficulty level, as per the following table:
* {{ site.easy }}: Accessible to a general audience; bring only your curiosity and an open mind.
* {{ site.medium }}: General literacy in the subject of the post is assumed, but the technical requirements aren't too demanding.
* {{ site.hard }}: A working technical knowledge of the subject of the post is required.
* {{ site.expert }}: This post is probably only accessible to practitioners and experts of in a relatively narrow area of study.

---

<ul>
  {% for post in site.categories.math %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
      <p>{{ post.abstract }}</p>
    </li>
  {% endfor %}
</ul>
