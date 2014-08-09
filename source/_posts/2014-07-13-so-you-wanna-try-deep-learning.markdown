---
layout: post
title: "So you wanna try Deep Learning?"
date: 2014-08-09 13:37
comments: true
categories: [deeplearning, machinelearning]
---

I'm keeping this post quick and dirty, but at least it's out there. The gist of this post is that [I put out a one file gist that does all the basics](8a0f820261926e2f41cc), so that you can play around with it yourself. First of all, I would say that deep learning is simply kernel machines whose kernel we learn. That's gross but that's not totally false. Second of all, there is nothing magical about deep learning, just that we can efficiently train (GPUs, clusters) large models (millions of weights, billions if you want to make a Wired headline) on large datasets (millions of images, thousands of hours of speech, more if you're GOOG/FB/AAPL/MSFT/NSA). I think a good part of the success of deep learning comes from the fact that practitionners are not affraid to go around beautiful mathematical principles to have their model work on whatever dataset and whatever task. But I disgress...

## What is a deep neural network?

A series of matrix multiplications and non-linearities. You take your input $x$ in your features space, multiply it by a matrix $W$ (add biases $b$), apply a non-linearity (Rectified Linear Unit is fashionable these days, that's $max(0, output)$, but $sigmoid$ and $tanh$ are OK too) and keep on doing that with other layers until you reach a classifier. For instance, you have a 3 layers ReLUs-based neural network with a softmax classifier on top? That gives:

$$y = softmax(max(0, W_2.(max(0, W_1.(max(0, W_0.x + b_0))+ b_1)) + b_2))$$

There are all sorts of different mammals, with very strong specificities, but I think I just described a rat (or is it an [euarchontoglires](https://en.wikipedia.org/wiki/Euarchontoglires)?).

## Links and Papers

I'm just dumping here a collection of links that I think everybody with an interest in deep learning should at least skim:

 - First, you should of course start with [the deeplearning.net tutorials](http://deeplearning.net/tutorial/), even though it's pretty old. Overall, these are very good foundations nevertheless.
 - If you want to get an intuition for [how NNs fold space with non-linearities](http://colah.github.io/posts/2014-03-NN-Manifolds-Topology/) and [an online demo to play around with this concept](http://cs.stanford.edu/people/karpathy/convnetjs/demo/classify2d.html).
 -  These online demos were nice, right? They're done by a guy who also wrote a pretty interesting [personnal history that concurs with my point-of-view on feature learning](http://karpathy.github.io/2014/07/03/feature-learning-escapades/).
 - I'm going to advise you against it in a bit, but if you want to do RBM pre-training, [this paper is a must-read](https://www.cs.toronto.edu/~hinton/absps/guideTR.pdf)
 - If you want to do anything that has to deal with images, start [here](http://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) and [there](http://arxiv.org/pdf/1311.2901.pdf).
 - If you want to do anything that has to deal with speech (I assume you know about speech coding, [otherwise I did a crash course](http://i.imgur.com/fA0QIQr.png)), start [here](http://www.cs.utoronto.ca/~gdahl/papers/dbnPhoneRec.pdf) and [there](http://www.csri.utoronto.ca/~hinton/absps/googlerectified.pdf).
 - If you want to do NLP with deep learning, there are lots of hot papers right now, but you could start with [NLP (almost) from scratch](http://leon.bottou.org/publications/pdf/jmlr-2011.pdf).
 - In any case, you should learn [practical stuff about SGD (must-read)](http://research.microsoft.com/pubs/192769/tricks-2012.pdf), [learn about momentum](http://machinelearning.wustl.edu/mlpapers/paper_files/icml2013_sutskever13.pdf), and you can geek out about extensions (I'm fond of [Adadelta](http://arxiv.org/pdf/1212.5701.pdf)). You should learn about [Dropout](http://arxiv.org/pdf/1207.0580.pdf), and maybe geek out about the variants (fast dropout, dropconnect...).
 - If you like videos, [Optimization I](https://www.youtube.com/watch?v=6WeyTUnbwQQ) and [Leon (1)](http://www.youtube.com/embed/cXzGpiUcvRI?vq=hd1080&autoplay=1) [Bottou's (2)](http://www.youtube.com/embed/4-hTxJAwr8U?vq=hd1080&autoplay=1) [MLSS class (3)](http://www.youtube.com/embed/adXwym8Lakg?vq=hd1080&autoplay=1) are good introductions.
 - Finally, if you want more, you can have a look at my [non-extensive collection of links on deep learning](https://pinboard.in/search/u:syhw?query=deeplearning). 

## Stuff you'll learn

There I'm getting totally subjective, because I'm telling you stuff that I learned the hard way.

#### Generic

 - Always answer "Do you want more data?" with "Yes, please."
 - If something feels wrong, check your gradients with finite differences.
 - For all gradient descent related stuff, first [RTFM](http://research.microsoft.com/pubs/192769/tricks-2012.pdf).
 - When do we stop the training? Almost everybody does it but nobody speaks about it: [early stopping on a validation set](https://en.wikipedia.org/wiki/Early_stopping).
 - If you use $tanh$ or $sigmoid$ activation units, [initialize them well](http://machinelearning.wustl.edu/mlpapers/paper_files/AISTATS2010_GlorotB10.pdf), respectively with uniform weights in $$[-\sqrt{\frac{6}{\mathrm{fan}_{in} + \mathrm{fan}_{out}}}, \sqrt{\frac{6}{\mathrm{fan}_{in} + \mathrm{fan}_{out}}}]$$ or $4$ times that.

#### Unsupervised Pre-Training

 - "What is unsupervised pre-training?" Using un-annotated data to initialize the network's 
 - What is unsupervised pre-training doing? ["unsupervised pre-training guides the learning towards basins of attraction of minima that are better in terms of the underlying data distribution; the evidence from these results supports a regularization explanation for the effect of pre-training."](http://jmlr.org/papers/volume11/erhan10a/erhan10a.pdf)
 - This is not needed if you have enough data.

#### Dropout

 - "How do we approach a problem with the deep learning mindset?" You design an under-constrained over-capacity over-fitting hog (by being deep and wide, just barely tractable efficiently on your hardware), and you keep it in check by using Dropout.
 - "What is Dropout?" Dropping hidden units randomly (usually with a binomial probability of 0.5) during training so that the networks learns to be "robust" and doesn't learn stupid co-activations of units (a way to tell the network to not just learn to compress the training set).
 - "What is Dropout doing exactly?" ["the dropout regularizer is first-order equivalent to an L2 regularizer applied after scaling the features by an estimate of the inverse diagonal Fisher information matrix"](http://papers.nips.cc/paper/4882-dropout-training-as-adaptive-regularization.pdf), ["Dropout performs gradient descent on-line with respect to both the training examples and the ensemble of all possible subnetworks. (...) The regularization term is the usual weight decay or Gaussian prior term based on the square of the weights to prevent overfitting. Dropout provides immediately the magnitude of the regularization term which is scaled by the inputs and by the variance of the dropout variables."](http://papers.nips.cc/paper/4878-understanding-dropout.pdf)
 - "Sorry, what?" You know about [L2 regularization](https://en.wikipedia.org/wiki/Tikhonov_regularization) right? So you know about [this picture](http://ej.iop.org/images/1741-2552/9/5/056002/Full/jne427232f9_online.jpg), where regularization means inflating the L2 (or L1) ball until it intersects your feasible set. Now imagine an ellipsis that has its moments matching the ones of the inverse of the Fisher information matrix of the data. You now have a picture of "kinda" what Dropout is doing.

## Practice

I'd advise to start by using either [Torch](http://torch.ch/) (Lua) or [Theano](http://deeplearning.net/software/theano/) (Python), both nice libraries that do automatic differentiation.

I put together a [single file simple deep neural network working on small datasets (Python)](https://gist.github.com/SnippyHolloW/8a0f820261926e2f41cc), more for pedagogical purposes than production ready, but it runs relatively fast on GPUs thanks to Theano. So if you want to run it, install Theano (I use the [bleeding edge](http://deeplearning.net/software/theano/install.html#bleeding-edge-install-instructions)). If you want to play around with it, look for `TODO` in the code and change values there. [There are several datasets](https://gist.github.com/SnippyHolloW/8a0f820261926e2f41cc#file-dnn-py-L567-L573) that you can use. Also, you should play around with the parameters of [this function](https://gist.github.com/SnippyHolloW/8a0f820261926e2f41cc#file-dnn-py-L575-L577), and maybe try against the SVMs from scikit-learn. Finally, if you use Dropout, you will see improvement only on large-enough networks (> 1000 units / layer, > 3-4 layers). Here is the result on running this file (`python dnn.py`) with a small ($784\times200\times200\times10$) ReLU-based L2-regularized network on MNIST:

{% img http://i.imgur.com/M3COTRE.png %}

## Conclusion

I didn't talk about convolutional neural networks, nor recurrent neural networks, nor other beasts. That should be the next step for the passionate reader. This was just a primer on raw facts for basic deep learning. Depending on what people want, I can either explain function by function the file that I provided here, talk about different loss functions (learning embeddings, e.g. as [word2vec](https://code.google.com/p/word2vec/)), recurrent neural nets, etc.

