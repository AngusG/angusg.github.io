---
layout: default
title:  "ELBO Surgery"
date:   2019-01-23 01:59:00-0400
categories: writing
---

{% include mathjax_config %}

# Introduction

Recently, {% cite hoffman2016elbo --file blog-article %} proposed an elegant
decomposition of the popular *evidence lower bound*, or ELBO, objective commonly
used when training unsupervised latent variable models characterized by a
probabilistic decoder $p(x | z)$ and prior $p(z)$ on latent
variables $z$. The model has the form:

\begin{equation}
p_{\theta}(x) = \int p_{\theta} (x | z) p(z) dz
\end{equation}

A common way of writing the ELBO objective is like,

\begin{equation}
L({\theta}, {\phi}) = \frac{1}{N} \sum_{n=1}^{N} \mathbb{E}_{q(z_n | x_n)}[\log (p(x_n|z_n))] - KL[q(z_n | x_n) \parallel p(z_n)]
\end{equation}

the __average term-by-term reconstruction minus the KL divergence to the prior__.

\begin{equation}
\frac{1}{N} \sum_{n=1}^{N} KL[q(z | z_n) \parallel p(z_n)] = D_{KL}(q(z) \parallel p(z)) + log(N) - \mathbb{E}_{q(z)}[H[q(n|z)]]
\end{equation}

Notice that $\log(N) - \mathbb{E}_{q(z)}[H[q(n\|z)]]$, looks an awful lot like
the mutual information of the form $I(n;z)=H(n)-H(n\|z)$, where we're treating
the indices $n$ to the $N$ input samples $X_N$ as a random variable.

Thus, we have:

\begin{equation}
 = D_{KL}(q(z) \parallel p(z)) + I(n;z)
\end{equation}

## Derivation

Using $p(z \| n) = p(z)$.

\begin{equation}
\frac{1}{N} \sum_{n=1}^{N} KL(q(z_n \| z_n) \parallel p(z_n)) = \sum_n q(n, z) \log \frac{q(n, z)}{p(n, z)}
\end{equation}

$$ = \sum_n q(n) q(z\|n) \log \frac{q(z) q(n \| z)}{p(n) p(z)} $$

Now, splitting up the log

$$ = \sum_n q(n) q(z\|n) \bigg( \log \frac{q(z)}{p(z)} + \log \frac{q(n | z)}{p(n)} \bigg)$$

$$ = \sum_n q(n) q(z\|n) \bigg( \log \frac{q(z)}{p(z)} + \log \frac{q(n | z)}{p(n)} \bigg)$$

\begin{equation}
 = KL(q(z) \parallel p(z)) + \mathbb{E}_{q(z)}[KL(q(n | z) \parallel p(n))]
\end{equation}

With Bayes rule $q(n)q(z\|n) = q(z)q(n\|z)$, and expanding some terms

$$ = KL(q(z) \parallel p(z)) + \sum_n q(z)q(n | z) \bigg( \log \frac{q(n | z)}{p(n)} \bigg)$$

$$ = KL(q(z) \parallel p(z)) + \sum_n q(z) \underbrace{q(n | z) \log q(n | z)}_\text{-ve entropy term}
  \underbrace{- \sum_n \log \frac{1}{p(n)}}_\text{entropy of n}$$

\begin{equation}
 = KL(q(z) \parallel p(z)) + (\log N - \mathbb{E}_{q(z)}[H(q(n|z))]
\end{equation}

And then it was shown that there is a formal limitation
{% cite -mcallester2018formal --file blog-article %}

# References

{% bibliography --file blog-article --cited %}

<!--![iou-vs-xent]({{site.url}}/img/vgg6xs-fc6-k1-512-deconv-k64s32-iou-vs-xent.png)-->
