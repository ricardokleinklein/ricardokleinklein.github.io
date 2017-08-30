---
layout: post
title: Neural network-based clothing classifier
author: Ricardo Faúndez-Carrasco
---

Many Deep Learning-oriented libraries have been released in the last years, up to the point of having literally too many frameworks to work with: *Caffe*, *Theano*, *Tensorflow* and *Pytorch* among others. I've worked mostly with the last two, but I shall constrain myself to *Pytorch* in this post to solve a classification task released few days ago: the [fashion-MNIST dataset](https://github.com/zalandoresearch/fashion-mnist/blob/master/README.md). The aim of this post is to run a series of experiments on pattern classification using neural networks, studying along the way the importance of the number of layers or how the number of neurons in the hidden layers affect the performance of the net. In the end, we seek to compare the accuracy of our classifiers with respect to the official results already published.

# What the data are

So far, in any AI-related problem the first task is to understand the data that will feed the learning system. Otherwise, we might well be missing relevant features of the input data that as a rule lead to a better performance of the overall system.

In this post I shall work on the recently released (August $$28^{th}$$, $$2017$$) [**Fashion-MNIST dataset**](https://github.com/zalandoresearch/fashion-mnist/blob/master/): a set of images where each one is a $$28$$x$$28$$-pixels grayscale picture of a given piece of clothing from [Zalando](https://jobs.zalando.com/tech/)'s article images, resembling the well-known original [MNIST dataset](http://yann.lecun.com/exdb/mnist/). Let's take a look at some of these images.

![fashion-mnist samples view](/images/fashion-MNIST/fashion-mnist-sprite.png)

Every class within the data takes three rows in the figure above. There are $$10$$ different classes, each one with the same number of instances both in the training and testing splits. Those classes are codified by an integer number ranging from $$0$$ to $$9$$: *T-shirt or top* (0), *Trouser* (1), *Pullover* (2), *Dress* (3), *Coat* (4), *Sandal* (5), *Shirt* (6), *Sneaker* (7), *Bag* (8), *Ankle boot* (9). 

Just like the original MNIST dataset, the splitting is performed such that there are $$60,000$$ instances for training and $$10,000$$ are intended for testing. There are the same amount of examples of every class in each of the two splits and an extremely important detail, they are already randomly shuffled once downloaded, so the data will not be skewed. There is actually a lot of work we could do here in order to understand better the data. There is literally a miriad of visualization techniques that can be applied to the data to gain a deeper understanding of the problem, but I won't explore them here but in other posts, since the data are simple enough.

# Our approach: Neural Networks

At the same time the dataset was released, a list of preliminary results was published, which is shown below in a summarized way. If you check it out, you'll find many different approaches to the task: from random forests to Support Vector Machines, not forgetting some shallow Neural Networks, denoted by [*MLP* (Multi-Layer Perceptron)](LINK TO PREVIOUS POST). Click on the link if it is the first time you hear about it.

![benchmark of preliminary results](/images/fashion-MNIST/benchmark.gif)

The link above will provide you with the theory behind neural networks, though a picture of the actual system we seek to build is always helpful to make up your mind. 

* The **input layer** will consist of $$28 \cdot 28 = 784$$ neurons, corresponding with the pixels in an instance image. In order to do so, every image is flattened in advance, getting a $$1$$x$$784$$ vector representation of the originally $$2$$-dimensional image.
* In contrast, the **output layer** will have just $$10$$ neurons, one per class. Remember, it's that way because at the output layer the net will compute the probability of a sample to belong to every class, from which we will take the neuron associated to the greatest value as the predicted class.

![Neural network](/images/fashion-MNIST/neuralNet.png)

* **Hidden layers** will take care of providing different layers of abstraction over the sample. Each consecutive layer will focus on the identification of more complex patterns than the previous layer. There is no limit both in the number of neurons per hidden layer or the number of layers, though there are several practical considerations one should bear in mind.

# Experiments

We are going to run three sets of experiments in order to explore some interesting features of neural networks:

1. The number of hidden layers.
2. The size (namely, the number of neurons) in the hidden layers.
3. The number of epochs, or how long we leave the system training.

The systems so developed will be measured by means of its accuracy with regards to the test dataset, this is, how well the predicted labels match the actual ones. 

## Coding a neural network in *Pytorch*

Before getting into the results section, I shall show a couple of examples for $$1$$- and $$2$$-hidden layer neural network implementations. Nonetheless, the whole code is available at my [Github profile](https://github.com/ricardokleinklein) in case you want to check anything out or you get stuck trying to add more layers/functionality. 

The core part of the scripts is contained within the following piece of code:

<script src="https://gist.github.com/ricardokleinklein/018f3b6c77021b5f8a33b65708eb5fc7.js"></script>

It defines the structure and working scheme of a neural network. In this simple case a single hidden unit is present. Moving onto two hidden layers is just as simple as adding the lines of code $$#13$$ and $$#14$$ to the previous snippet, having thus that the neural net is defined by

<script src="https://gist.github.com/ricardokleinklein/018f3b6c77021b5f8a33b65708eb5fc7.js"></script>

## On the number of layers

## On the size of the hidden layers

## On the number of epochs

# Conclusions