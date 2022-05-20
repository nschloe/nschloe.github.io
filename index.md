Hi! This is Nico, big nerd with a math background. I'll write irregularly on
topics that interest me, mostly math and programming (duh).

Here the list of posts:
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

[Here's my GitHub page](http://github.com/nschloe) in case you're in for a deep
dive. You'll also find [the sources of this
blog](https://github.com/nschloe/nschloe.github.io).
