---
layout: post
title: Getting along with DNN's attention mechanisms
author: Ricardo Faúndez-Carrasco
---

The mechanisms that allow computers to perform automatic translations between human languages (such as [Google Translate](https://translate.google.com/?hl=es&tab=TT)) are known under the flag of **Machine Translation** (MT), with most of the current such systems being based on **Neural Networks**, so these models end up under the tag of **Neural Machine Translation**, or *NMT*. The state-of-the-art results are obtained using the most advanced class of neural networks known, which lay under the umbrella of Deep Learning. In this context, researchers from *Google Brain* released a paper this very year showing a new mechanism that could be applied to the problem and that outperforms any other NMT model seen before [^1]. The aim of this post is to develop the ideas presented in this paper, explaning to a wider audience its results, why it is relevant not only in MNT, and last but not least, trying to make a little more accesible some of the most complex parts of the model.

# Highlights of the paper

When reading a scientific paper, the first thing one should figure out is its actual achievements, and whether it is worth it to go through the entire paper or not. So, what are the main points you should remember about this one?

* Introduces a new model, named **Transformer**, which use neither Convolutional Neural Networks[^2] (CNNs) nor Recurrent Neural Networks[^3] (RNNs) in its structure, but just *attention mechanisms*[^4].
* In particular, introduces the concept of **multi-head attention mechanism**.
* It is a __*seq2seq* model__, this is, both the inputs and the outputs of the model are sequences, with no external processing required to discretize them.
* It follows a **classical encoder $$+$$ decoder structure**. The encoder tries to find a proper simplified representation of the input data, and the decoder tries to return the original data from the representation provided by the encoder.
* It is an **autoregressive model**, meaning that every output sequence depends not only on the input, but also on previous outputs.
* Achieves new state-of-the-art results in NMT when using the [WMT 2014 English-to-German](http://www.statmt.org/wmt14/translation-task.html) and [WMT 2014 English-to-French](http://www.statmt.org/wmt14/translation-task.html) datasets[^5].

So quite a lot of things to see here! 

# First you should have these terms clear

This section is intended to be an introduction to those less familiar with the simple attention mechanisms. If you feel confident about that topic, feel free to skip this and go straight to the model itself.

## BLEU score in NMT

It would be crazy to check our automatic translation models using human translators, not only because of the time that would require, but because it wouldn't make any sense. It is reasonable then to find some sort of measure that automatically can evaluate the goodness of our translations. Amidst the sea of such measures, the BLEU score[^6] has became the most widely used one, for it shows a high correlationwith human judgement, and is relatively cheap to compute. The intuition behind is to compare human-generated translations (which are available, since they will be the labels during training time of the translator), with the computer-generated ones. Comparing sequences of words, denoted as [*n-grams*](https://en.wikipedia.org/wiki/N-gram) present in both translations, the closer the artificial one is (ideally absolutely equal) to the human-generated one, the better the translation is. This measure yields a scalar number between $$0$$ and $$1$$, though it is usually regarded as its decimal part, with high values representing better translations.

## Why attention and not CNNs or RNNs?

In short, **autoregressive models imply sequential computations, which are extremely expensive in computational terms**. In order to reduce such costs, models like *ByteNet*[^7] and *ConvS2S*[^8] use CNNs that are easily parallelizable, something that is not possible with RNNs, although they seem to be more suitable for sequence modelling. The drawback is that CNN approaches require a lot of layers to catch long-term dependencies in the sequential structure of the data, not always succeding in the attempt, or making the net so big it ends up being impractical.

The ability of a model to learn dependencies between positions decreases rapidly with distance, making it fundamental to find an approach that can deal in a parallel way with these sequential data.

## Basic attention mechanism

First of all, you must keep in mind that attention mechanisms and structures such as CNNs and RNNs are not mutually exclusive. In fact, all of them can be combined with no perjury. That the paper's authors decided not to do so does not mean you have to do the same.

That said, take a look at the diagram below:

![basic dot-attention mechanism]()

# The Transformer architecture

## 1. Input -- Output

## 2. Positional encodings

## 3. Encoder

### A) Multi-head attention mechanism

### B) Residual connections

### C) Layer normalization

### D) Positio-wise Fully-connected feed-forward nets

## 4. Decoder

### A) Masked causal attention

# Results published in the paper

## Training hyperparameters

# Our results

# Conclusion

# References

[^1]: [Vaswani et al. (2017): "Attention is all you need". arXiv:1706.03762v4](https://arxiv.org/abs/1706.03762)

[^2]: [Goodfellow, I., Bengio, Y. and Courville, A., "Deep Learning", Chapter 9, MIT Press, 2016.](http://www.deeplearningbook.org/)

[^3]: [Goodfellow, I., Bengio, Y. and Courville, A., "Deep Learning", Chapter 10, MIT Press, 2016.](http://www.deeplearningbook.org/)

[^4]: [Bahdanau, D., KyungHyun, C. and Bengio, Y. (2016): "Neural Machine Translation by jointly learning to align and translate". arXiv:1409.0473v7](https://arxiv.org/abs/1409.0473)

[^5]: [EMNLP 2017 Second Conference on Machine Translation's (WMT17) website.](http://www.statmt.org/wmt17/)

[^6]: [Papineni, K. et al. (2002): "BLEU: a Method for Automatic Evaluation of Machine Translation", pp. 311-318, Proceedings of the 40th Annual Meeting of the Association for Computational Linguistics (ACL).](http://citeseer.ist.psu.edu/viewdoc/summary?doi=10.1.1.19.9416)

[^7]: [Kalchbrenner, N. et al. (2017): "Neural Machine Translation in linear time". arXiv:1610.10099](https://arxiv.org/abs/1610.10099)

[^8]: [Gehring et al. (2017): "Convolutional Sequence to Sequence Learning". arXiv:1705.03122](https://arxiv.org/abs/1705.03122)