---
layout: post
title: Neural network-based clothing classifier
author: Ricardo Kleinlein
---

Many Deep Learning-oriented libraries have been released in the last years, up to the point of having literally too many frameworks to work with: *Caffe*, *Theano*, *Tensorflow* and *Pytorch* among others. I've worked mostly with the last two, but I shall constrain myself to *Pytorch* in this post to solve a classification task released few days ago: the [fashion-MNIST dataset](https://github.com/zalandoresearch/fashion-mnist/blob/master/README.md) (a published paper is also available, explaining the dataset in detail [^1]). The aim of this post is to run a series of experiments on pattern classification using neural networks, studying along the way the importance of the number of layers or how the number of neurons in the hidden layers affects the performance of the net. In the end, we seek to compare the accuracy of our classifiers with respect to the official results already published.

# What the data are

So far, in any AI-related problem the first task is to understand the data that will feed the learning system. Otherwise, we might well be missing relevant features of the input data that as a rule lead to a better performance of the overall system.

In this post I shall work on the recently released (August $$28^{th}$$, $$2017$$) [**Fashion-MNIST dataset**](https://github.com/zalandoresearch/fashion-mnist): a set of images where each one is a $$28$$x$$28$$-pixels grayscale picture of a given piece of clothing from [Zalando](https://jobs.zalando.com/tech/)'s article images, resembling the well-known original [MNIST dataset](http://yann.lecun.com/exdb/mnist/) [^2]. Let's take a look at some of these images.

![fashion-mnist samples view](/images/fashion-MNIST/fashion-mnist-sprite.png)

Every class within the data takes three rows in the figure above. There are $$10$$ different classes, each one with the same number of instances both in the training and testing splits. Those classes are codified by an integer number ranging from $$0$$ to $$9$$: *T-shirt or top* (0), *Trouser* (1), *Pullover* (2), *Dress* (3), *Coat* (4), *Sandal* (5), *Shirt* (6), *Sneaker* (7), *Bag* (8), *Ankle boot* (9). 

Just like the original MNIST dataset, the splitting is performed such that there are $$60,000$$ instances for training and another $$10,000$$ that are intended for testing. There are the same amount of examples of every class in each of the two splits and an extremely important detail, they are already randomly shuffled once downloaded, so the data will not be skewed. There is actually a lot of work we could do here in order to understand better the data. There is literally a miriad of visualization techniques that can be applied to the data to gain a deeper understanding of the problem, but I won't explore them here but in other posts, since the data are simple enough.

# Our approach: Neural Networks

At the same time the dataset was released, a list of preliminary results was published, which is shown below in a summarized way. If you check it out, you'll find many different approaches to the task: from random forests to Support Vector Machines, not forgetting some shallow Neural Networks, denoted by [*MLP* (Multi-Layer Perceptron)](https://www.youtube.com/watch?v=uXt8qF2Zzfo&index=12&list=PLUl4u3cNGP63gFHB6xb-kVBiQHYe_4hSi). Click on the link if it is the first time you hear about it.

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
3. The number of epochs, or how long we train the model.

The systems so developed will be measured by means of its accuracy with regards to the test dataset, this is, how well the predicted labels match the actual ones. 

## Coding a neural network in *Pytorch*

Before getting into the results section, I shall show an example of a baseline neural network implementation. Nonetheless, the whole code is available at my [Github profile](https://github.com/ricardokleinklein) in case you want to check anything out or you get stuck trying to add more layers/functionality. 

The core part of the scripts is contained within the following piece of code, namely the definition of a neural network:

<script src="https://gist.github.com/ricardokleinklein/018f3b6c77021b5f8a33b65708eb5fc7.js"></script>

Those lines of code set the architecture for our neural networks. They will be pretty simple, consisting of a variable number of layers where the information from one layer to the next one is performed by a linear operation $$\mathbf{y} = \mathbf{W}^T \cdot \mathbf{x} + \mathbf{b}$$, which is the dot product of the correspoding weight matrix by the input at that layer plus the bias term. This is done in line $$#15$$ in the snippet above.

Had our neural networks been linear, no learning could take place. The non-linearities implemented at each layer are (as in the benchmark results published so far) $$\tanh$$ functions, defined as

$$ \tanh(\mathbf{x}) = \frac{\sinh(\mathbf{x})}{\cosh(\mathbf{x})} = \frac{\exp(\mathbf{x}) - \exp(-\mathbf{x})}{\exp(\mathbf{x}) + \exp(-\mathbf{x})}$$ 

These few lines of code contain the information regarding the neural net's architecture. Furthermore, we ought to state what the training method is.

* `criterion = nn.CrossEntropyLoss()` -- An usual cost function used in classification tasks is the *Cross Entropy* [^3].
* `optimizer = torch.optim.SGD(self.parameters(), lr = lr)` -- Indicates that the training algorithm is *Stochastic Gradient Descent*[^4], and that we are going to use a fixed, non-decaying learning rate $$lr$$. 

For those experienced in neural networks, this architecture may seem extremely naïve, and I am aware of that. Nonetheless, this post is intended to be but a first approach to the problem, and I plan to further the quality and the depth of this study in upcoming experiments. 

## On the number of hidden layers

In order to have an intuition on how many hidden layers yield the best score, I ran a series of trials where the only change among the nets were the number of hidden layers, but the amount of epochs, learning rate and number of neurons in each hidden layer is the same.

The learning rate was fixed at a value of $$lr = 0.03$$. After $$50000$$ iterations training, which is around $$416$$ epochs considering that $$\mathrm{batch\_size} = 500$$, the accuracies obtained over the test set were:
* For the net with **1 hidden layer** (its layer structure is $$[784, 100, 10])$$ -- **Accuracy $$\mathbf{= 83.85}$$%**.
* With **two hidden layers**, $$[784, 100, 100, 10]$$ -- **Accuracy** $$\mathbf{= 79.44}$$%.
* When the net has **three hidden layers**, $$[784, 100, 100, 100, 10]$$ -- **Accuracy** $$\mathbf{= 82.02}$$%.

![accuracy upon num layers](/images/fashion-MNIST/num_layers.png)

Surprisingly, the best score was achieved with the simplest neural network! In order to understand why, it is always a good idea to take a look at the loss function values along the training phase. Below a comparison of the three loss function values is shown.

![Loss cross entropy function values](/images/fashion-MNIST/cost_layers.gif){: .center-image }

As you can easily see, there is not a big difference among them. This is, the three of them reach similar loss values and remain stable on them, so the training is succesfull recognising a great deal of patterns in the data. Another particular feature remarkable of discussion is the fact that the more layers not always mean more accuracy. Better said, it's not that easy, and there are many more details that should be kept in mind when implementing larger networks. 

With these results, I am going to pick the best neural network to continue the experiments.

## On the number of neurons in the hidden layer

Once we have decided a number of layers for the neural network-based classifier, now it is time to decide how many neurons will be in it. The input and output layer must remain the same due to the task, but the hidden layer is another story. Ideally, it should not be either too small or too large. Long story short, the hidden layer should have a number of neurons such that it does not become a bottleneck, contraining the patterns that can be recognized on it, but neither should be too large so there is not classification. 

The experiments I have run cover a wide range of sizes: from $$600$$ to $$10$$ neurons: $$600$$, $$500$$, $$400, 300, 200, 100, 50$$ and $$10$$. The learning rate and the training time remain exactly the same than in the previous experiments.

![accuracy upon number of neurons on hidden layer](/images/fashion-MNIST/num_neurons.png)

What we observe is that too few neurons yield a poor result. Actually, we can check that the nets we were working with in the previous experiment could likely perform much better if more neurons had been added to the models. You can notice that an intermediate number of neurons in the hidden layer is the best choice. Just like with too few neurons, when the hidden layer is made up of too many, the accuracy decays too. This is happening because an intermediate number of neurons allows the net to properly learn the most remarkable features of the dataset, making a reasonable generalisation of it. 

Once again, if we take a look at the plot of the loss function, it is easy to see how it is closely related to the performance of the model over the test set. The blue line in the figure below represents the loss function of the worst model, the one with just $$10$$ neurons in its hidden layer, whereas the other two lines, which are approximately equal to each other, represent the nets with $$300$$ and $$400$$ hidden units respectively.

![Loss cross entropy for several hidden units](/images/fashion-MNIST/cost_neurs.gif){: .center-image }

As you can see, the loss is greater in the first model, thus it has not learned as well the important features for a succesful classification.

## On the training time

So far we've found that a neural network with just one hidden layer and about $$300$$ neurons is pretty close to the benchmark results published by the dataset's authors. However, we have not achieved the same good results yet. At this point it'd be reasonable to ask yourself why.

One possible line of action is to increase the training in terms of epochs (namely, the number of iterations), since the number of samples is fixed. In order to test it, the number of iterations was increased from the $$\sim 5 \cdot 10^4$$ iterations of previous experiments to $$5$$ millions ($$5 \cdot 10^6$$). The figure below shows the accuracy over the test data at different stages of the training phase. The results are averaged over $$3$$ experiments.

![accuracy upon number of iterations](/images/fashion-MNIST/num_iters.png)

Notice that in the beginning it learns pretty fast, so the learning curve (below) decreases quickly as well as the accuracy increases steadily until it reaches a million iterations. Keep in mind that with the rest of bars represent a training time as long as what it has already trained. With that in mind, you can see that any upgrade in accuracy comes with a lot of training. Really, a lot. However, something happens at the end of the process: **the accuracy no longer increases, but worsens instead!**

![Loss cross entropy for iterations](/images/fashion-MNIST/cost_iters.gif){: .center-image }

Moreover, the learning curve remains somehow stable around the same values. This fact confirms that the system has reached a minimum in the cost function, and thus the learned parameters are as general as they can be. In short, once the algorithm reaches this point, you shouldn't expect it to keep learning a better generalization of the data, becoming a benchmark for the model. 

After the right number of iterations, I got pretty much the same results as the original paper announced, reassuring them. 

# Conclusion

Following there's a short list of issues we all should always keep in mind when building classification systems based on neural networks:

1. **More layers do not always mean a better performance**. This a common mistakes, made both by experienced professionals and newcomers.
2. The number of hidden processing units (namely neurons) must be tuned carefully in order to get the best results possible. Bear in mind bottleneck issues!
3. Although as a rule more training leads to better performances in these systems, there's always a trade-off *time-accuracy*: It may take too long to improve your current results by a $$1$$%, and generally that same improvement can be due to a better random initialization of the parameters. Instead of increasing the number of epochs, try another configuration of the hyperparameters instead.

There's no much left I'd like to mention. As I explained in the beginning of the post, this is intended to be a merely introduction to the topic of pattern classification with neural networks. 

Please leave any suggestion you may have in the comments below. Any idea is indeed welcome!

# References

[^1]:[Xiao, H., Rasul, K., & Vollgraf, R. (2017): "Fashion-MNIST: a Novel Image Dataset for Benchmarking Machine Learning Algorithms". arXiv:1708.07747.](https://arxiv.org/abs/1708.07747v1?utm_campaign=Artificial%2BIntelligence%2Band%2BDeep%2BLearning%2BWeekly&utm_medium=email&utm_source=Artificial_Intelligence_and_Deep_Learning_Weekly_28)

[^2]: [Y. LeCun, L. Bottou, Y. Bengio, and P. Haffner. "Gradient-based learning applied to document recognition." Proceedings of the IEEE, 86(11):2278-2324, November 1998.](http://yann.lecun.com/exdb/publis/index.html#lecun-98)

[^3]: [Michael A. Nielsen, "Neural Networks and Deep Learning", Determination Press, 2015.](http://neuralnetworksanddeeplearning.com/chap3.html)

[^4]: [Léon Bottou: Large-Scale Machine Learning with Stochastic Gradient Descent, Proceedings of the 19th International Conference on Computational Statistics (COMPSTAT'2010), 177–187, Edited by Yves Lechevallier and Gilbert Saporta, Paris, France, August 2010, Springer.](http://leon.bottou.org/publications/pdf/compstat-2010.pdf)