# Math

Each post is marked with a cat face indicating its difficulty level, as per the following table:
* :smile_cat:: Accessible to a general audience; bring only your curiosity and an open mind.
* :crying_cat_face:: General literacy in the subject of the post is assumed, but the technical requirements aren't too demanding.
* :pouting_cat: A working technical knowledge of the subject of the post is required.
* :scream_cat: This post is probably only accessible to practitioners and experts of in a relatively narrow area of study.

<ul>
  {% for post in site.categories.math %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: '%B %d, %Y' }}
      <p>{{ post.abstract }}</p>
    </li>
  {% endfor %}
</ul>
