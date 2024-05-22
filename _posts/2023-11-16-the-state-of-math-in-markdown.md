---
title: "The State of Math in Markdown"
date: 2023-11-16
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
  2022](https://github.blog/changelog/2023-05-08-new-delimiter-syntax-for-inline-mathematical-expressions/).

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

Updated May 2024.[^2]

|  | GitHub `$` (14.4%) | Github `` $` `` (41.3%) | GitLab `$` (58.7%) | GitLab `` $` `` (95.2%) | Gitea `$` (77.9%) | Gitea `\(` (81.7%) | Pandoc `$` (94.2%) |
| :---- | :----: | :----: | :----: | :----: | :----: | :----: | :----: |
| [Basic example](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#basic-example) | [partial](https://github.com/github/markup/issues/1744) | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Subsequent math](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#subsequent-math) | [no](https://github.com/github/markup/issues/1741) | ✓ | [no](https://gitlab.com/gitlab-org/gitlab/-/issues/431890) | ✓ | ✓ | ✓ | ✓ |
| [Indented math](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#indented-math) | ✓ | ✓ | [✓](https://gitlab.com/gitlab-org/gitlab/-/issues/431893) | ✓ | [no](https://github.com/go-gitea/gitea/issues/27834) | [no](https://github.com/go-gitea/gitea/issues/27834) | ✓ |
| [Math in quote blocks](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#math-in-quote-blocks) | [✓](https://github.com/github/markup/issues/1732) | [✓](https://github.com/github/markup/issues/1732) | [✓](https://gitlab.com/gitlab-org/gitlab/-/issues/431889) | ✓ | [partial](https://github.com/go-gitea/gitea/issues/27777) | [partial](https://github.com/go-gitea/gitea/issues/27777) | ✓ |
| [Escaped dollar sign](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#escaped-dollar-sign) | [no](https://github.com/orgs/community/discussions/17116) | ✓ | ✓ | ✓ | [no](https://github.com/go-gitea/gitea/issues/27618) | ✓ | ✓ |
| [Inline and display math in same list](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#inline-and-display-math-in-same-list) | [no](https://github.com/github/markup/issues/1745) | [no](https://github.com/github/markup/issues/1745) | [✓](https://gitlab.com/gitlab-org/gitlab/-/issues/431895) | ✓ | ✓ | ✓ | ✓ |
| [Math in footnotes](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#math-in-footnotes) | [no](https://github.com/orgs/community/discussions/55227) | [no](https://github.com/orgs/community/discussions/55227) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Math in links](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#math-in-links) | [no](https://github.com/orgs/community/discussions/55232) | [no](https://github.com/orgs/community/discussions/55232) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Escaped symbols in math](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#escaped-symbols-in-math) | [no](https://github.com/github/markup/issues/1746) | ✓ | [✓](https://gitlab.com/gitlab-org/gitlab/-/issues/431896) | ✓ | ✓ | ✓ | ✓ |
| [Images and math in the same list](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#images-and-math-in-the-same-list) | [no](https://github.com/github/markup/issues/1743) | [no](https://github.com/github/markup/issues/1743) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Math in `<details>`](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#math-in-details) | [no](https://github.com/orgs/community/discussions/57950) | [no](https://github.com/orgs/community/discussions/57950) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [`<` without surrounding whitespace](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#-without-surrounding-whitespace) | [no](https://github.com/orgs/community/discussions/55225) | [no](https://github.com/orgs/community/discussions/55225) | [✓](https://gitlab.com/gitlab-org/gitlab/-/issues/431897) | ✓ | ✓ | ✓ | ✓ |
| [Inline math preceeded by non-whitespace](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#inline-math-preceeded-by-non-whitespace) | [no](https://github.com/github/markup/issues/1742) | [partial](https://github.com/github/markup/issues/1742) | ✓ | ✓ | [✓](https://github.com/go-gitea/gitea/issues/27605) | [partial](https://github.com/go-gitea/gitea/issues/27605) | ✓ |
| [Inline math succeeded by non-whitespace](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#inline-math-succeeded-by-non-whitespace) | [partial](https://github.com/github/markup/issues/1742) | ✓ | [partial](https://gitlab.com/gitlab-org/gitlab/-/issues/431869) | ✓ | [partial](https://github.com/go-gitea/gitea/issues/27605) | [✓](https://github.com/go-gitea/gitea/issues/27605) | [partial](https://github.com/jgm/pandoc/issues/9192) |
| [Inline math with `%\n`](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#inline-math-with-n) | ✓ | [no](https://github.com/orgs/community/discussions/55237) | [no](https://gitlab.com/gitlab-org/gitlab/-/issues/432102) | [no](https://gitlab.com/gitlab-org/gitlab/-/issues/432102) | [no](https://github.com/go-gitea/gitea/issues/27617) | [no](https://github.com/go-gitea/gitea/issues/27617) | [no](https://github.com/jgm/pandoc/issues/9193) |
| [Comments before bracket delimiters](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#comments-before-bracket-delimiters) | [no](https://github.com/orgs/community/discussions/55228) | [no](https://github.com/orgs/community/discussions/55228) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Sum/product signs in inline mode or fractions](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#sumproduct-signs-in-inline-mode-or-fractions) | [no](https://github.com/orgs/community/discussions/17051) | [no](https://github.com/orgs/community/discussions/17051) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [`\operatorname`](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#operatorname) | [no](https://github.com/orgs/community/discussions/55368) | [no](https://github.com/orgs/community/discussions/55368) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Math in stylized text](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#math-in-stylized-text) | [no](https://github.com/orgs/community/discussions/17264) | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Inline math at the end of stylized text](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#inline-math-at-the-end-of-stylized-text) | [no](https://github.com/orgs/community/discussions/55033) | [no](https://github.com/orgs/community/discussions/55033) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Dollar in `\text`](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#dollar-in-text) | [no](https://github.com/orgs/community/discussions/39655) | ✓ | [no](https://gitlab.com/gitlab-org/gitlab/-/issues/432104) | ✓ | [no](https://github.com/go-gitea/gitea/issues/28070) | [no](https://github.com/go-gitea/gitea/issues/28070) | ✓ |
| [Math vs. HTML mix-ups](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#math-vs-html-mix-ups) | [no](https://github.com/github/markup/issues/1747) | [no](https://github.com/github/markup/issues/1747) | [partial](https://gitlab.com/gitlab-org/gitlab/-/issues/432106) | ✓ | ✓ | ✓ | ✓ |
| [sqrt symbol around fractions](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#sqrt-symbol-around-fractions) | [no](https://github.com/orgs/community/discussions/39251) | [no](https://github.com/orgs/community/discussions/39251) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [Matrix without line breaks](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#matrix-without-line-breaks) | [no](https://github.com/orgs/community/discussions/52991) | ✓ | [✓](https://gitlab.com/gitlab-org/gitlab/-/issues/432108) | ✓ | ✓ | ✓ | ✓ |
| [50 or more colors in a block](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#50-or-more-colors-in-a-block) | [no](https://github.com/orgs/community/discussions/45276) | [no](https://github.com/orgs/community/discussions/45276) | ✓ | ✓ | ✓ | ✓ | ✓ |
| [100 or more bracketed exponents or subscripts](https://github.com/nschloe/markdown-math-acid-test/blob/main/dollar-backtick.md#100-or-more-bracketed-exponents-or-subscripts) | [no](https://github.com/orgs/community/discussions/59960) | [no](https://github.com/orgs/community/discussions/59960) | ✓ | ✓ | ✓ | ✓ | ✓ |

### Comments

[Comments welcome on Hacker
News!](https://news.ycombinator.com/item?id=38292214)


[^1]:
    `$`-toggles are original TeX notation, LaTeX introduced the begin-end
    `\(...\)` notation. It's generally preferable as it's easier to work with from
    a compiler's standpoint, and gives you better info in case of an error. In fact
    the creator of TeX, Donald Knuth, [perfers the begin-end
    syntax.](https://tex.stackexchange.com/questions/510/are-and-preferable-to-dollar-signs-for-math-mode#comment61028_510)

[^2]:
    I will update the samples and table once in a while to keep track of new
    developments.
