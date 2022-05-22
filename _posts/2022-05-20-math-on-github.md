---
title: "Math on GitHub: The Good, the Bad and the Ugly"
date: 2022-05-20
---

Finally! On May 19, 2022, GitHub released one of its most requested features:
[Math support on
Markdown](https://github.blog/2022-05-19-math-support-in-markdown/)! Many had
been anticipating this for years, including myself, so much in fact that I
wrote a browser extension to get there early.
([xhub](https://github.com/nschloe/xhub), it can do a few other things so check
it out.)

The syntax GitHub has chosen is the familar `$`-based TeX syntax.

```latex
Inline math: $a+b$

Display math:
$$
a + b
$$
```

I'm pointing out below why this may be a problem.

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

- The spacing between the math characters is wrong. (bug report
  [here](https://github.com/github/feedback/discussions/16997))

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

### The syntax

Getting only one of Markdown and LaTeX rendering right is a big challenge.
Mixing them together gets you in all kinds of trouble.

What I think GitHub does now is the following:

1. Render the entire page as Markdown, produce the HTML.
2. Look for `$$...$$`, and determine somehow if they toggle math mode or if
   they should render as dollar signs.
3. Try the same for `$...$` pairs.

This logic brings two fundamental problems.

#### Competing Markdown and math renderer

As part of step 1, the Markdown is "sanitized". This means [the removal of HTML
tags unless they are
allowed](https://github.github.com/gfm/#disallowed-raw-html-extension-), or the
removal of `\` unless they are right before a letter.

The contents of code blocks are unaffected of course, but `$` or `$$` blocks
aren't code blocks as far as Markdown is concerned. This means that, e.g.,

- ```markdown
  $\{n\in\mathbb{N}:\: n \text{even}\}$
  ```

  gets transformed to

  ```markdown
  ${n\in\mathbb{N}:: n \text{even}}$
  ```

  which as math renders like
  <p align="center">
    <img src="/images/math-n-even.png" width="30%">
  </p>

- ```markdown
  &dollar;\frac{f}{a}&dollar;
  ```
  This gets rendered as math because Markdown transforms the `&dollar;` into
  `$`.

#### What is math?

The second step has to apply _some_ heuristic to get right which `$` signs
toggle a math group, and which are just dollar signs. Many mistakes happen
there. This get really tricky if the math block seemingly contains HTML code,
e.g.,

- ```markdown
  $$
  a+b
  <ul><li>c</li></ul>
  $$
  ```

  This gets rendered as HTML, not math.

- ```markdown
  $$
  a + b
  <img/>
  $$
  ```

  This doesn't render at all.

- ```markdown
  $$
  a < b > c
  $$
  ```

  This gets rendered as math. This though

  ```markdown
  $$
  a <b > c
  $$
  ```

  takes `<b >` as an [HTML bold
  tag](https://www.w3schools.com/tags/tag_b.asp), so no math here.

- ```markdown
  $[(a+b)!](c+d)$
  ```
  This gets rendered as a link surrounded by $.

#### Solution

So, how could this be avoided? We need to make sure that the Markdown parser
does not mess with the math. One way of doing this is to drill open the
Markdown parser and and tell it that `$` and `$$` have a special meaning.
This will be pretty tedious.

Another way of achieving this is to use _code_ blocks for math -- their
contents are left untouched by Markdown anyway. In fact, this is how GitLab
handles their [math
support](https://docs.gitlab.com/ee/user/markdown.html#math):

````markdown
Display math:

```math
E = mc^2
```

Inline math: $`a^2 + b^2 = c^2`$.
````

With this syntax, rendering becomes easy:

1. Render the page as Markdown produce the HTML.
2. Check for all `<code syntax="math">` blocks, and render their contents as
   display math.
3. Check for all inline code blocks which are surrounded by `$...$` and render
   their their contents as inline math.

No ambiguity, no stress. Whole classes of bugs simply doesn't exist.

### It doesn't work everywhere

Not everything works. Most notably, there is _no math support_ in

- lists,
- tables,
- headers,

yet.

```markdown
- $E = mc^2$
- $a^2 + b^2 = c^2$
```

![math in lists](/images/math-in-lists.png)

(See [here](https://github.com/github/feedback/discussions/16992) for a
bug report.)

Getting math in lists etc. right is hard because of the syntax GitHub chose.
With GitLab's syntax, math works wherever Markdown code blocks work.

### MathJax vs KaTeX

Then there is their choice of rendering engine,
[MathJax](https://github.com/mathjax/MathJax/). Historically, it's the first
engine that could seriously render math in web pages, and is used in many
places, e.g., [math.stackexchange](https://math.stackexchange.com/). If you
look just a little closer, though, you'll find there's at least one serious
competitor: [KaTeX](https://github.com/KaTeX/KaTeX). In terms of GitHub stars,
it has become way more popular than MathJax:

![mathjax stars](/images/mathjax-stars.png)

Why is that? Having worked with both MathJax and KaTeX in
[xhub](https://github.com/nschloe/xhub), I think the reasons for the popularity
include

- its great community support
- It's [faster](https://www.intmath.com/cg5/katex-mathjax-comparison.php).
- It's smaller/more modular (page load times!)
- You can copy-and-paste math
- Better font support

For one, with KaTeX I've never had problems as described in the _Ugly_ section
below.

_Note:_ In a former version on this article, it was claimed that MathJax is
barely maintained. This is not true.

## The Ugly

### Font sizes

MathJax's default font MJXTEX-I and GitHub's default text Helvetica have a
different x-height/cap-height ratio. This means that, if you match the capitals
in height, the small letters won't match. This is what it looks like:

<p align="center">
  <img src="/images/math-font-size.png" width="30%">
</p>

Font enthusiasts will also notice that the text font is sans-serif while the
math font has serifs -- a no-go in serious typesetting, but forgivable on the web.

There's probably not too much you can do here. Matching text and math fonts is
a whole branch of science, see, e.g.,
[here](https://tug.org/pracjourn/2006-1/hartke/hartke.pdf).

See the bug report
[here](https://github.com/github/feedback/discussions/16999).

### Kerning

[_Kerning_](https://en.wikipedia.org/wiki/Kerning) is a typographist's way of
saying _distance between letters_. Compare the kerning in `a = b` between
GitHub

<p align="center">
  <img src="/images/kerning-github.png" width="20%">
</p>

and LaTeX:

<p align="center">
  <img src="/images/kerning-latex.png" width="20%">
</p>

Clearly, the GitHub kerning is off. This could perhaps be a font issue, or an
issue related to MathJax. (See the bug report
[here](https://github.com/github/feedback/discussions/16997).)

## Concluding thoughts

The reason why I'm so excited about this feature is that, in combination with
version control and the issues/discussions capabilities in GitHub, I can see
tectonic changes in how we're publishing science. At last, science can really
reap the benefits of a connected internet by moving away from static PDFs to
living, breathing repositories which _render_ like PDFs and provide a central
place where one can actually talk about the article. -- And fix bugs!

I'm less enthusiastic about some of the engineering decisions in developing
this product though. The choice of MathJax over KaTeX confuses me.
Since that is more or less a drop-in replacement, perhaps that's a change
that can be made at some point.

A more serious issue is GitHub's approach of (not) trying to consolidate math
and Markdown syntax. _Something_ is working now, but knowing how hard it is to
write a parser I'm pessimistic about how quickly bugs can be fixed and features
added. The fact that math support is only on the top level of the document
speaks for itself.
