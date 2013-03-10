---
layout: post
title: "Collapsed Gibbs Sampling for Dirichlet Process Gaussian Mixture Models"
date: 2013-03-10 15:44
comments: true
categories: [Bayesian, Dirichlet process, mixture models, Gibbs sampling]
---

I really enjoyed the pedagogy of [Edwin Chen's introduction to infinite mixture models](http://blog.echen.me/2012/03/20/infinite-mixture-models-with-nonparametric-bayes-and-the-dirichlet-process/), but I was a little disappointed that it does not go as far as presenting the details of the Dirichlet process Gaussian mixture model (DPGMM), as he uses [sklearn's variational Bayes DPGMM implementation](http://scikit-learn.org/stable/modules/mixture.html#dpgmm-classifier-infinite-gaussian-mixtures). 

For this reason, I will try and give here sufficient information to implement a DPGMM with collapsed Gibbs sampling.

### Prerequisites
On Dirichlet processes, Chinese Restaurant processes, Indian Buffet processes, there is [the excellent blog post by Edwin Chen](http://blog.echen.me/2012/03/20/infinite-mixture-models-with-nonparametric-bayes-and-the-dirichlet-process/). Another excellent introduction to Dirichlet processes is provided by [Frigyik, Kapila and Gupta](http://www.ee.washington.edu/research/guptalab/publications/UWEETR-2010-0006.pdf).

If you lack some knowledge about clustering or density estimation (unsupervised learning), you can read Chapters 20 (p. 284) to (at least) 22 of [MacKay's ITILA](http://www.amazon.com/gp/product/0521642981/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0521642981&linkCode=as2&tag=syhwsblog-20), that you can find as a [free ebook](http://www.inference.phy.cam.ac.uk/itila/book.html); or chapter 9 of [Bishop's PRML](http://www.amazon.com/gp/product/0387310738/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0387310738&linkCode=as2&tag=syhwsblog-20). As a refresher, the [Wikipedia article on mixture models](https://en.wikipedia.org/wiki/Mixture_model), and [the sklearn documentation on GMM](http://scikit-learn.org/stable/modules/mixture.html) are more efficient.

### DPGMM: the model

Let's say we have $N$ observations and $K$ clusters, $i \in [1\dots N]$ is the indice for the observations, while $k \in [1\dots K]$ is the indice for the clusters.

The generative story of a DPGMM is as follows:

$\pi \sim Stick(\alpha)$ (mixing rates)  
$z_i \sim \pi$ (cluster assignments)  
$\theta_k \sim H(\lambda)$ (parameters)  
$x_i \sim F(\theta_{z_i})$ (values)   

So we have:

$$
P(x_{1:N}) = \prod_{i=1}^N \sum_{k=1}^K P(x_i|\theta_{z_i}) P(z_i=k)
$$

### Fitting the data
Notation: 

$$
\begin{align}
& z_{-i} = \{z_j | j \neq i\}\\
& x = x_{1:N}\\
& k^*\mathrm{\ is\ a\ new\ cluster}\\
& x_{-i,c} = \{x_j | z_j = c, j \neq i\}\\
& N_{k,-i} = card(x_{-i,c})
\end{align}
$$

Let's decompose the probability that the observation $i$ belongs to cluster $k$ into its two independent factors:

$$
P(z_i = k | z_{-i}, x, \alpha, \lambda) \propto P(z_i = k | z_{-i}, \alpha)P(x_i | x_{-i}, z_i=k, z_{-i}, \lambda)
$$


$$
\boxed{P(z_i = k | z_{-i}, \alpha) = \left\{ 
\begin{array}{l}
\frac{N_{k,-i}}{\alpha + N - 1} \mathrm{\ if\ }k\ \mathrm{has\ been\ seen\ before}\\
\frac{\alpha}{\alpha + N - 1} \mathrm{\ if\ }k\ \mathrm{is\ a\ new\ cluster}\\
\end{array}
    \right.}
$$

$$
P(x_i | x_{-i}, z_i=k, z_{-i}, \lambda) = P(x_i | x_{-i,k}, \lambda) = \frac{P(x_i, x_{-i, k}, \lambda)}{P(x_{-i,k}|\lambda)}
$$

$$
\boxed{P(x_i, x_{-i, k}, \lambda) = \int P(x_i | \theta_k)\left[\prod_{j \neq i, z_j = k}P(x_j | \theta_k)\right] H(\theta_k | \lambda) d\theta_k}
$$

is the marginal likelihood of all the data assigned to cluster $k$, including $i$.

If $z_i = k^*$ (new cluster) then:

$$
P(x_i | x_{-i}, z_i=k^*, z_{-i}, \lambda) = \boxed{P(x_i | \lambda) = \int P(x_i | \theta)H(\theta | \lambda) d\theta}
$$

### Conjugate priors

Now we should choose $H$ for it to be conjugate to $F$ and have easy to compute parameters posterior. As we want $F$ to be multivariate normal: we can look on [Wikipedia's page of conjugate priors](http://en.wikipedia.org/wiki/Conjugate_prior) under multivariate normal with unknown $\mu$ and $\Sigma$ to see that $H$ should be Normal-inverse-Wishart with prior parameters:

 - $\mu_0$ initial mean guess [In my code further, I set it to the mean of whole the dataset.]
 - $\kappa_0$ mean fraction (smoothing parameter) [I set it to 0.]
 - $\nu_0$ degrees of freedom [I set it to the number of dimensions.]
 - $\Psi_0$ pairwise deviation product (matrix) [I set it to $d\times d$ identity matrix.]

This gives us MAP estimates on parameters:

$$
\begin{align}
& \mu_n = \frac{\kappa_0 \mu_0 + n\tilde{x}}{\kappa_0 +n} = \mu\\
& \kappa_n = \kappa_0 + n\\
& \nu_n = \nu_0 + n \\
& \Psi_n = \Psi_0 + C + \frac{\kappa_0 n}{\kappa_0 + n}(\tilde{x} - \mu_0)(\tilde{x} - \mu_0)^T$\\
& \Sigma = \frac{\kappa_n + 1}{\kappa_n * (\nu_n - d + 1)}\Psi_n
\end{align}
$$

with $\tilde{x}$ the sample mean and $C=\sum_{i=1}^n (x_i-\tilde{x})(x_i-\tilde{x})^T$.

Set $\kappa_{0} = 0$ to have no effect of the prior on the posterior mean. 
This reduces to MLE estimates if 

$$
\kappa_{0} = 0, \nu_{0} = d, \|\Psi_{0}\| = 0
$$

### Collapsed Gibbs sampling

Here is the pseudo-code of collapsed Gibbs sampling adapted from algorithm 3 of [Neal's seminal paper](http://www.stat.purdue.edu/~rdutta/24.PDF):

    while (not converged on mus and sigmas): // or in log-likelihood
        for each i = 1 : N in random order do:
            remove x[i]'s sufficient statistics from old cluster z[i]
            if any cluster is empty, remove it and decrease K
            for each k = 1 : K do
                compute P_k(x[i]) = P(x[i] | x[-i]=k)
                N[k,-i] = dim(x[-i]=k)
                compute P(z[i]=k | z[-i], Data) = N[k,-i] / (alpha + N - 1)
            compute P*(x[i]) = P(x[i] | lambda)
            compute P(z[i]=* | z[-i], Data) = alpha / (alpha + N - 1)
            normalize P(z[i] | ...)
            sample z[i] from P(z[i] | ...)
            add x[i]'s sufficient statistics to new cluster z[i]
            (possibly increase K)

### Code

Here is my quick-and-dirty code implementing this version of Gibbs sampling for DPGMM. You may want to comment out scikit-learn if you do not have it installed (I use [sklearn's VB DPGMM](http://scikit-learn.org/stable/modules/mixture.html#dpgmm-classifier-infinite-gaussian-mixtures) as a comparison).

{% gist 5128969 %}
