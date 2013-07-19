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
 * Numenta's HTM benchmark (sur base Allen?)


Kaldi pipeline:
 
 * test switchboard s5b local DNN recipee


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

 * correlation BoubaKiki: lancer analysis.py après avoir calculé les
   tilt-shifts après RASTA-PLP au lieu de simple spectrogram
 * faire un modèle machine learning (Random Forest ? ElasticNet ?) sur toutes
   les features (+ sélection features ? PCA ?)


Pour Léo:

 * GP for replacing QUEST and staircases
 * bandits pour online re-calibration


Pour thèse:

 * écrire article journal



Bayesian Nonparametrics:

 * implem DP K-means Jordan et al.
 * HDP with bootstraping of the latent 
 * bootstrapping for DP(G)MM clusters (done, write-up)
 * bootstrapping for DPGMM with different hyper-parameters (and see what sticks)
 * Gaussian processes + Bayesian bandits for psycho XPs
 * Bandits, Bandits everywhere
 * DP-DBN (DP stacked RBM for "phones" clustering and recognition to start with)
 * DPGMM avec 1 classe = mixture de plusieurs gaussiennes ? (comme en classif., i.e. comme en GMM pour HMM en speech recog.)


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


