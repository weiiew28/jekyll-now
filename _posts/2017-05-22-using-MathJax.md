---
layout: post
title: How to type equations in jekyll
mathjax: true
---

I found this [post](http://sgeos.github.io/github/jekyll/2016/08/21/adding_mathjax_to_a_jekyll_github_pages_blog.html) gives me a very neat explanation on how you should modify you jekyll to display mathematical equations. It boils down to 2 steps:

-  create **\_includes/mathjax.html**: insert javascript into the html, and specify some configurations. According to [MathJax documents](http://docs.mathjax.org/en/latest/tex.html), the tex2jax preprocessor defines the LaTeX math delimiters,\\(...\\) for inline math, and \\[...\\] for displayed equations. It also supports \$\$...\$\$ for displayed equations. However, it does not support \$...\$ to avoid confusion with its other usages. To resolve this issue, you can enable that in your mathjax configuration (MathJax.Hub.Config) in the **\_includes/mathjax.html**. It is announced recently that The MathJax CDN hosted at cdn.mathjax.org will be shutting down on April 30, 2017, and it is recommend to change the link to the following "https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML".
-  include the above **\_includes/mathjax.html** in **\_layout/defaults.html**. You can add **{% include mathjax.html %}** directly after the \<html\> tag.

Let us give it a try!

* mathematical symbols: $\mathbb{R}^{n}$, $$\mathbb{E}$$
* Greek letters: $$ \alpha $$, $$ \sigma $$, $$ \Epsilon $$, $$ \epsilon $$
* Equations: $$ \int_{x} \frac{1}{\sqrt{2\pi}}\exp(-\frac{x^2}{2}) dx = 1 $$

---
