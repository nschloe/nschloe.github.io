---
title: "Math on GitHub: Following up"
date: 2022-07-27
---

About six weeks ago, on May 19, 2022, GitHub released [Math support on
Markdown](https://github.blog/2022-05-19-math-support-in-markdown/). At the
time of the release, it received criticism for being ill-designed, buggy, and
generally lacking. I, too, wrote [a blog
post](https://nschloe.github.io/2022/05/20/math-on-github.html) about it.

In the weeks following the release, GitHub have tried to address the upcoming
issues. Let's take a look at where math on GitHub stands now.

## Fixed issues

- Kerning

  At the time of the release, kerning between symbols in LaTeX blocks was
  completely off. This was probably a simple math font config bug, and was fixes
  swiftly after release.

  Before:

  After:

- Math in headers, lists, tables etc. is something that l

## Markdown vs. math

One, if not _the_ major issue with GitHub's math support is its syntax. They're
using the old familar `$...$` for inline and `$$...$$` for display math
support. Unfortunately, this does not integrate well with Markdown at all.
Whatever Markdown tools run on a text file, they'll see everything between
$-signs as regular text. This means that certain symbols are sanitized away,
other symbols are forcibly `\`-escaped etc. Let's review some of the math that
failed to display correctly right after the release.

- `$\{n\in\mathbb{N}:\: n \text{even}\}$`

  This is still fails to display correctly in many ways. The curly brackets are
  removed, the `\:` isn't displayed a space but as a `:`. Workaround:
  Double-escaping those.

  $\\{n\in\mathbb{N}:\\: n \text{even}\\}$

  It's also unclear which commands have to be double-escaped. Perhaps those not
  starting with an alphabetic character.

  Anyway, you'd have to turn that back once the issue is fixed. Not sure if this will
  ever happen though. I'm not sure what to recommend here.

- `$a <b > c$`

  This still doesn't get rendered as math as `<b >` is recognized as an [HTML opening
  bold tag](https://www.w3schools.com/html/html_formatting.asp).

- `$[a+b](c+d)$`

  Also still doesn't get rendered as math. The problem: `[...](...)` is
  accidentally the syntax for Markdown links. It's recognized as such, and math
  is skipped.

- $`U`$

  Again, the interior is recognized as Markdown code, and math is not rendered.

It seems that GitHub haven't been able to tackle any of the syntactical issues
yet. That's no surprise: Math was added as an afterthought, and if the Markdown
sanitizer is applied first, there's not much you can do.

### `$` vs. dollar bugs

GitHub might have taken inspiration from
[StackExchange](https://math.stackexchange.com/) which also uses `$...$` as
math syntax delimiters. In contrast to StackExchange, though, GitHub wanted
"regular" dollar signs to render as such. What _is_ a regular dollar sign
though? That's where it gets confusing and, consequently, buggy.

Choosing a purely `$`-based syntax poses other problems. What about legitimate
uses of `$` in existing Markdown texts? GitHub still renders

```
An apple costs $1, a mango $2.
```

as dollar signs. This

<!--in contrast with stackexchange-->

```
An apple costs 1$, a mango 2$.
```

is math. This too:

```
An apple costs $1, a mango $ 2.
```

is rendered as math though.

It seems to be that the terminating `$` is only treated as such if not
immediately followed by an alphanumeric character. If it _is_ followed by an
alphanumeric, the opening `$` is also not treated as such. It's unclear how it
works exactly.

```
$x + $2 $
```

This gives you an idea of how if-elsy their parser
must look like. It's also not clear to the user which dollar signs get
interpreted as LaTeX-toggle, and which as regular dollar signs.

Experience shows that, the less clear a syntax is, the more
bugs appear. New

- `where $T$ is lookback; $\tau$ is horizon.`

  This line is rendered as

  Whoops! What happened here?

- `$x = \text{my $y$}$`

  Dollar sign's within LaTeX completely

- `$x =\$$`

  The parser gets confused with the dollar signs. Instead of

StackExchange doesn't have those problems: `$` toggles math there, always. If
you want a regular dollar sign, you'll have to escape it, `\$`.

### The remedy

[As outlined in the previous blog
post](https://nschloe.github.io/2022/05/20/math-on-github.html), and as done by
GitLab and my browser extension [xhub](https://github.com/nschloe/xhub), a
clean fix for all of the above problems is to use a different syntax,
e.g.,

```markdown
$`...`$
```

for inline math and

````markdown
```math
...
```
````

for display math. This only looks half-LaTeXy at first, but is easy to type
_and_ integrates nicely with Markdown. Thanks to the backticks, indicating to
Markdown parsers that this is "code", math content is never messed with. There
is no confusion with dollar signs. The only thing you can _not_ use in inline
math is backtick-dollar without exiting LaTeX, but that's perhaps something one
can live with. 😉
