---
layout: post
title: How to type equations in jekyll
---

I found this [post](http://sgeos.github.io/github/jekyll/2016/08/21/adding_mathjax_to_a_jekyll_github_pages_blog.html) gives me a very neat explanation on how you should modify you jekyll to display mathematical equations. It boils down to 2 steps:

1.  create **\_includes/mathjax.html**: it inserts javascript into the html, and specifys some Latex configurations. According to [MathJax documents](http://docs.mathjax.org/en/latest/tex.html), the tex2jax preprocessor defines the LaTeX math delimiters: \\ \(...\\ \) for inline math, and \\ \[...\\ \] for displayed equations. It also supports a pair of double dollar sign for displayed equations. However, it does not support a pair of single dollar sign in order to avoid confusion with its other usages. To resolve this issue, you will need to enable the single pair dollar sign in your mathjax configuration in the **\_includes/mathjax.html**. The code is the following:

{% highlight html linenos %}

<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
    tex2jax: {
       inlineMath: [ ['$','$'], ["\\(","\\)"] ],
       displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
       processEscapes: true
    },
    TeX: {Macros:{subscript:['_{#1}',1],superscript:['^{#1}',1]}}
  });
</script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS-MML_HTMLorMML" 
        type="text/javascript"
>
</script>
{% endhighlight %}

 - Remark: The MathJax CDN hosted at cdn.mathjax.org was down on April 30, 2017, and this is the reason we change the above link to the following "https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML\_HTMLorMML".

2. include the above **\_includes/mathjax.html** in **\_layouts/defaults.html**: add the code \{\% include mathjax.html \%\} directly after the \<html\> tag in **\_layouts/defaults.html**.

Now let us give it a try!

* mathematical symbols: $\mathbb{R}^{n}$, $\mathbb{E}$, $\mathbb{P}$
* Greek letters: $\alpha$, $\sigma$, $\gamma$, $\epsilon$
* Equations: $ \int_{x} \frac{1}{\sqrt{2\pi}}\exp(-\frac{x^2}{2}) dx = 1 $
* cases: $$f(x)=\begin{cases} 0  &  x\leq 0   \\\\   x & x>0 \end{cases} $$
* underbrace: $x_{t+1}=x_{t}-\gamma_{t} \underbrace{\nabla f(x_{t})}_{\text{Gradient}}$
* Label equations: \\[ D_{KL}(p||q)=\int p(x) \log \frac{p(x)}{q(x)}dx \\]
* Table: $$ \begin{array}{|c|c|c|} \hline & \text{Attr1} & \text{Attr2} \\\\ \text{ins1} & 1 & 2  \\\\  \hline  \text{ins2} & 3 & 4  \\\\  \hline \end{array} $$

---
