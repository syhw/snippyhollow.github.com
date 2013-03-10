---
layout: post
title: "From hacks to Bayesian probability"
date: 2013-03-08 18:58
comments: true
categories: [Bayesian, logic, hacks]
---

In which we look at two pragmatic hacks that lead to the Bayesian theory of probabilities, when pushed further and added as constraints.

## Coinflips

Let's say we have a coin, and we want to decide if it's fair. We throw it $N$ times and we get $m$ heads, we can code heads=1, tails=0. With $\mu$ the ratio of heads:

$$ 
P(m | N, \mu) = Binomial(m|N,\mu) = \binom{N}{m} \mu^m (1-\mu)^{N-m}
$$

### Maximum likelihood
How do we set $\mu$? We could maximize the probability of the data that we saw under our model, that is maximizing the _likelihood_. Let's say that $D = {x_1 \dots x_N}$, then we have:

$$
P(D|\mu) = \prod_{n=1}^N P(x_n|\mu) = \prod_{n=1}^N \mu^{x_n}(1-\mu)^{1-x_n}
$$

The maximum of this function of $\mu$ is reached for $\mu= \frac{m}{N}$. The problem arises if we have little data (in fact, when we have data that does not cover the whole space of possible data). If $D=(1,1,1)$, the maximum likelihood estimate of $\mu$ will be $1.0$. It means that we predict that _all_ the tosses will land on heads, after only three observations!

### Smoothing

A classical hack is to smooth the maximum likelihood estimate by adding "fake data", we could consider that we already saw the coin land on heads and tails once, before getting our data. This way, before ("prior to") the experiment, we would have $\mu=1/2=0.5$. After (_posterior_ to) our experiment, taking the data into account, we would have $\mu = (3+1)/(3+2) = 0.8$. How do we set the these prior coin flips (smoothing parameters)?

### Maximum A Posteriori

The _right way_ to encode this prior knowledge is to put a probability distribution on the parameter $\mu$. As $\mu$ is a ratio, we should have a continuous distribution on $[0, 1]$ that can represent a whole range of prior belief on what the coin's ratio of heads is. For these reasons, a sensible choice is the Beta distribution:

$$
Beta(\mu|a, b) = \frac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)} \mu^{a-1}(1-\mu)^{b-1}
$$

On Wikpedia, we can check how the $Beta(x\|\alpha, \beta)$ distribution looks like:

