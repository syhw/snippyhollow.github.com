---
layout: post
title: "Adaptor Grammars"
date: 2013-03-19 10:25
comments: true
categories: 
---

Adaptor grammars are a [quite recent](http://mailserver.cog.brown.edu/~mj/papers/JohnsonGriffithsGoldwater06AdaptorGrammars.pdf) nonparametric extension of PCFGs, which also can implement (discrete) hierarchial Dirichlet processes, with several applications in NLP already.

### Probabilistic Context Free Grammars

A [_CFG_](https://en.wikipedia.org/wiki/Context-free_grammar) is a quadruple $(N, W, R, S)$ with:

 - $N$ a set of nonterminal symbols, e.g. (for the grammar of English): NP (noun phrase), VP (verb phrase).
 - $W$ a set of terminal symbols, e.g. "cat", "dog", "ate".
 - $R$ a set of rules $A \rightarrow \beta$, with $A \in N$ and $\beta \in (N \cup W)$, e.g. $S \rightarrow NP\ VP;$ $NP \rightarrow the\ NN;$ $NN \rightarrow cat\ \|\ dog; \dots$
 - $S$ the start symbol $S \in N$.

A [_PCFG_](https://en.wikipedia.org/wiki/Stochastic_context-free_grammar) (sometimes also stochastic context-free grammar), is a quintuple $(N, W, R, S, \theta)$ which adds probabilities $\theta$ on all the rules: $\theta_{A \rightarrow \beta}$ is the probability that $A$ expands into $\beta$.

The set of all $$\theta_{A \rightarrow b}$$ defines a distribution $G_A$ over trees that have $A$ as a root (noted $T_A$). That is, if when we are in $A$ we can generate subtrees $$t_1 \dots t_n$$ with root $A$, the distribution on these subtrees is $$\prod_{i=1}^n G_i(t_i)$$: the $t_i$ are generated indepently (of $$t_{j \neq i}$$) from $G_i$. That is the assumption that is relaxed by adaptor grammars.

### Pitman-Yor Process

The Pitman-Yor (PY) process is a simple extension of the [Chinese restaurant process (CRP)](https://en.wikipedia.org/wiki/Chinese_restaurant_process) (the CRP is equivalent to the Dirichlet process, that we used previously in the [DPGMM](http://snippyhollow.github.com/blog/2013/03/10/collapsed-gibbs-sampling-for-dirichlet-process-gaussian-mixture-models/)). 

Actually, similarly as the CRP maps to the Dirichlet process, the PY process maps to the Poisson-Dirichlet process. The CRP has only one concentration parameter $\alpha$ and creating new tables with probability $\frac{\alpha}{\alpha + N - 1}$. So for the CRP with $\delta_k$ the Kronecker delta function (Dirac distribution), $N$ datapoints, $M$ clusters, and $N_k$ datapoints in cluster $k$, we have:

$$
z_{n+1} | z_1 \dots z_n \sim \frac{\alpha}{\alpha + N - 1}\delta_{M+1} + \sum_{k=1}^M \frac{N_{k}}{\alpha + N - 1}\delta_k
$$

The PY process has two parameters $a$ and $b$, $b$ playing the role of $\alpha$ (we get backt to the CRP is $a=0$), the probability of creating a new table is now $\frac{M.a + b}{N + b}$, so we have:

$$
z_{n+1} | z_1 \dots z_n \sim \frac{M.a + b}{N + b} \delta_{M+1} + \sum_{k=1}^M \frac{N_{k} - a}{N + b}\delta_k
$$


### Bayesian Nonparametrics as Caching

Now we will make a small detour to pinpoint the parallel between Bayesian non-parametrics (I'm thinking about the CRP and the PY process) and "caching" (keeping frequently accessed items where they can be quickly accessed). Take for instance the problem of learning the grammar of English from a [Treebank](http://en.wikipedia.org/wiki/Treebank) (POS-tags annotated tree) dataset.

The PCFG generative story to build a grammar: for all inputs, repeat:

 - Set $$root(rule) = S$$,
 - Pick a rule according to $P(rule \| root(rule))$ and expands it,
 - Recursively pick rules (as above) and expand them until all leaves are terminal symbols (words).

Notice that no context is used in the rule selection. 

Sure we can do better by adding context for the rule selection, but how? A good idea is to limit context to rules that we used so far. To quote Kevin Knight: "clearly this story of re-use is pervasive in natural language. Ask anyone named Zipf." 

So let's re-use the rules that we already used successfully. Here is the Bayesian nonparametrics tree substitution grammar (TSG) generative story, repeat:

 - Set $$root(rule) = S$$,
 - Pick a rule according to $P(rule \| root(rule), counts\ of\ rules\ used)$ and expands it,
 - Recursively pick rules (as above) and expand them until all leaves are terminal symbols (words).

How do we set-up $P(rule \| root(rule), counts\ of\ rules\ used)$? We can simply mix between the rule probability and the rule uses history with a $\beta$ parameter:

$$
P(rule | root(rule), rules\ use\ history) = \beta P_0(rule | root(rule)) \\
+ (1-\beta) \frac{count(rule)}{\sum_{r \in rules\ with\ same\ root\ as\ rule} count(r\in cache)}
$$

All the same for adaptor grammars: as a PCFG can only learn the probability to for the rewrite $VP \rightarrow VB PP$, an adaptor grammar can learn ("cache") the probability for a specific production $VP \rightarrow (VB climb) (P up) (NP)$.

### Adaptor Grammars

An adaptor C is a function which transforms a distribution into another with the same support. An adaptor grammar is a sextuple $(N, W, R, S, \theta, C)$, where $(N, W, R, S, \theta)$ is a PCFG and C is a vector of adaptors (indexed by N). 

For any rule $A \in N$, $C_A$ maps a distribution over trees $T_A$ to another distribution over the same trees.

### Pitman-Yor Adaptor Grammars

### Hierarchical Dirichlet Processes



Stay tuned for algorithms and for an implementation description.
