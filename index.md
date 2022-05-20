# Github Pages Demo Blog

Welcome to this demo blog!

You can find the sources of this project
[here](https://github.com/nschloe/nschloe.github.io).

x
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.permalink }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
y
