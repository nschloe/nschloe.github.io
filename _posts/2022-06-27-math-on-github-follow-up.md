---
title: "Math on GitHub: Following up"
date: 2022-07-27
---

About six weeks ago, on May 19, 2022, GitHub released [Math support on
Markdown](https://github.blog/2022-05-19-math-support-in-markdown/). At the
time of the release, it received criticism for being ill-designed and buggy. I,
too, wrote [a blog
post](https://nschloe.github.io/2022/05/20/math-on-github.html) about it.

In the weeks following the release, GitHub have tried to address the issues.
Let's take a look at where math on GitHub stands now.

## Fixed issues

- Kerning

  At the time of the release, kerning between symbols in LaTeX blocks was
  completely off. This was probably a simple math font config bug, and was fixes
  swiftly after release.

  Before:
  <p align="center">
    <img src="/images/kerning-github.png" width="20%">
  </p>

  After:
  <p align="center">
    <img src="/images/kerning-github-fixed.png" width="20%">
  </p>

- Math in lists, tables, headers works now.

  <p align="center">
    <img src="/images/math-lists.png" width="30%">
  </p>

## Markdown vs. math

One, if not _the_ major issue with GitHub's math support is its syntax. They're
using the old familiar `$...$` for inline and `$$...$$` for display math. This
is easy for users but unfortunately does not integrate well with Markdown at
all. Whatever Markdown tools run on a text file, they'll see everything between
`$`-signs as regular text. This means that certain symbols are sanitized away,
other symbols are forcibly `\`-escaped etc. Let's review some of the math that
failed to display correctly right after the release.

- `$\{n\in\mathbb{N}:\: n \text{even}\}$`

  This is still fails to display correctly. The curly brackets are removed, the
  `\:` isn't displayed a space but as a `:`. Workaround: Double-escaping LaTeX
  commands that don't start with a letter:

  $\\{n\in\mathbb{N}:\\: n \text{even}\\}$

  You'd have to go back to singly-escaped brackets and spaces though once the
  issue is fixed. I'm not sure when or _if_ this will happen. Not sure what to
  recommend here.

- `$a <b > c$`

  This still doesn't get rendered as math as `<b >` is recognized as an [HTML opening
  bold tag](https://www.w3schools.com/html/html_formatting.asp).

- `$[a+b](c+d)$`

  Also still doesn't get rendered as math. The problem: `[...](...)` is
  accidentally the syntax for Markdown links. It's recognized as such, and math
  is skipped.

- $`U`$

  Again, the interior is recognized as Markdown code, and math is not rendered.

It seems that GitHub haven't been able to tackle _any_ of the syntactical
issues yet. That's no surprise: Math was added as an afterthought, and if the
Markdown sanitizer is applied first, you'd have to drill that one open. This is
a major endeavor and I'm not sure if GitHub want to go down that path. So far,
they don't.

### `$` vs. dollar bugs

GitHub might have taken inspiration from
[StackExchange](https://math.stackexchange.com/) which also uses `$...$` as
math syntax delimiters. In contrast to StackExchange, though, GitHub wanted
"regular" dollar signs to continue rendering as such. What _is_ a regular
dollar sign though? That's where it gets confusing and, consequently, buggy.

GitHub still renders

```markdown
An apple costs $1, a mango $2.
```

as dollar signs. This, however,

```markdown
An apple costs 1$, a mango 2$.
```

is math. This too:

```markdown
An apple costs $1, a mango $ 2.
```

It seems to be that the terminating `$` is only treated as a LaTeX-toggle if
not immediately followed by an alphanumeric character. If it _is_ followed by
an alphanumeric, the opening `$` is also treated as a regular dollar. It's
unclear to me how it works exactly. Experience shows: The clearer the syntax,
the fewer bugs appear. The syntax here is not clear, so you have plenty of
bugs:

- ```markdown
  $x + $2 $
  ```

  renders as
  <p align="center">
    <img src="/images/x-plus.png" width="10%">
  </p>
  The rest has somehow disappeared.

- `where $T$ is lookback; $\tau$ is horizon.`

  This line is rendered as

  <p align="center">
    <img src="/images/lookback-horizon.png" width="30%">
  </p>

  Whoops! What happened here?

- `$x = \text{my $y$}$`

  <p align="center">
    <img src="/images/y-dollar.png" width="10%">
  </p>

  Dollar signs within LaTeX completely throw off the parser.

- `$x =\$$`

  <p align="center">
    <img src="/images/triple-dollar.png" width="15%">
  </p>

  Although properly escaped, the parser gets confused with the dollar sign.

Note that StackExchange doesn't have those problems: There, `$` toggles math,
always. If you want a regular dollar sign, you'll have to escape it, `\$`.

### The remedy

[As outlined in the previous blog
post](https://nschloe.github.io/2022/05/20/math-on-github.html), and as done by
GitLab and my browser extension [xhub](https://github.com/nschloe/xhub), a
clean fix for all of the above problems is to use a different syntax,
e.g.,

```markdown
$`a + b = c`$
```

for inline math and

````markdown
```math
a + b = c
```
````

for display math. This only looks half-TeXy at first, but it's easy to type
_and_ integrates nicely with Markdown. Thanks to the backticks, indicating to
Markdown parsers that the contents are "code", it's never messed with. There is
no confusion with dollar signs either.
The only thing you can _not_ use in inline math is backtick-dollar without
exiting LaTeX, but that's perhaps something one can live with. 😉
