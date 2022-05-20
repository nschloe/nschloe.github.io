---
title: "Math on GitHub: The Good, the Bad and the Ugly"
date: 2022-05-20
---

Finally! On May 19, 2022, GitHub released one of its most requested features:
[Math support on
Markdown](https://github.blog/2022-05-19-math-support-in-markdown/)! I myself
had been anticipating this for many years, so much in fact that I wrote a
browser extension to get there early. ([xhub](https://github.com/nschloe/xhub),
it can do a few other things so check it out.)

The syntax GitHub has chosen is the `$`-based TeX syntax.
```latex
Inline math: $a+b$

Display math:
$$
a + b
$$
```
It's well-known and popular amongst scientists altough the more modern LaTeX syntax
is actually recommended in now:
```latex
Inline math: \(a+b\)

Display math:
\[
a + b
\]
```
(See [here](https://tex.stackexchange.com/q/510/13262) for an explanation.)


## The Good

The best thing is that it's finally there. And many things are working well, too.


## The Bad

Not everything works. Most notably, there is math in lists or tables. Or GitHub
pages, such as this one.

![math in lists](images/math-in-lists.png)

This is hopefully something that GitHub will be able to sort out soon;
particularly the list issue appears easy to fix.

#### MathJax vs KaTeX

Then there their choice of rendering engine,
[MathJax](https://github.com/mathjax/MathJax/). Historically, it's the first
engine that could seriously render math in web pages, and is used in many
places, e.g., [math.stackexchange](https://math.stackexchange.com/).

If you look just a little closer, though, you'll find there's at least one
serious competitor, [KaTeX](https://github.com/KaTeX/KaTeX).


## The Ugly

MathJax's default font MJXTEX-I and GitHub's default text Helvetica have a
different x-height/cap-height ratio. This means that, if you match the capitals
in height, the small letters won't match. This is what it looks like:

![math font size comparison](images/math-font-size.png)

Font enthousiasts will also notice that the text font is sans-serif while the
math font has serifs -- a no-go in serious typesetting.

There's probably not too much you can do here. Matching text and math fonts is
a whole branch of science, see, e.g.,
[here](https://tug.org/pracjourn/2006-1/hartke/hartke.pdf).

## Prior art

GitLab https://docs.gitlab.com/ee/user/markdown.html#math

````markdown

```math
E = mc^2
```

And inline math $`a^2 + b^2 = c^2`$

````
