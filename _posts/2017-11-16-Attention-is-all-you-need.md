---
layout: post
title: Attention is all you need's review
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

It would be crazy to check our automatic translation models using human translators, not only because of the time that would require, but because it wouldn't make any sense. It is reasonable then to find some sort of measure that automatically can evaluate the goodness of our translations. Amidst the sea of such measures, the BLEU score[^6] has become the most widely used one, for it shows a high correlation with human judgement, and is relatively cheap to compute. The intuition behind is to compare human-generated translations (which are available, since they will be the labels of the translator), with the computer-generated ones. Comparing sequences of words, denoted as [*n-grams*](https://en.wikipedia.org/wiki/N-gram) present in both translations, the closer the artificial one is (ideally absolutely equal) to the human-generated one, the better the translation is. This measure yields a scalar number between $$0$$ and $$1$$, though it is usually regarded as its decimal part, with higher values representing better translations.

## Why attention and not CNNs or RNNs?

In short, **autoregressive models imply sequential computations, which are extremely expensive in computational terms**. In order to reduce such costs, models like *ByteNet*[^7] and *ConvS2S*[^8] use CNNs that are easily parallelizable, something that is not possible with RNNs, although they seem to be more suitable for sequence modelling. The drawback is that CNN approaches require a lot of layers to catch long-term dependencies in the sequential structure of the data, not always succeding in the attempt, or making the net so big it ends up being impractical.

The ability of a model to learn dependencies between positions decreases rapidly with distance, making it fundamental to find an approach that can deal in a parallel way with these sequential data.

## Basic attention mechanism

First of all, you must keep in mind that attention mechanisms and structures such as CNNs and RNNs are not mutually exclusive. In fact, all of them can be combined with no perjury. That the paper's authors decided not to do so does not mean you have to do the same.

That said, take a look at the diagram below, which shows the basic structure of an attention cell:

![basic dot-attention mechanism](/images/attention/attention_dot_product.png){: .center-image }

There's a lot of information in there:
1. Blocks denoted by *A* denote the input sequence. As said, *A*s blocks can be connected in case we are using a RNN. In the general case, though, it is not a requirement. Blocks denoted by letter *B* are completely analogous, but represent the output sequence.
2. Notice the autoregressive behaviour of this architecture. To produce the output at the second position, we consider not only the input sequence but also the first output.
3. There's a processing pipeline of the output and input signals before the second output block is fed. That accounts for the non-linearities and time-dependencies of data, pretty much like RNNs do.

The equations describing this mechanism are:

$$
e_{i,u} = < \Phi(s_i), \Psi(h_u) >
$$

$$
\alpha_{i,u} = \frac{\exp(e_{i,u})}{\sum_{u'} \exp(e_{i,u'})}
$$

$$
c_i = \sum_{u} \alpha_{i,u}h_u
$$

