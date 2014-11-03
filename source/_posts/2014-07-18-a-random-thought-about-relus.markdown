---
layout: post
title: "A random thought about ReLUs"
date: 2014-07-18 16:59
comments: true
categories: 
---

We know ReLU rock, they're fast to compute, they're fast to converge (train), they combine well with dropout... When I transitioned to ReLU for good, I found out that in phones recognition the "hard sigmoids" (piecewise approximation with 5 pieces) are doing almost as well as ReLUs (e.g. $\approx$24% phone error rate on TIMIT for a given architecture vs 23.5% IIRC), and much better than "smooth" sigmoids (26%). I've been wondering for a few months about how much of the good performance of ReLUs comes from the fact that they have a hard 0, that propagates to the upper layer and makes the higher level activations sparser and sparser. Is it well known? Is this random thought in the bad part of the random walk on the posterior? ;-)
