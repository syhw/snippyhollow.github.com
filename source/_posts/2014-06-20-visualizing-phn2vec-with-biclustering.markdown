---
layout: post
title: "Visualizing phn2vec with biclustering"
date: 2014-06-20 12:17
comments: true
categories: [phonetics, phonemic transcription, speech recognition, word2vec, embeddings, gensim, biclustering, sklearn]
---

Following [my previous blog post on phn2vec](http://snippyhollow.github.io/blog/2014/05/27/phn2vec-embeddings/), I used [scikit-learn's biclustering](http://scikit-learn.org/stable/modules/biclustering.html) to make the similarity matrices more readable. So here are some quick results for TIMIT:

## Phonetic annotation


### 2 biclusters

We clearly see consonants vs. vowels.

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_2_biclusters.png %}

### 4 biclusters

We clearly see a separation in the place (+nasals) in the consonants. Silences get their own cluster.

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_4_biclusters.png %}

### 6 biclusters

Fricatives and nasals get their own clusters.

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_6_biclusters.png %}
 
## Phonemic transcription

### 2 biclusters

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phonemes_2_biclusters.png %}

### 4 biclusters

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phonemes_4_biclusters.png %}

### 6 biclusters

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phonemes_6_biclusters.png %}

