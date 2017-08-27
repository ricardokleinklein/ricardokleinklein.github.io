---
layout: post
title: Easy GUI design in Matlab (I)
author: Ricardo Faúndez-Carrasco
---

When you make a click on your computer, when you type something in a text editor, when you scroll down using a slidebar... Everytime you do that, you are interacting with computer programmes by means of a Graphical User Interface (GUI). 

GUIs are a wonderful thing. It makes our lifes much easier, allowing us to focus on what we want to do with the computer rather than thinking how to do it. Instead of typing a long set of commands in the terminal, we can just click on *"save"*, and the document we are working on on the screen is safely stored in the blink of an eye. That's a great shot. During my studies I had to code a lot of simulations (believe me, a lot) in order to understand many physical laws, and sideways pass my exams. The range of topics was huge: from simple ones like the law of conservation of momentum, to galactic motion, gas theory or quantum dynamics. However, there was a permanent problem for most of my classmates: they knew how to code any given physical system, but they failed in making it readable, understandable. Rather than moving the point of view of the camara to span the whole system, they had to struggle with the code every single time they wanted the system to actually perform the simulation under any minimal change in the original conditions of the problem. This is, not a single coefficient could be  changed in real time, making those simulations far from practical.

As many engineering and science students, we worked most of the time with *Matlab®*. As usual, that means pros and cons. I won't dig deeper into that, let's just say that one major advantage of *Matlab®* over any other programming language underlies on how easily it is to show on screen a visual representation of the system. But, linking with what said above, sometimes we would love to have some buttons, or display additional information to make the simulation more useful. With some practice, you'll soon be able to code GUIs such as this one, which displays a complete customizable robotic arm along with some of its cinematic properties:

![Full GUI robotic arm](/images/GUI/gui_full.png)

I guess there's no need to say that having all the information at once, plus the opportunity to change some parameters at any given time, is way much more useful than the sum of the parts separately. Even though a typical *Matlab®* installation has attached *GUIDE*, which is a sort of assistant for GUI design, I prefer the hard way, and code it by myself. I make my point here, since using that tools you'll soon see tons of lines of code you don't understand, and probably get stuck pretty quickly. With my method, you'll always be in control of how does the GUI look, and what's going on beneath the visual appearance. In this first post of a series on the topic, I shall explain how to design and create the skeleton of any GUI: a **interface window** and a **click-responsive button**.

The goal is to create a sample GUI that displays the plot of a sine function with a different phase $$\omega$$ each time we press a button on the screen. The sine function follows the equation $$f(x) = \sin (\omega x)$$.

# Step 1: Elemental interface window

First of all you have to create the window frame the rest of elements of the GUI will be placed in. The whole GUI is defined as a function.

<script src="https://gist.github.com/ricardokleinklein/99867e400196a734de72072510f35e8e.js"></script>

If you run the piece of code above, you should get something like this:

![Customized GUI](/images/GUI/window2_gui.png)

Most likely, it has caught your attention that we are using a *Matlab®* *structure* to place the element in. A [structure](https://es.mathworks.com/help/matlab/ref/struct.html) is the analogous to a class in other programming languages. The definition of the element is made in the first statement `handles.mainFrame = figure(...)`.

## Step 2: Interactive button

The main call of designing a GUI is its functionality. In order to achieve that in *Matlab®*, you must make use of **callback functions** and **handle objects**. The latter contains the information regarding the objects in the GUI, whereas the first involves the tasks to perform by those objects.

<script src="https://gist.github.com/ricardokleinklein/127a50243c19cc7db9ae5fb213517071.js"></script>

In the snippet above, I have created a *button* object with a piece of text on it. The most important line, though, is the last one, when we set the callback function `plot_sine to be applied over the objects contained within the structure `handles`. Note that the kind of the object is defined in the tag *style* of the *uicontrol* label. It just takes to change the value `push` to `slide` if you want a sidebar, and just like that, many other objects.

![GUI with a non-sense push button](/images/GUI/button1.png)

## Step 3: Add the plot of the function

Last but not least, we must add functionality to our GUI. First, in order to plot the sine function, we shall add within the GUI function the lines of code required to include an axis in which the sine is plotted. Nonetheless, the callback function is defined separately from the main function, though in the same file. The complete piece of code for the entire GUI is shown below.

<script src="https://gist.github.com/ricardokleinklein/0bbd7a6720415d9ae690e6d2c62eedb3.js"></script>

The result is depicted in the next figure. 

![Plot of sine function through GUI](/images/GUI/button2.png)

And good news! This is all it takes to build a GUI in *Matlab®*. Now I encourage you to further your experience with more complex examples. I have a good bunch of them available at my [Github profile](https://github.com/ricardokleinklein). In following posts I shall show how to add event-depending interaction, real-time simulations and many other features I'm sure you'll want to include in your future projects. Feel free to leave a comment if there's anything in particular you want me to cover in upcoming posts!