---
layout: page
title: "todo"
date: 2013-06-06 17:18
comments: true
sharing: true
footer: true
---

Pour Thomas:

 * pipe ABX et base Allen avec modèle train par timit:  
    - <s>map TIMIT vers Allen symbols' http://hear.ai.uiuc.edu/public/Corpus/LDC/DOC/LDC_symbols.pdf </s> [our map](http://cogito.ens.fr/dokuwiki/doku.php?id=science:teams:models:translatephoneticsymbols)
    - posteriogrammes (tester Baum-Welch et Viterbi post) sur la base d'Allen 
    -> scores par taches ABX/ABX-C/ABX-L  
 * <s>port HDF5 wrapper to Python</s> [PyTables](http://www.pytables.org/moin)
 * DTW implem pour ABX 2.0


Currently:

 * viterbi avec unigram model frequence
    - viterbi vs. HVite  
    - viterbi vs. Baum-Welch (sum instead of max)  
 * tester sur TIMIT sans ENORMALIZE et aussi sans les D / A
 * s/GMM/deep learning (RBM/mcRBM/convolutional net)/
 * HDP-HMM (learning states / phones)


Pour Isabelle:

 * <s>Haghighi et al. unsupervised POS-tagging</s>
 * iHMM POS-tagging
 * our model


Pour Mathilde:

 * correlation bouba-kiki in vision/audio


Pour thèse:

 * écrire article journal


Bayesian Nonparametrics:

 * implem DP K-means Jordan et al.
 * HDP with bootstraping of the latent 
 * boostraping for DP(G)MM clusters
 * Gaussian processes + Bayesian bandits for psycho XPs
 * Bandits, Bandits everywhere


Modeling team journal club:

 * implement VB DPGMM
 * implement adaptor grammars


Cloverfeed:

 * implem page sharing/commenting
 * implem simple with LDA (proto offline -> online)
 * $P(Read=true\|features, time)$
 * implem double Gibbs LDA (Mimno et al.)
 * distances between words knowing their topics distribs,
 and topics knowing their words distrib => match topics after re-training
 * smart shell (reading stdout + simple Laplace smoothing learning)
 * smart calendar
 * online smart email client (using gmail API)


Probabilistic programming:

 * port Laplace (ProBT 0.1) to Clojure
 * investigate pyMC


Perso:

 * finish and publish PhotoResto
 * simple (Google) contact sharing 
 * finish and publish blog posts (see drafts)
 * photos (dvp)
 * MGEN
 * lire


Update this TODO


