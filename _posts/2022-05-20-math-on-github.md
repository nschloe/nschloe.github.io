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
It's well-known and popular amongst scientists altough the more modern LaTeX
syntax with `\(...\)` for inline and `\[...\]` for display math is actually
recommended in now.
(See [here](https://tex.stackexchange.com/q/510/13262) for an explanation.)


## The Good

The best thing is that it's finally there. And many things are working well, too.

This


```markdown
#### Cauchy's Theorem

Let $U$ be an open subset of the complex plane $\mathbb{C}$, and suppose the closed
disk $D$ defined as

$$
D = \{z:|z-z_{0}|\leq r\}
$$

is completely contained in $U$. Let $f: U\to\mathbb{C}$ be a holomorphic function,
and let $\gamma$ be the circle, oriented counterclockwise, forming the boundary of
$D$. Then for every $a$ in the interior of $D$,

$$
f(a) = \frac{1}{2\pi i} \oint_{\gamma}\frac{f(z)}{z-a} dz.
$$
```

gives you

![Cauchy's theorem](/images/math-cauchy.png)

You'll notice that not everything is working here:

  - The curly brackets are missing in the first indented code block.
  - The math font is a little small? Particularly the

    > every _a_ in

    towards the end.

Apart from that: Great! This works in README.mds, issues, discussions,
including the _Preview_ tab. Also cool: `\newcommand`s are global, so you can
define a command in one block, and use it everywhere in the page:

```markdown
$$
\newcommand\myexp[1]{e^{#1}}
$$

Inline: $\myexp{i}$

Display:

$$\myexp{i}$$
```

The reason why I'm so excited about this feature is that, in combination with
version control and the issues/discussions capabilities in GitHub, I can see
tectonic changes in how we're publishing science. At last, science can really
reap the benefits of a connected internet by moving away from static PDFs to
living, breathing repositories which _render_ like PDFs and provide a central
place where one can actually talk about the article. -- And fix bugs!

## The Bad

Not everything works. Most notably, there is math in lists or tables. Or GitHub
pages, such as this one.

```markdown
- $E = mc^2$
- $a^2 + b^2 = c^2$
```

![math in lists](/images/math-in-lists.png)

This is hopefully something that GitHub will be able to sort out soon;
particularly the list issue appears easy to fix.

#### MathJax vs KaTeX

Then there their choice of rendering engine,
[MathJax](https://github.com/mathjax/MathJax/). Historically, it's the first
engine that could seriously render math in web pages, and is used in many
places, e.g., [math.stackexchange](https://math.stackexchange.com/).

If you look just a little closer, though, you'll find there's at least one
serious competitor: [KaTeX](https://github.com/KaTeX/KaTeX). It's
- newer,
- has more contributors,
- is [faster](https://www.intmath.com/cg5/katex-mathjax-comparison.php)

- MathJax activity:

  [![mathjax contributors](/images/mathjax-contributors.png)](https://github.com/mathjax/MathJax/graphs/contributors)

- KaTeX activity:

  [![katex contributors](/images/katex-contributors.png)](https://github.com/KaTeX/KaTeX/graphs/contributors)



## The Ugly

MathJax's default font MJXTEX-I and GitHub's default text Helvetica have a
different x-height/cap-height ratio. This means that, if you match the capitals
in height, the small letters won't match. This is what it looks like:

![math font size comparison](/images/math-font-size.png)

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
