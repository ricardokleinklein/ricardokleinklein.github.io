---
layout: post
title: Speech Synthesis and SampleRNN
author: Ricardo Faúndez-Carrasco
---

Typing things on your keyboard is a rather uncomfortable way to interact with your electronic devices. We would like to be able to have a normal conversation with them instead, as we would talk to any other human being. Researchers from Standford, the University of Washington and Baidu published an study showing an improvement of [about a $$3$$% in saved time](https://arxiv.org/abs/1608.07323) when interacting with mobile devices using voice instead of a keyboard in either English or Chinese. For such a system to be really attractive to the average consumer, we must have both accurate speech recognizers and speech synthetizers capable of uttering perfectly understandable words.

One of the latest models to make a public appearance is [SampleRNN](https://arxiv.org/abs/1612.07837) by Soroush Mehri, Kundan Kumar, Ishaan Gulrajani, Rithesh Kumar, Shubham Jain, Jose Sotelo, Aaron Courville and Yoshua Bengio. This model is directly inspired, and the authors state it explicitly, by *Deepmind*'s [Wavenet](https://arxiv.org/abs/1609.03499), which I shall introduce in another post. In this entry I shall focus on the first, for it is conceptually simpler.

This blog is not intended to be exclusively about speech-related technologies, so many readers may have little or no idea about which are the state of the art models and their working principles. Therefore, we should start with a brief introduction to Speech Synthesis, reviewing the main strategies used until very recently. 

# How is an artificial speech built

# A little survey on RNNs

# Let's review SampleRNN's paper

# Training method proposed by the authors

# My implementation and results

The original code used by the authors is publicly available on Github ([link to the official repository](https://github.com/soroushmehr/sampleRNN_ICLR2017)), though it is written in *Theano*, a framework that is no longer under support. That's why I have developed my own implementation of it in *Tensorflow*. You can access to it [here](https://code-to-sampleRNN).

# Future directions