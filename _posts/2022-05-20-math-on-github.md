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
It's well-known and popular among scientists although the more modern LaTeX
syntax with `\(...\)` for inline and `\[...\]` for display math is actually
recommended in now.
(See [here](https://tex.stackexchange.com/q/510/13262) for an explanation.)


## The Good

The best thing is that _something_ is finally there. This


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

  - The curly brackets are missing in the first indented code block. (bug
    report [here](https://github.com/github/feedback/discussions/16993))
  - The math font is a really small, particularly the

    > every _a_ in

    towards the end.

  - The spacing between the math characters is wrong.

  - The syntax highlighting in the above code block is off.

Apart from that: Great! This works in README.mds, issues, discussions,
including the _Preview_ tab. Also cool: `\newcommand`s are global, so you can
define a command in one block, and use it everywhere in the page:

```markdown
$$
\newcommand\myexp[1]{e^{#1}}
$$

$\myexp{i}$
$$\myexp{i}$$
```

## The Bad

Not everything works. Most notably, there is _no math support_ in

- lists,
- tables
- headers,
- Gists,
- Wikis

yet.

```markdown
- $E = mc^2$
- $a^2 + b^2 = c^2$
```

![math in lists](/images/math-in-lists.png)

(See [here](https://github.com/github/feedback/discussions/16992)) for a
bug report.)

This is hopefully something that GitHub will be able to sort out soon;
particularly the list issue appears easy to fix.

#### MathJax vs KaTeX

Then there their choice of rendering engine,
[MathJax](https://github.com/mathjax/MathJax/). Historically, it's the first
engine that could seriously render math in web pages, and is used in many
places, e.g., [math.stackexchange](https://math.stackexchange.com/).
If you look just a little closer, though, you'll find there's at least one
serious competitor: [KaTeX](https://github.com/KaTeX/KaTeX). Its main
advantage over _MathJax_ is that it isn't dead. Check out the repo activity on the two projects:

- MathJax:

  [![mathjax contributors](/images/mathjax-contributors.png)](https://github.com/mathjax/MathJax/graphs/contributors)

- KaTeX:

  [![katex contributors](/images/katex-contributors.png)](https://github.com/KaTeX/KaTeX/graphs/contributors)

With
[xhub](https://github.com/nschloe/xhub), I've had a great experience with
KaTeX, especially the support.
It's also [faster](https://www.intmath.com/cg5/katex-mathjax-comparison.php)!

No idea why anyone would choose MathJax here.

#### The syntax

Mixing together two syntaxes TeX and Markdown introduces all kinds of
problems.

- What do you think
  ```markdown
  &dollar;\frac{f}{a}&dollar;
  ```
  renders as? That's right, math!

- And this?
  ```
  $[(a+b)!](c+d)$
  ```
  A link surrounded by dollar signs!

Fortunately, the problem of Markdown/math integration has been solved before,
namely [by GitLab](https://docs.gitlab.com/ee/user/markdown.html#math).
They use "code" blocks with `math` syntax for display math, and ``$`...`$`` for
inline math.

````markdown
```math
E = mc^2
```

$`a^2 + b^2 = c^2`$
````

This is a very accessible syntax and ties in  nicely with other Markdown
renderers, too. (For example the syntax highlighter in this very code block.)

Choosing the `$`-approach makes many things hard on the engineering side,
and I assume that missing support for math in lists and headers is a
consequence this.


## The Ugly

#### Font size

MathJax's default font MJXTEX-I and GitHub's default text Helvetica have a
different x-height/cap-height ratio. This means that, if you match the capitals
in height, the small letters won't match. This is what it looks like:

![math font size comparison](/images/math-font-size.png)

Font enthusiasts will also notice that the text font is sans-serif while the
math font has serifs -- a no-go in serious typesetting.

There's probably not too much you can do here. Matching text and math fonts is
a whole branch of science, see, e.g.,
[here](https://tug.org/pracjourn/2006-1/hartke/hartke.pdf).


#### Kerning

_Kerning_ is a typographist's way of saying _distance between letters_. Compare
the kerning in `a = b` between $Github

![github kerning](/images/github-kerning.png)

and LaTeX:

![latex kerning](/images/latex-kerning.png)

Clearly, the GitHub kerning is off. This could perhaps be a  font issue, or an
issue related to MathJax.

## Concluding thoughts

The reason why I'm so excited about this feature is that, in combination with
version control and the issues/discussions capabilities in GitHub, I can see
tectonic changes in how we're publishing science. At last, science can really
reap the benefits of a connected internet by moving away from static PDFs to
living, breathing repositories which _render_ like PDFs and provide a central
place where one can actually talk about the article. -- And fix bugs!

I'm less enthusiastic about some of the engineering decisions in developing
this product though. Particularly the choice of MathJax over KaTeX confuses me.
Since that is more or less a drop-in replacement, perhaps that can be fixed at
some point.

A more serious issue is the decision against GitLab's Markdown math syntax.
