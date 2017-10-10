---
layout: post
title: Some elementary observations for learning distribution
---

One of the measures to quantify the discrepancy between two continuous distributions $P$ and $Q$ is called the KL divergence, defined as following:
\[
KL(P||Q)=\int_{x} P(x)\log \frac{P(x)}{Q(x)}
\]
If both distributions are discrete, then we will replace the integral with summation. Using Jensen's inequality, we get that $KL(P||Q)\geq 0$ and it is exactly $0$ if and only if $P$ agrees with $Q$ almost surely. However, KL divergence is not a metric, because it is not symmetric in general. The triangle-like inequality still holds, because $a \to a\log a$ and $a\to -\log a$ are both convex and we will have that
\[
KL(\frac{1}{2}P_{1}+\frac{1}{2}P_{2}||Q) \leq \frac{1}{2} K(P_{1}||Q)+\frac{1}{2}K(P_{2}||Q) 
\]
\[
KL(P||(\frac{1}{2}Q_{1}+\frac{1}{2}Q_{2})) \leq \frac{1}{2} K(P||Q_{1}) + \frac{1}{2} K(P||Q_{2})
\]
Now suppose we want to learn a distribution $P$, and we have a learning model $Q$, possibly parametrized by some parameters $w$. One of the things we could try is to minimize the KL divergence between $P$ and $Q$ because at optimal solution: $Q=P$ a.s when the learning model has sufficient expressiveness. This will be casted as the following optimization problem:
\[
\min KL(P||Q(w)) \leftrightarrow  \max \int_{x} P(X)\log Q(w,x)
\]
When we have finitely many data points $x_{1},x_{2},\cdots, x_{n}$, the above integral can be approximated using a Monte Carlo sum: $\frac{1}{n}\sum_{i=1}^{n} \log Q(w,x)$. It is seen that this is exactly the maximum likelihood approach.  \\
To study the limitation of MLE approach is thus equivalent to studying the limitation of minimizing $ KL(P||Q(w))$. One observation is that if $P(x)$ is very large and $Q(w,x)$ is very small, this will make the KL divergence very large. However, for other local modes for $P$, the KL divergence will not be penalized much as the peak mode if it is missed. My understanding is that MLE could be good at learning the large modes of the distribution, but not necessarily the multimodality of a distribution. Moreover, it is also very sensitive to the outlier if the data is scarce. \\
At this point, one might ask, is it possible to minimize $KL(Q(w)||P)$? The learned distribution $Q(w)$ will have support within the support $P$, otherwise, the KL divergence will explode. However, we can not use the Monte Carlo sum here, because the data point is drawn from $P$. Even if we use importance sampling, 
\[
\int \frac{Q(w,x)}{P(x)}\log\frac{Q(w,x)}{P(x)} P(x) dx \approx \frac{1}{n} \sum_{i=1}^{n} \frac{Q(w,x_i)}{P(x_{i})}\log\frac{Q(w,x_{i})}{P(x_{i})}
\]
we have no idea of the value $P(x)$. Therefore, it is kind of surprising that it is possible to optimize another type of divergence, called Jenson Shannon Divergence, which has the merit of the both, using Generative Adversarial Network (GAN). The Jensen-Shannon Divergence is defined as:
\[
JS(P||Q)=\frac{1}{2}KL(P||M)+\frac{1}{2}KL(Q||M)
\]
where $M=\frac{1}{2}(P+Q)$.
 \\
GAN has two components, the generator $G$, and the discriminator $D$. The discriminator $D$ is trying to decide if an example is from the true natural distribution $P$ or the fake generated distribution $Q$, while the generator $G$ is trying to fool the discriminator to make mistakes. In particular, we are supplying a stream of data points, and each of them comes from either true distribution $P$ or the generated distribution $Q$ with equal probability. The probability that $D$ is correct when $x$ is from $P$ is:
\[
D(x)
\]
and the probability that $D$ is correct when $x$ is from $Q$ is 
\[
1-D(x)
\]
Therefore the log probability that $D$ is right is 
\[
\frac{1}{2}\mathbb{E}_{x\sim P}\log D(x)+ \frac{1}{2}\mathbb{E}_{x\sim Q}\log (1-D(x))
\]
and the min max formulation is:
\begin{align*}
\min_{Q}\max_{D}\frac{1}{2}\mathbb{E}_{x\sim P}\log D(x)+ \frac{1}{2}\mathbb{E}_{x\sim Q}\log (1-D(x))
\end{align*}
Given $Q$, the optimal solution for $D$ is 
\[
D(x)=\frac{P(x)}{P(x)+Q(x)}
\]
and the above objective function with the optimal choice of $D$ becomes
\[
\min_{Q}  \frac{1}{2}KL(P||\frac{1}{2}(P+Q)) + \frac{1}{2}KL(Q||\frac{1}{2}(P+Q)) -log 2 \leftrightarrow \min_{Q} JS(P||Q)
\]
Therefore, at equilibrium, the optimal $Q$ is equal to $P$ almost surely.  From the above construction, it gives us an inspiration that maybe we can find a way to construct discriminator that will learn other measuring functions to quantify the discrepancy between distributions, and this is the topic we will pursue further shortly.

* Goodfellow, Ian, et al. "Generative adversarial nets." Advances in neural information processing systems. 2014.

----
****
