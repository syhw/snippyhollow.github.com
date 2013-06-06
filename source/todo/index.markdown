---
layout: page
title: "todo"
date: 2013-06-06 17:18
comments: true
sharing: true
footer: true
---

(CHILDES) Davis corpus:

 * couper ceux avec un timestamp \d\d\d\d\d
 * regarder SNR (et pruner)
 * calculer les formants
 * aligner des fenetres (constant / noyaux vocaliques), ou essayer forced
  alignment
 * demander l'inversion à VLAM ou au modèle de Raphaël
 * synthétiser son ?


Corpus Lalevée (annoté avec places):

 * à partir des annotations (phonétiques) -> dériver places / features
  phonétiques


Pour Thomas:

 * pipe ABX et base Allen avec modèle train par timit:  
    - map TIMIT vers Allen symbols' 
    http://hear.ai.uiuc.edu/public/Corpus/LDC/DOC/LDC_symbols.pdf  
    - posteriogrammes (tester Baum-Welch et Viterbi post) sur la base d'Allen 
    -> scores par taches ABX/ABX-C/ABX-L  
 * port HDF5 wrapper to Python
 * Numenta's HTM benchmark (base Allen?)


HTK pipeline:

 * triphones


Currently:

 * viterbi avec unigram model frequence
    - viterbi vs. HVite  
    - viterbi vs. Baum-Welch (sum instead of max)  
 * tester sur TIMIT sans ENORMALIZE et aussi sans les D / A
 * s/GMM/deep learning (RBM/mcRBM/convolutional net)/
 * HDP-HMM (learning states / phones)


Pour Isabelle:

 * Haghighi et al. unsupervised POS-tagging
 * iHMM POS-tagging
 * our model


Pour Mathilde:

 * correlation bouba-kiki in vision/audio


Pour thèse:

 * écrire article journal
 * updates about review paper?
 * review AIIDE papers


Bayesian Nonparametrics:

 * implem DP K-means Jordan et al.
 * HDP with bootstraping of the latent 
 * boostraping for DP(G)MM clusters
 * Gaussian processes + Bayesian bandits for psycho XPs
 * Bandits, Bandits everywhere


Modeling team journal club:

 * implement VB DPGMM
 * adaptor grammars


Cloverfeed:

 * implem page sharing/commenting
 * implem simple with LDA (proto offline -> online)
 * $P(Read=true\|features, time)$
 * implem double Gibbs LDA (Mimno et al.)
 * distances between words knowing their topics distribs,
 and topics knowing their words distrib => match topics after re-training


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