where $$i$$ stands for the $$i$$-th decoder's timestep and $$u$$ is the $$u$$-th encoder's. The latter two equations should be clear for everyone (they're a softmax layer computing probabilities and a weighted sum layer). Their task is to make a weighted sum to addres the issue of knowing which positions are more relevant when trying to predict next position's output. The first equation, though, is a bit more complex; the relationship between the signal $$\Phi(s_i)$$ and $$\Psi(h_u)$$ can be expressed as a simple element-wise product (**dot-product attention**), or as the outcome of a neural network (**additive attention**). The complexity of computing $$e_{i,u}$$ depends on the implementation, but it's just a modelling of the correlation between them.

When reading Google's papers including attention mechanism, you'll probably see diagrams like the following:

![Google's attention diagram](/images/attention/google_dot_product.png){: .center-image }

At first I didn't understand it, but if we consider that

$$
Q \rightarrow \Phi(s_i)
$$

$$
K \rightarrow \Psi(h_u)
$$

$$
V \rightarrow h_u
$$

so they express the dot-product attention as

$$
\text{Attention}(Q,K,V) = \text{softmax}\left( Q K^T \right) V
$$

which in the present paper is scaled to assure that gradients don't get too small during training time[^1], so it takes the final form (right model of the figure above):

$$
\text{Attention}(Q,K,V) = \text{softmax}\left( \frac{Q K^T}{\sqrt{d_k}} \right) V
$$

where $$Q$$, $$K$$ and $$V$$ stand for *Query*, *Key* and *Value* respectively, and $$d_k$$ is the dimension of the queries and keys. In light of this, it looks pretty much the same as the previous figure, right?

In this naïve approach to attention (naïve because we are not considering it in combination with any other DL structure), the ouput is generated by **looking at the entire sequence of inputs plus previous outputs**. But, what if all the variables required (queries, keys and values) come from the same sequence? When that happens, it is said to be a **self-attention layer**.

# The Transformer architecture

Very much alike most NMT models, Transformer is based on an **encoder-decoder structure**[^9]. The difference between it and any other model is that Transformer is entirely based on attention mechanisms and point-wise, fully connected layers for both the encoder and the decoder. Both modules are thus cheaper in computational terms than any other competitor with similar test scores.

Next is a figure from the paper, showing the whole model. We'll go through it piece by piece.

![Transformer's architecture](/images/attention/transformer.png){: .center-image }

## 1. Input -- Output

Input and output are made up by sentence pairs encoded using a *byte-pair* encoding[^9], so the input to Transformer is a sequence of words splitted in sub-units denominated *tokens*, with outputs (which in training time act as labels) being completely analogous.

Following this procedure, we have passed from text to a tokenized version of the words, to finally obtain sequences of vectors, with each vector having a size $$d_{model} = 512$$ and representing a given token. Thus a whole sentence is now a matrix of real numbers.

## 2. Positional encodings

Introduced for the first time by Gehring *et al.*[^8], it's a way to deal with sequential data when not recurrency or temporal information is available due to the lack of RNNs or CNNs.

Let's say that the raw input sequence can be described as $$\mathbf{x} = (x_1, x_2, \cdots, x_m)$$, and that it's converted into an embedded representation $$\textbf{w} = (w_1, w_2, \cdots, w_m)$$ with $$w_j \in \mathbb{R}^f$$. Each $$w_j$$ is a column vector of the matrix embedding belonging to the space $$\mathbb{R}^{V\times f}$$, with $$V$$ the number of embeddings and $$f$$ the number of features of each embedding.

The keypoint is to add some sequential structure to the embeddings. To achieve that, we add up to the original matrix in an element-wise way a vector $$\mathbf{p} = (p_1, p_2, \cdots, p_m)$$, with $$p_j \in \mathbb{R}^f$$.

The resulting vector is $$\mathbf{e} = (w_1 + p_1, w_2 + p_2, \dots, w_m + p_m)$$, that implicitly has a way to tell the neural net that there's an underlying sequential structure.

In the paper, the function chosen to play that role might seem a bit arbitrary, but it's not. They use a combination of sinusoidal functions, namely the sine and cosine:

$$
PE(pos, 2i) = \sin(pos/10^{4 \cdot 2i / d_{model}})
$$

$$
PE(pos, 2i + 1) = \cos(pos/10^{4 \cdot 2i/ d_{model}})
$$

with $$d_{model} = 512$$, the size of the embeddings, and the dimensionality we are going to work with all along the model. $$pos$$ represents a word within the whole sentence, and $$i$$ denotes each feature. The figure below is a crude visualization of the $$PE$$ values that are added to the original input embeddings across a set of words. 

![Sinusoidal positional encodings](/images/attention/positional_encodings.png){: .center-image }

As can be seen, the frequency of the sinusoidal signals changes across the dimensions, so feature $$i=1$$ has a smaller period than $$i=204$$. This is the only mention to any sequential structure in the data fed to Transformer.

## 3. Encoder

![Transformer's encoder](/images/attention/encoder.png){: .center-image }

The encoder is made up of a stack of $$N=6$$ layers, with each of them being composed of two sub-layers: a *multi-head attention mechanism*[^1] and a *fully-connected feed forward net*[^10], plus *residual connections*[^11] on both stages, followed by a *layer normalization*[^12] procedure. Thus the output of every layer in the encoder can be expressed as:

$$
y = \text{LayerNorm}\left[x + \text{FFN}\left[\text{LayerNorm}\left[x + \text{MultiHead}\left(Q,K,V\right)\right]\right]\right]
$$

* Every layer in the encoder has a dimension equal to $$d_{model} =512$$.
* Only the vector from the last layer is sent to the decoder.

### A) Multi-head attention mechanism

Before we talked about attention mechanisms, but we are not done with the topic. In those architectures, a vector is processed into a subspace of fixed dimensions. What if we could jointly attend to information from different representation subspaces at different positions? That what multi-head attention does! Think of it as a dot-product attention compute by blocks, with each representation havinf a different size.

![Multi-head attention mechanism](/images/attention/multi_head.png){: .center-image }

As you can see in the figure, after we compute normal dot-products using all the $$h$$ different representations we want, and the output of them all is concatenated and then projected again. In the present paper in particular, they took $$h=8$$, with all of them having a projection size of $$d_{model} / h = 64$$ elements. With this configuration, we overcome the averaging issue that rises when using uniquely dot-attention mechanisms, while keeping more or less the number of parameters constant.

$$
\text{MultiHead}(Q,K,V) = \text{Concat}(H_1, H_2, \cdots, H_h) W^0
$$

where $$H$$ denotes every "head" of the module (every representation subspace from $$h$$ available),

$$
H_i = \text{Dot-product}(Q,W^Q_i, KW^K_i, VW_i^V)
$$

The matrices $$W^Q_i \in \mathbb{R}^{d_{model}\times d_k}$$, $$W^K_i \in \mathbb{R}^{d_{model}\times d_k}$$ and $$W_i^V \in \mathbb{R}^{d_{model}\times d_v}$$ are usual, trainable weight matrices.

### B) Residual connections

Deep Learning models are difficult to train, and it gets more difficult the more layers there are. In order to deal with such issue, the idea of keeping an untouched copy of the data that is fed to upper layers looks appealing. **It works under the assumption that the function we are trying to model (the weights of the net) is closer to an identity mapping than to a zero mapping**, so perturbations on this modelling should be easier to detect when referencing to the identity.

![Residual connection](/images/attention/residual.png){: .center-image }

Moreover, this sort of connections allow us to have way deeper models with barely any additional cost of computation. As a matter of fact, it has been observed that this idea works really well, so it's been an increasing number of papers devoted to it, such as *Wide Residual Networks* from Université Paris-Est and École des Ponts ParisTech[^14], or FAIR's *ResNeXt*[^15]. 

### C) Layer normalization

It is conceived as a way to speed up the training convergence of the neural network, usually requiring incredibly long times. Unlike *batch normalization*[^13], here the key is to extract statistical information within each single pass before the non-linearity takes place. It operates directly on the output vector $$\mathbf{a}^l$$ of the layer $$l$$, shifting the mean and variance of those data to 

$$
\mu^l = \frac{1}{H} \sum^H_{i=1} a_i^l
$$

$$
\sigma^l = \sqrt{\frac{1}{H} \sum^H_{i=1}(a_i^l - \mu^l)^2}
$$

with $$H$$ the number of units in the $$l$$-th layer. The values of the outputs of the layer, $$\mathbf{h}^l$$, are then converted by the following rule:

$$
\mathbf{h}^l = f\left[ \frac{\matbf{g}{\sigma^l}\circ(\mathbf{a}^l-\mu^l)+\mathbf{b}\right]
$$

where $$\mathbf{g}$$ and $$\mathbf{b}$$ are known as the gain and the bias parameters, and must be learned during training time.

Advantages of this approach against other strategies are that:
* Layer normalization does not impose any constraint in the batch size.
* It can be applied easily to CNNs and fully-connected networks, and requires just a little tuning for RNNs, making it suitable for every architecture studied until now.
* It is extremely fast to compute.

### D) Position-wise Fully-connected feed-forward nets

Probably the easiest element in the whole system, these sub-layers are but common fully-connected layers, with input and output size $$d_{model}=512$$, whose hidden layer has a size of $$d_{ff}=2048$$.

$$
\text{FFN} = \max (0, xW_1+b_1)W_2 + b_2
$$

The interesting thing with this module is that it is applied position-wise to the input, which translated to the problem at hand means that the same neural network is applied to every single "token" vector belonging to the sentence sequence, so $$x$$ is a vector representing a token of the input, and the same process is repeated for as many tokens as there are in the sentence.

## 4. Decoder

![Transformer's decoder](/images/attention/decoder.png){: .center-image }

The decoder resembles the encoder but for a detail. Althought it is also a stack of $$6$$ layers with sub-layers within them, there are $$3$$ of them instead of two: *multi-head attention*, *fully-connected layers* and *masked multi-head attention* (apart from the common *residual connections* and *layer normalization*).

### A) Masked causal attention

This masking hides those features that belong to future states of the sequence (notice it is a *self-attention* layer!). With this trick, we are making the model **autoregressive and causal**, so $$p(o_t) = p(o_t \mid o_1, o_2, \cdots, o_{t-1})$$ with $$o_i$$ denoting the output (generated translation). The generation of the current state is then not conditioned in any way by future states.

It is implemented setting to $$-\infty$$ the values corresponding to the forbidden states in the softmax layer of the dot-product attention modules. Thus, it is different than other causal convolutions such as the ones exhibited by *Wavenet*[^16] or *SampleRNN*[^17].

# Results published in the paper

Now we do understand the Transformer model, it's time to look at its performance. In the end, a system may look glorious but be absolutely useless when the truth comes up. This is not the case, though, since the results are stunning.

## Training hyperparameters

The hyperparameters used to obtain such results are the following:
* Each training batch was conformed by a set of sentence pairs containing about $$25000$$ source tokens and $$25000$$ target tokens.
* An Adam optimizer[^19] with parameters $$\beta_1 = 0.9$$, $$\beta_2=0.98$$ and $$\epsilon = 10^{-9}$$ was used.
* They considered an increasing learning rate (with $$\text{warmup_steps}=4000$$):

$$
lr = d^{-0.5}_{\text{model}} \cdot \min(\text{step_num}^{-0.5}, \text{step_num} \cdot \text{warmup_steps}^{-1.5})
$$

* Regularization techniques were used: residual dropout[^20], attention dropout[^1] and label smoothing[^21].

The model was trained during $$300000$$ steps, roughly $$3.5$$ days, using $$8$$ NVIDIA P$$100$$ GPUs.

With these hyperparameters, the Transformer model achieved a BLUE score of $$26.4$$ when applied over the [*newstet2013*](http://www.statmt.org/europarl/) dataset as a test set, **which set a new state-of-the-art**. Using the available open source repository [*tensor2tensor*](https://github.com/tensorflow/tensor2tensor), written in Tensorflow[^18] and including the full model and data used to train it, I ran my own experiment, though using fewer iterations and a smaller batch size. I trained it using one GPU NVIDIA Tesla P$$80$$ during $$2.5$$ days over the same training dataset (you can see the difference in the training with regards to the paper's), and I obtained a BLEU score of $$12.4$$ when using it over the same test data. It's not a great result, and it's far from being a state-of-the-art result, but definitely does great considering the training it has received!

Another advantage of attention layers against CNNs or RNNs, authors claim, is the intuition it can provide, and in fact it is quite easy to plot some interesting relationship that arise during training between English sequences, as shown below.

![Relationships observed in attention](/images/attention/relationships.png){: .center-image }

The picture shows some relationships present in two different heads from the layer encoder self-attention at layer $$5$$ of $$6$$. It can be seen, from the intensity of the links between words, that each of the heads has learned to perform a different task. But hey, you can see they're doing great in detecting long-term dependencies!

# Conclusion

This work is interesting because it replaces entirely the recurrent or convolutional layerls, using attention and fully-connected layers instead. For translation tasks, it set the new state-of-the-art benchmark, and offers a solid ground that suggest that the future of many architectures in Deep Learning will shift towards attention mechanisms. 

Although there are others architectures that make use of attention layers, none achieves so good results so fast. Not only that, but the only model that can compite against Transformer is the *Slicenet*[^22], proposed just fifteen days before. **It takes much longer to train, due to the huge amount of parameters it requires** ($$348$$ million against the $$213$$ millions of Transformer), and the BLEU scores it achieves are slightly worse on average. In short, up to date it offers no profit over Transformer.

Despite not having any explicit recurrency, implicitly the model is built as an autoregressive one. It implies that in order to generate an output (both while training or during inference), the model needs to compute previous outputs, which is extremely costly, for the whole net has to be run for every output. That's the main idea to overcome in a recent paper by researchers at [*Salesforce Research*](https://einstein.ai/research/non-autoregressive-neural-machine-translation) and the University of Hong Kong, who tried to make the whole process parallelizable[^23]. Their proposal is to compute *fertilities* for every input word in the sequence, and use it instead of previous outputs in order to compute the current output. This is summarized in the figure below.

![Fertilities over Transformer model](/images/attention/fertilities.png){: .center-image }

As you can see, the "relevance" of the input words is modelled, and then then those that are more important are copied a number of times dependent on their importance as a guessing that provides sort of the information Transformer does by its self-attention in the decoder. The results are not state-of-the-art. The only advantage (it's true), is the **possibility of compute all the output sequence at once, with just a small worsening in performance** (they report on having just $$2$$ points less in BLEU scoring over the same dataset than Transformer. In my opinion, those two features make it indeed suitable for more bussiness, real-world applications. 

The paper covered here could give rise to a whole book. I've tried my best to cover all the different components of the model, but sure every reader will miss something. That's why I encourage you to comment below and help me improve this post!


# References

[^1]: [Vaswani et al. (2017): "Attention is all you need". arXiv:1706.03762v4](https://arxiv.org/abs/1706.03762)

[^2]: [Goodfellow, I., Bengio, Y. and Courville, A., "Deep Learning", Chapter 9, MIT Press, 2016.](http://www.deeplearningbook.org/)

[^3]: [Goodfellow, I., Bengio, Y. and Courville, A., "Deep Learning", Chapter 10, MIT Press, 2016.](http://www.deeplearningbook.org/)

[^4]: [Bahdanau, D., KyungHyun, C. and Bengio, Y. (2016): "Neural Machine Translation by jointly learning to align and translate". arXiv:1409.0473v7](https://arxiv.org/abs/1409.0473)

[^5]: [EMNLP 2017 Second Conference on Machine Translation's (WMT17) website.](http://www.statmt.org/wmt17/)

[^6]: [Papineni et al. (2002): "BLEU: a Method for Automatic Evaluation of Machine Translation", pp. 311-318, Proceedings of the 40th Annual Meeting of the Association for Computational Linguistics (ACL).](http://citeseer.ist.psu.edu/viewdoc/summary?doi=10.1.1.19.9416)

[^7]: [Kalchbrenner et al. (2017): "Neural Machine Translation in linear time". arXiv:1610.10099](https://arxiv.org/abs/1610.10099)

[^8]: [Gehring et al. (2017): "Convolutional Sequence to Sequence Learning". arXiv:1705.03122](https://arxiv.org/abs/1705.03122)

[^9]: [Denny Britz, Anna Goldie, Minh-Thang Luong and Quoc V. Le. (2017): "Massive exploration of neural machine translation architectures". CoRR, abs/1703.03906.](https://arxiv.org/abs/1703.03906)

[^10]: [Bishop, Christopher M., "Pattern Recognition and Machine Learning (Information Science and Statistics", Springer-Verlag New York, Inc., Secaucus, NJ, USA, ISBN: 0387310738, 2006.](http://users.isr.ist.utl.pt/~wurmd/Livros/school/Bishop%20-%20Pattern%20Recognition%20And%20Machine%20Learning%20-%20Springer%20%202006.pdf)

[^11]: [He, K., Zhang, X., Ren, S. and Sun, J. (2015): "Deep residual learning for image recognition". arXiv:1512.03385](https://arxiv.org/abs/1512.03385)

[^12]: [Lei Ba, J., Kiros, J.R. and Hinton, G. (2016): "Layer normalization". arXiv:1607.06450](https://arxiv.org/abs/1607.06450)

[^13]: [Ioffe, S. and Szegedy, C. (2015): "Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift". arXiv:1502.03167](https://arxiv.org/abs/1502.03167)

[^14]: [Zagoruyko, S. and Komodakis, N. (2017): "Wide Residual Networks". arXiv:1605.0714604](https://arxiv.org/abs/1605.07146)

[^15]: [Xie et al. (2017): "Aggregated Residual Transformations for Deep Neural Networks". arXiv:1611.05431v2](https://arxiv.org/abs/1611.05431)

[^16]: [Van de Oord et al. (2016): "Wavenet: A generative model for raw audio". arXiv:1609.03499v2](https://deepmind.com/blog/wavenet-generative-model-raw-audio/)

[^17]: [Mehri et al. (2017): "SampleRNN: An Unconditional End-to-End Neural Audio Generation Model". arXiv:1612.07837](https://arxiv.org/abs/1612.07837)

[^18]: [Abadi et al. (2015): "TensorFlow: Large-Scale Machine Learning on Heterogeneous Systems", Software available from tensorflow.org.](https://www.usenix.org/system/files/conference/osdi16/osdi16-abadi.pdf)

[^19]: [Kingman, D. and Ba, A. (2015): "Adam: A method for stochastic optimization". In ICLR, 2015.](https://arxiv.org/abs/1412.6980)

[^20]: [Srivastava et al. (2014): "Dropout: a simple way to prevent neural networks from overfitting". Journal of Machine Learning Research, 15(1): 1929-1958, 2014.](http://jmlr.org/papers/v15/srivastava14a.html)

[^21]: [Szegedy et al. (2015): "Rethinking the inception architecture for computer vision". CoRR, abs/1512.00567, 2015.](https://arxiv.org/abs/1512.00567)

[^22]: [Kaiser, L., Gomez, A.N. and Chollet, F. (2017): "Depthwise Separable Convolutions for neural machine translation". arXiv:1706.03059v2](https://arxiv.org/abs/1706.03059)

[^23]: [Gu et al. (2017): "Non-autoregressive neural machine translation". arXiv:1711.02281](https://arxiv.org/abs/1711.02281)