---
layout: post
title: Neural Variational Inference for Text Processing
date: '2015-11-28 04:48:57'
---

* Yishu Miao, Lei Yu, Phil Blunsom. Neural Variational Inference for Text Processing. [arXiv preprint arXiv:1511.06038](http://arxiv.org/abs/1511.06038), 2015.

## Summary

Following recent work on variational auto-encoders and their advances in computer vision, the authors propose deep generative models and related inference algorithms for text. The Neural Variational Document Model (NVDM) is an instantiation of the variational auto-encoder for deep latent Gaussian models, where the data input is the bag-of-words representation of the document. There's a cool interpretation of this, as the Gaussian latent variables can be thought of as the embedding of the discrete vocabulary in a real-valued latent space. In the scenario of multiple hidden layers, the deep latent Gaussian model can be seen as sequentially embedding each corresponding value within nested latent spaces.

As a more complex model, they also propose the Neural Answer Selection Model (NASM) for answer sentence selection, in which the task is to identify the correct answers to a question from a set of possible answers. The NASM employs a latent attention model similar to the deep recurrent attentive writer (Gregor et al., 2015) for modelling images. Given a question $$q$$, it attends a set of answer sentences $$~\{a_1,\ldots,a_n\}$$ associated with $$q$$; this in turn determines the context vector, which are the words in the answer sentences that are prominent for predicting the answer matches to the current question. This enables the model to learn subtleties inherent in the questions.

## Discussion

It's a simple idea coming from the variational auto-encoder literature and their recent state-of-the-art advances for unsupervised learning in computer vision. This is well worth studying. Interestingly, there doesn't seem to be any complications when transferring these same ideas to analyzing text.

Confusingly, it doesn't apply neural variational inference from Mnih and Gregor (2014), as the generative model is continuous. Instead it uses the more common tricks standard in variational auto-encoders.

It would have been interesting to study a generative model  of documents which also employs a latent attention architecture. Most immediate in my mind would be avoiding the bag-of-words representation and explicitly modelling the word-to-word sequence of the documents.
