---
layout: post
title: "phn2vec embeddings"
date: 2014-05-27 21:00
comments: true
categories: [phonetics, phonemic transcription, speech recognition, word2vec, embeddings, gensim]
---

Several months ago, I started thinking in terms of embeddings for everything,
let's forget about discrete/categorical values and replace everything with
vector spaces that behave as we ask of them![^1]

### xyz2vec

A few months ago, I toyed with ["word2vec"](http://radimrehurek.com/gensim/models/word2vec.html) ([Mikolov et al. 2013](http://arxiv.org/pdf/1301.3781.pdf)) in gensim on a lot of stuff. One of them were phonetic annotations of speech corpora. Basically, a word2vec model is a one hidden layer neural network trained with backpropagation of a loss based on either predicting the central word given its neighbors (continuous bag-of-word) or predicting the neighbors given the central word. This can be applied to corpora of continuous text of words, but anything that has neightboring structure really. So I ran word2vec on phonetic and phonemic datasets (TIMIT and Buckeye), with a window of 5 phone(me)s (+/- 2 around the central one) and both skip-grams (SG) and continuous bag-of-words (CBOW). For all the following results, I used an embedding dimension of 10, so it is "contractive" compared to the number of phone(me)s (39). I tried with 100 dimensions and this gave very similar results, so this does not seem to matter. All the code to reproduce these results is [here](https://github.com/SnippyHolloW/speech_embeddings).

## phone2vec

Using [TIMIT](https://catalog.ldc.upenn.edu/LDC93S1) phonetic annotations here are the similarity matrices of phones (SG left and CBOW right):

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_similarity_sg_left_cbow_right.png %}

If we do a 2 dimensional [isomap projection](http://scikit-learn.org/stable/auto_examples/manifold/plot_lle_digits.html) of the skip-grams, we can see 3 clusters of vowels, (mainly) plosives (stop consonants) and other consonants (some fricatives, nasals..).[^2]

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_isomap_sg.png %}

An isomap of the CBOW gives roughly the same clusters:

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_isomap_cbow.png %}

Contrary to the TIMIT corpus (read sentences that were designed for phonetic variability and effects), the [Buckeye](http://buckeyecorpus.osu.edu/) is a corpus of conversational speech. We find the same (but a little bit weaker) clusters, e.g. in an isomap of skip-grams:

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/buckeye_phones_isomap_sg.png %}

## read speech vs. conversational speech

To the extent that the Buckeye and TIMIT corpus have slightly different phonetic annotations (and different annotations quality too), we can try and compare read speech vs. conversational speech. Here we plot the difference of the similarity matrices between one and the other (SG left, CBOW right). The biggest difference is in silences vs. stop-consonants:

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_vs_buckeye_phones_similarity_sg_left_cbow_right.png %}

## phoneme2vec

What about phonemic annotation (phonemes from the word-level transcription)? Here are the similarity matrices (SG left, CBOW right):

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_words_similarity_sg_left_cbow_right.png %}

We still have the clusters of consonants vs vowels in the isomap of the skip-gram:

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_words_isomap_sg.png %}

and of the CBOW

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_words_isomap_cbow.png %}

but we have much lesser distinctions between front and back consonants as well as nasals and stop. That's obvious, because phonotactics are not accounted for in the phonemic transcription that I did.

## phonetic vs. phonemic

We already know that speech (phonetics) and this "higher level" (phonemic) representation differ, how do they differ in this embedding?

{% img https://dl.dropboxusercontent.com/u/14035465/pictures/figures_10dim_5window/timit_phones_vs_words%28phonemes%29_similarity_sg_left_cbow_right.png %}

That's all folks! Currently, I worked only on English datasets. That would be fun to see what comes up for other languages.


[^1]: There are several interesting papers about turning text into emdeddings that have useful properties. Picking a few: from classical nature language processing tasks done only with vector spaces (and neural networks) ([Collobert et al. 2011](http://static.googleusercontent.com/media/research.google.com/fr//pubs/archive/35671.pdf)), to semantic spaces for multi-relational data ([Bordes et al. 2013](http://papers.nips.cc/paper/5071-translating-embeddings-for-modeling-multi-relational-data.pdf)). The recent "word2vec" ([Mikolov et al. 2013](http://arxiv.org/pdf/1301.3781.pdf)) from Google spiked interest from the NLP community, and it quickly got implemented in [gensim](http://radimrehurek.com/gensim/) (if you want to geek out the implementation, I recommend the [excellent blog post about its optimisation](http://radimrehurek.com/2013/09/word2vec-in-python-part-two-optimizing/)).

[^2]: [Wikipedia provides some phonetics 101 of consonants](https://en.wikipedia.org/wiki/Consonant#Features).
