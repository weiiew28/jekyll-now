---
layout: post
title: Learning a Distribuiton
---

One of the measures to quantify the discrepancy between two continuous distributions $P$ and $Q$ is called the (Kullback Leibler) **KL** divergence, defined as following:

$$KL(P||Q)=\int_{x} P(x) \log \frac{P(x)}{Q(x)} dx $$

If both distributions are discrete, then we will replace the integral with summation. Using Jensen's inequality, we can show that 

$$ KL(P||Q)\geq 0 $$ 

and it is exactly $0$ if and only if $P$ agrees with $Q$ almost surely. However, KL divergence is not a metric, because it is not symmetric in general. However, The triangle-like inequality still holds, simply because the function: $a \to a \log a$ and the function: $a \to -\log a$ are both convex. It follows that:

$KL(\frac{1}{2}P_{1}+\frac{1}{2}P_{2}||Q)  \leq  \frac{1}{2} K(P_{1}||Q)+\frac{1}{2}K(P_{2}||Q)$

and 

 $KL(P||(\frac{1}{2}Q_{1}+\frac{1}{2}Q_{2})) \leq \frac{1}{2} K(P||Q_{1}) + \frac{1}{2} K(P||Q_{2})$
  
 Now suppose we want to learn a distribution $P$, and we have a learning model $Q$, possibly parametrized by some parameters $w$. One of the things we could try is to minimize the KL divergence between $P$ and $Q$ because at optimal solution: $Q=P$ a.s when the learning model has sufficient expressiveness. This will be casted as the following optimization problem: 
 
$\min_{\omega} KL(P||Q(w)) = \max_{\omega} \int_{x} P(X) \log Q(w,x) dx$   (1)
 
 When we only have finitely many data points $x_{1},x_{2},\cdots, x_{n}$, the above integral can be approximated using a Monte Carlo sum: 
 
$\frac{1}{n} \sum_{i=1}^{n} \log Q(w,x_i)$. 

This is exactly the maximum likelihood approach we have learned in the statistics class. For example, estimating the mean and variance for a Gaussian distribution $G(\mu,\sigma^2)$  or a binomial distribution $B(n,p)$. The only prior assumption we have here is the modelling hypothesis space.  One could imagine that the more complex model we have in the hypothesis space, the smaller KL divergence we will have. Another direct consequence is that suppose the ground truth model $\omega_*$ indeed lies in our hypothesis space, then $\omega_*$ is guaranteed to be the global minimizer of the above optimization problem (1).

Let us try to understand some limitations about the above approach. 
One observation is that if at some point $x$, $P(x)$ is very large and $Q(w,x)$ is very small, this will make the KL divergence very large. However, for other local modes for $P$, the KL divergence will not be penalized much as the peak mode if it is missed. My understanding is that MLE could be good at learning the high modes of the distribution, but not necessarily the multimodality of a distribution. Moreover, it is also very sensitive to the outlier if the number of data points is small. 

One might ask, is it possible to minimize $KL(Q(w)||P)$? The learned distribution $Q(w)$ will have support within the support $P$, otherwise, the KL divergence will explode. However, we can not use the Monte Carlo sum here as before, because the data point is drawn from $P$. Even if we use importance sampling, 

$\int Q(w,x) \log {Q(w,x) }/{ P(x) } dx$

$\approx \frac{1}{n} \sum_{i=1}^{n} {Q(w,x_i)}/{P(x_{i})} \log {Q(w,x_{i})}/{P(x_{i})}$

we have no idea about the value $P(x)$. Therefore, it is kind of surprising that it is possible to optimize another type of divergence, called Jenson Shannon Divergence, which has the merit of the both, using Generative Adversarial Network (**GAN**). 

The Jensen-Shannon Divergence is defined as: 

$JS(P||Q)=\frac{1}{2}KL(P||M)+\frac{1}{2}KL(Q||M)$

where $M=\frac{1}{2}(P+Q)$. 

One can think of $M$ as a mixture model of $P$ and $Q$, where the data could come from both sources with equal probility. 

**GAN**  has two key components, one is called the generator $G$, and the other is called the discriminator $D$. The discriminator $D$ is to decide if an example is from the true natural distribution $P$ or the fake generated distribution $Q$, while the generator $G$ is trying to fool the discriminator to make mistakes. One can imagine when making the decision, $D$ always provides the confidence that a particular data point is from the true distribution $P$.  For example, if $D(x)=0.8$, then it will report that $x$ is from distribution $P$ with probability $80\%$.

Now suppose we are supplying a stream of data points generated from $M$. The probability that $D$ is correct when $x$ is from distribution $P$ is: $D(x)$ and the probability that $D$ is correct when $x$ is from distribution $Q$ is $1-D(x)$. Therefore the log probability that $D$ is correct is the following:

$\frac{1}{2} \mathbb{E}_{x \sim P} \log D(x)+ \frac{1}{2} \mathbb{E}_{x \sim Q} \log (1-D(x))$ 

and the min max formulation to capture the objective of the discriminator and generator is: 

$\min_{Q}\max_{D}\frac{1}{2} \mathbb{E}_{x \sim P} \log D(x)+ \frac{1}{2} \mathbb{E}_{x \sim Q}\log (1-D(x))$

Indeed, in view from the optimization problem, $D$ is optimized to distinguish between true and fake data points and $Q$ is optimized to fool the generator (in other work, to make generator make mistakes). 

Given $Q$, the optimal solution for $D$ is the following (obtained by taking derivative and setting the derivative to be $0$):

$D(x)={P(x)}/{(P(x)+Q(x))}$ 

and the objective function with the above optimal choice of $D$ becomes 

$\min_{Q} \frac{1}{2}KL(P|| \frac{1}{2}(P+Q)) + \frac{1}{2}KL(Q||\frac{1}{2}(P+Q)) -log 2$

This is exactly the JS divergence modulo some absolute constant. Therefore, at equilibrium, the optimal $Q$ is equal to $P$ almost surely. GAN is working theoretically! Moreover, it bridges and gap between minmimizing $KL(P||Q)$ and $KL(Q||P)$.

From the above construction, it gives us an inspiration that maybe we can find a way (general methodology) to construct discriminators that will learn other measuring functions that will quantify the discrepancy between distributions better and also captures the  multimodality of the distributions! 


However, we have not talked about any actual optimization procedures of the above formulation. For example: are there any efficient algorithms? Does the algorithm guarantee the solution converging to the true distribution? so on and so forth.  We just start to appreciate the idea of GAN as a very first step. 

Reference:

-   Goodfellow, Ian, et al. "Generative adversarial nets." Advances in neural information processing systems. 2014.