![Plots of the Beta distribution](http://upload.wikimedia.org/wikipedia/commons/thumb/f/f3/Beta_distribution_pdf.svg/639px-Beta_distribution_pdf.svg.png)

Now we can compute again what is the _posterior_ value of $\mu$ knowing the data $D$ and the prior Beta ($\propto$ means "proportional to"):

$$
\begin{align}
P(\mu | a_0, b_0, D) & \propto P(D|\mu)P(\mu|a_0, b_0)\\
                    & \propto \left( \prod_{n=1}^N \mu^{x_n} (1-\mu)^{1-x_n} \right) Beta(\mu | a_0, b_0)
\end{align}
$$

Hopefully, the Beta distribution is the conjugate prior for the Bernouilli and binomial distributions, and thus a bit of calculus reduces it to:

$$
P(\mu | a_0, b_0, D) \propto Beta(\mu|a_N, b_N)\\
a_N = a_0 + m\\
b_N = b_0 + (N-m)
$$

We can compute that, when $N \rightarrow \infty$, the expectation of $\mu$: $\mathbb{E}[\mu] = \mu_{ML}$, as:

$$
\mathbb{E} [\mu | a_0, b_0, D] = \frac{a_N}{b_N}
$$

### First conclusion

This approach of using a prior on the parameters of the distributions that are essential to our model (the predicting distribution) is central to the Bayesian approach of building models. It makes the model robust to what can happen, even though we had few data. It makes it easier to reason about our prior assumptions that simply "adding unseen data", and it yields in the presence of more data.

If you're interested about Bayesian modeling, there are plenty of very good textbooks. My prefered gradual introduction is [MacKay's ITILA](http://www.amazon.com/gp/product/0521642981/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0521642981&linkCode=as2&tag=syhwsblog-20), that you can find as a [free ebook](http://www.inference.phy.cam.ac.uk/itila/book.html).


## Causality

Now here is another hack for logical reasoning, that leads to Bayesian probabilities. Let's say that you want to express that an event $A$ entails an event $B$, in logic you would write $A \Rightarrow B$. We will be abusing the notation $A=[A=true]$ and $\neg A=[A=false]$. Now with the [_modus ponens_](http://en.wikipedia.org/wiki/Modus_ponens), you can deduce $B$ whenever $A$ is true.

$$
\frac{[A\Rightarrow B] \wedge A}{B}
$$

### Plausible reasoning

Now, we want to extend prepositional logic to _plausible reasoning_, in which we can have degrees of probability that rules are true; or degrees of belief in these rules and facts. A pragmatic way to do that is to introduce the variable $C$ which represents $A \Rightarrow B$, that is: if $P(C)=p$, there is a probability $p$ that $A \Rightarrow B$. Then, this previous _modus ponens_ translates to:

$$
P(B|A,C) = \frac{P(A|B,C)P(B|C)}{P(A|C)}\ (Bayes'\ theorem)\\
P(B|A,C)=\frac{P(A,B|C)}{P(A|C)}\ (Product\ rule)
$$

And actually, as $P(A,B\|C)=P(A\|C)$, we have $P(B\|A,C)=1$, which corresponds to the strong syllogism of _modus ponens_. 

So now, if we are only 80% sure of $C$, we can write $P(C) = 0.8$ and seek for $P(B\|A)$ (we are 100% sure of A):

$$
\begin{align}
P(B|A) = \frac{\sum_{C\in\{false,true\}} P(B|A,C)P(A)P(C)}{P(A)} & = P(\neg C)P(B|A,\neg C) + P(C)P(B|A,C)\\
& = 0.2*x(\in [0,1]) + 0.8*1.0 \geq 0.8
\end{align}
$$

Which means that $B$ has 80% chances to be true by following the strong syllogism of modus ponens, but it can also be true even though $C=false$.

Finally, contrary to prepositional logic, we _also_ get the weak syllogism (and I'll let you think it through):

$$
\frac{[A\Rightarrow B] \wedge A}{A\ becomes\ more\ plausible}
$$

A similar derivation and observation can be done for [_modus tollens_](http://en.wikipedia.org/wiki/Modus_tollens).

### Cox-Jaynes theorem

A reasoning mechanism needs to be consistent (one cannot prove $A$ and $\neg A$ at the same time). For plausible reasoning, consistency means: a) all the possible ways to reach a conclusion leads to the same result, b) information cannot be ignored, c) two equal states of knowledge have the same plausibilities. Adding consistency to plausible reasoning leads to [Cox’s theorem](http://en.wikipedia.org/wiki/Cox's_theorem), which derives the laws of probability (the product-rule and the sum-rule). So, the degrees of belief of any consistent induction mechanism verify Kolmogorov’s axioms.


### Second and last conclusion

With plausible reasoning, we get all the benefits of prepositional logic, but we can also reason with/about facts and rules that are not 100% true. We have another example of how a pragmatical (sensical) hack to extend logic to "degrees of beliefs" (probabilities) leads to Bayesian probabilities. 

If you are interested by learning about plausible reasonning, you can [look at my thesis](http://emotion.inrialpes.fr/people/synnaeve/phdthesis/phdthesis.html#x1-590003.2), or, better yet, read it directly from one of the masters in [Jayne's Probability Theory: The Logic of Science](http://www.amazon.com/gp/product/0521592712/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0521592712&linkCode=as2&tag=syhwsblog-20) for which the pre-print is [there](http://www-biba.inrialpes.fr/Jaynes/prob.html).


