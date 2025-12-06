---
title: "The State of Math in Markdown"
date: 2023-11-16
last_modified_at: 2024-05-22
---

_TLDR: Markdown + math is hard. GitHub is bad, Gitea better, GitLab best.
Backticked syntax trumps `$`-syntax._

Math input is quite a common Markdown extension in Git services these days.

- The first proper math implementation in Markdown is from
  [Pandoc](https://pandoc.org/) and dates back to at least 2014. Their
  `tex_math_dollars` syntax takes inspiration from TeX:

  ```markdown
  $a + b = c$

  $$
  f(x) = 1
  $$
  ```

  It is [now](https://github.com/jgm/pandoc/pull/9156) also possible to use the
  backticked `tex_math_gfm` syntax:

  ````markdown
  $`a + b = c`$

  ```math
  f(x) = 1
  ```
  ````

  One can choose between [`MathJax`](https://www.mathjax.org/),
  [`KaTeX`](https://katex.org/), and
  [`MathML`](https://developer.mozilla.org/en-US/docs/Web/MathML) as HTML
  renderers.

- Gitea has had math support right from their 1.0 release in 2016. They offer
  TeX (`$`-`$$`) and LaTeX syntaxes[^1]:

  ```markdown
  \(a + b = c\)

  \[
  f(x) = 1
  \]
  ```

- In December 2016, GitLab
  [introduced](https://about.gitlab.com/releases/2016/12/22/gitlab-8-15-released/)
  math support using a backticked-syntax:

  ````markdown
  $`a + b = c`$

  ```math
  f(x) = 1
  ```
  ````

- In May 2022, GitHub
  [introduced](https://github.blog/2022-05-19-math-support-in-markdown/) their
  MathJax-based math, using the `$`-`$$` syntax. After (persisting) problems
  with this, they first
  [added](https://github.blog/changelog/2022-06-28-fenced-block-syntax-for-mathematical-expressions/)
  `math` code blocks in June 2022, and ``$`a + b = c`$`` for inline math in
  [May
  2023](https://github.blog/changelog/2023-05-08-new-delimiter-syntax-for-inline-mathematical-expressions/).

Feeding on bug reports and discussions, I've compiled a list of test cases for
math in Markdown that seem to be easy to get wrong. The difficulty ranges from
seemingly simple math in lists:

```markdown
- $$
  f(x) = 1
  $$
```

to devious "HTML syntax" in math:

```markdown
$$
a <b > c
$$
```

The samples can be compared on:

- [GitHub](https://github.com/nschloe/markdown-math-acid-test)
- [GitLab](https://gitlab.com/nschloe/github-math-bugs)
- [Gitea](https://try.gitea.io/nschloe/markdown-math-acid-test)

[The table below](#comparison-table) gives an overview of what works and what
doesn't.

#### Key Takeaways

- The older an implementation is, the fewer errors are present. Apparently, you
  can't get around this even as a large company such as GitHub whose young math
  implementation performs relatively poorly still.

- The backtick syntax is a great practical choice for online services. They
  outperform other math syntaxes everywhere and GitLab even gets almost all
  test cases right. Using backticks to blend in with Markdown avoids a whole
  array of pitfalls, especially if strict Markdown/HTML sanitizers are present.
  (A problem that Pandoc doesn't have.)

From a math-centric point of view, even better-suited for inline math would be
`` `$...$` `` (the dollar signs _inside_ the "code" block). This allows parsers
to only look at inline code contents for determining if it's math or not. In
general though, you'd probably want to keep allowing actual code that starts
and ends in `$`.

### Comparison table

Updated December 2025.[^2]

|  | GitHub `$` (38.5%) | Github `` $` `` (59.4%) | Github `` $$ `` (42.2%) | Github ```` ```math ```` (59.4%) |
| :---- | :----: | :----: | :----: | :----: |
| [Basic example](https://github.com/nschloe/markdown-math-acid-test/blob/main/01-basic-example.md) | [✓](https://github.com/github/markup/issues/1744) | ✓ | ✓ | ✓ |
| [Consecutive math](https://github.com/nschloe/markdown-math-acid-test/blob/main/02-consecutive-math.md) | [partial](https://github.com/github/markup/issues/1741) | ✓ | ✓ | ✓ |
| [Indented math](https://github.com/nschloe/markdown-math-acid-test/blob/main/03-indented-math.md) | ✓ | ✓ | ✓ | ✓ |
| [Math in quote blocks](https://github.com/nschloe/markdown-math-acid-test/blob/main/04-math-in-quote-blocks.md) | [✓](https://github.com/github/markup/issues/1732) | [✓](https://github.com/github/markup/issues/1732) | ✓ | ✓ |
| [Escaped dollar sign](https://github.com/nschloe/markdown-math-acid-test/blob/main/05-escaped-dollar-sign.md) | [partial](https://github.com/github/markup/issues/17116) | partial | partial | ✓ |
| [Math in footnotes](https://github.com/nschloe/markdown-math-acid-test/blob/main/06-math-in-footnotes.md) | [no](https://github.com/orgs/community/discussions/55227) | [no](https://github.com/orgs/community/discussions/55227) |  |  |
| [Math in links](https://github.com/nschloe/markdown-math-acid-test/blob/main/07-math-in-links.md) | [no](https://github.com/orgs/community/discussions/55232) | [no](https://github.com/orgs/community/discussions/55232) |  |  |
| [Escaped symbols in math](https://github.com/nschloe/markdown-math-acid-test/blob/main/08-escaped-symbols-in-math.md) | [no](https://github.com/orgs/community/discussions/1746) | ✓ | no | ✓ |
| [Math in `<details>`](https://github.com/nschloe/markdown-math-acid-test/blob/main/09-math-in-details.md) | ✓ | ✓ | no | ✓ |
| [`<` without surrounding whitespace](https://github.com/nschloe/markdown-math-acid-test/blob/main/10--without-surrounding-whitespace.md) | [✓](https://github.com/orgs/community/discussions/55225) | [✓](https://github.com/orgs/community/discussions/55225) | no | no |
| [Inline math preceeded by non-whitespace](https://github.com/nschloe/markdown-math-acid-test/blob/main/11-inline-math-preceeded-by-nonwhitespace.md) | [no](https://github.com/github/markup/issues/1742) | [partial](https://github.com/github/markup/issues/1742) |  |  |
| [Inline math succeeded by non-whitespace](https://github.com/nschloe/markdown-math-acid-test/blob/main/12-inline-math-succeeded-by-nonwhitespace.md) | [partial](https://github.com/github/markup/issues/1742) | ✓ |  |  |
| [Inline math with `%\n`](https://github.com/nschloe/markdown-math-acid-test/blob/main/13-inline-math-with-n.md) | ✓ | [no](https://github.com/orgs/community/discussions/55237) |  |  |
| [Comments before bracket delimiters](https://github.com/nschloe/markdown-math-acid-test/blob/main/14-comments-before-bracket-delimiters.md) | [no](https://github.com/orgs/community/discussions/55228) | [no](https://github.com/orgs/community/discussions/55228) |  |  |
| [Small sum/product signs](https://github.com/nschloe/markdown-math-acid-test/blob/main/15-small-sumproduct-signs.md) | [✓](https://github.com/orgs/community/discussions/17051) | [✓](https://github.com/orgs/community/discussions/17051) | ✓ | ✓ |
| [`\operatorname`](https://github.com/nschloe/markdown-math-acid-test/blob/main/16-operatorname.md) | [no](https://github.com/orgs/community/discussions/55368) | [no](https://github.com/orgs/community/discussions/55368) | [no](https://github.com/orgs/community/discussions/55368) | [no](https://github.com/orgs/community/discussions/55368) |
| [Inline math in stylized text](https://github.com/nschloe/markdown-math-acid-test/blob/main/17-inline-math-in-stylized-text.md) | [partial](https://github.com/orgs/community/discussions/17264) | ✓ |  |  |
| [Inline math at the end of stylized text](https://github.com/nschloe/markdown-math-acid-test/blob/main/18-inline-math-at-the-end-of-stylized-text.md) | [partial](https://github.com/orgs/community/discussions/55033) | [partial](https://github.com/orgs/community/discussions/55033) |  |  |
| [Dollar in `\text`](https://github.com/nschloe/markdown-math-acid-test/blob/main/19-dollar-in-text.md) | [no](https://github.com/orgs/community/discussions/39655) | ✓ | ✓ | no |
| [Math vs. HTML mix-ups](https://github.com/nschloe/markdown-math-acid-test/blob/main/20-math-vs-html-mixups.md) | [no](https://github.com/github/markup/issues/1747) | [✓](https://github.com/github/markup/issues/1747) | no | ✓ |
| [sqrt symbol around fractions](https://github.com/nschloe/markdown-math-acid-test/blob/main/21-sqrt-symbol-around-fractions.md) | [no](https://github.com/orgs/community/discussions/39251) | [no](https://github.com/orgs/community/discussions/39251) | [no](https://github.com/orgs/community/discussions/39251) | [no](https://github.com/orgs/community/discussions/39251) |
| [Matrix without line breaks](https://github.com/nschloe/markdown-math-acid-test/blob/main/22-matrix-without-line-breaks.md) | [no](https://github.com/orgs/community/discussions/52991) | partial | no | partial |
| [50 colors in a block](https://github.com/nschloe/markdown-math-acid-test/blob/main/23-50-colors-in-a-block.md) | no | no | [no](https://github.com/orgs/community/discussions/45276) | [no](https://github.com/orgs/community/discussions/45276) |
| [100 bracketed exponents or subscripts](https://github.com/nschloe/markdown-math-acid-test/blob/main/24-100-bracketed-exponents-or-subscripts.md) | [no](https://github.com/orgs/community/discussions/45276) | [no](https://github.com/orgs/community/discussions/45276) | [no](https://github.com/orgs/community/discussions/45276) | [no](https://github.com/orgs/community/discussions/45276) |

### Comments

[Comments welcome on Hacker
News!](https://news.ycombinator.com/item?id=38292214)

[^1]:
    `$`-toggles are original TeX notation, LaTeX introduced the begin-end
    `\(...\)` notation. It's generally preferable as it's easier to work with from
    a compiler's standpoint, and gives you better info in case of an error. In fact
    the creator of TeX, Donald Knuth, [perfers the begin-end
    syntax.](https://tex.stackexchange.com/questions/510/are-and-preferable-to-dollar-signs-for-math-mode#comment61028_510)
