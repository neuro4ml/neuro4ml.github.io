---
authors: goodman
---

# Spiking is Not Differentiable

[Download the slides here](slides/W5-V0-spiking-is-not-differentiable.pptx)

:::{iframe} https://www.youtube.com/embed/0fNiTc-0t2k
:width: 100%
:::
---

```{danger} Work in progress
The text below has been transcribed by hand from the video above but has not yet been reviewed. Please use the videos and slides as the primary material and the text as support until I have a chance to proofread everything. When I have done this, I will remove this message.
```

:::{note}
In this section, we’ll focus on how to train spiking neural networks. But first though, in this video, we’ll see why this is difficult to do.
:::

## What we want to do

What we’d like to do is to use the methods developed for artificial neural networks, because we know they work really well.

:::{seealso} For more
:class: dropdown
This isn’t going to be a complete or comprehensive guide, so if you haven’t encountered this before, stop now and read an introduction.

[Introduction to neural networks and backpropagation by 3Blue1Brown (excellent, easy to follow YouTube series)](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi)
:::

So let’s quickly recap how that goes. 

First we write the neural network as a [function of inputs and parameters](#NN).

```{math}
:label:NN
y = f(x, \theta)

```

Parameters $\theta$, input $x$ leading to output $y$

For [example](#exampleNN), for a one layer neural network the parameters could be the weights, and the function could be a sigmoid applied to the dot product of the weights and input vector.

```{math}
:label:exampleNN
\begin{gather}
\theta = w \text{ weight matrix} \\
f(x,w) = \sigma(w^{T}x) \\
\sigma = \text{ some nonlinear function (e.g sigmoid)}
\end{gather}
```
We want to minimise some [loss function](#loss) that tells us how far the output of the network is from the desired output.

```{math}
:label:loss
\begin{gather}
\text{Want to minimise loss:} \\
\text{Loss function } \mathcal{L}(y,y^*) \\
\text{Desired output } y^* \\
\theta^* = \text{ arg } \underset{\theta}{\text{min }} \mathcal{L}(f(x,\theta),y^*)
\end{gather}
```

To do that, we apply [gradient descent](#graddescent), moving in the reverse direction of the gradient of the loss function with respect to the parameters.

```{math}
:label:graddescent
\begin{gather}
\text{Gradient descent:} \\
\theta \leftarrow \theta - y\nabla_{\theta}\mathcal{L}(f(x,\theta),y^*) \\ 
\text{Learning rate } \gamma \\
\text{Chain rule to compute } \nabla \text{ term}
\end{gather}
```

We can [compute the gradient](#computegrad) efficiently using vector and matrix multiplies using the vector chain rule.

```{math}
:label:computegrad
\begin{gather}
\text{Computing the gradient:} \\
\text{Gradient } (\nabla_{\theta}\mathcal{L})_i = \frac{\delta\mathcal{L}}{\delta\theta_i} \\
\text{Vector chain rule:} \\
x \in \mathbb{R}^n \overset{f}{\rightarrow} y \in \mathbb{R}^m \overset{g}{\rightarrow} z \in \mathbb{R}^p \\
\text{Jacobian matrix } J_{ij}^{f} = \frac{\delta f_i}{\delta x_j} \\
\text{Chain rule is } J^{g \circ f} = J^g J^f \\
\text{Efficient: just matrix / vector multiplications}
\end{gather}
```
So, can we just do this for a spiking neural network? It’s not quite so easy. Let’s see why.

## Why we can't do it

To see what goes wrong here, let’s start with a [recap](#LIFrecap) of the [leaky integrate-and-fire neuron](#LIF-section) we’ve seen before.

Over time it evolves according to a differential equation, with instantaneous effects when a spike arrives. Whenever the membrane potential $v$ crosses the threshold $v>1$ you fire a spike and instantly reset to $0$.

```{math}
:label:LIFrecap
\begin{gather}
\text{Recap:} \\
\text{\textbf{Leaky integrate-and-fire neuron}} \\
\text{Continous evolution oer time:} \\
\tau \frac{\ud v}{\ud t} = -v \\
\text{On presynaptic spike index i:} \\
v \leftarrow v + w_i \\
\text{Threshold condition, fire a spike if:} \\
v > 1 \\
\text{Reset event, after a spike set:} \\
v \leftarrow 0
\end{gather}
```

We’re going to rewrite [these equations](#LIFrecap), temporarily ignoring the synaptic connections for simplicity as you get the same problem and solution with that but it makes the notation more complicated.

The threshold we’ll write as $S(t)$ being the Heaviside function of $v(t)-1$. So this will be equal to $1$ if the neuron has spiked, otherwise $0$.

```{math}
:label: thresholdre
\begin{gather}
\text{\textbf{Threshold}} \\
S(t) = H(v(t) - 1)
\end{gather}
```


Now we replace the differential equation with a discrete update rule. To get $v$ at time $t+\delta t$ we multiply $v$ at time $t$ with this exponential term. We also fold in handling the spike reset by then multiplying this value by $1-S(t)$. So this means we multiply by $0$ if it spiked, otherwise we multiply by $1$. This is equivalent to setting it to $0$ after a spike.

```{math}
:label: replacede
v(t + \delta t) = e^{\frac{-\delta t}{\tau}}v(t)(1 - S(t))
```

Now if we wanted to use gradient descent we'd have to compute the gradient using the chain rule.
That's unproblematic for every part of these equations except for the Heaviside function. This function has derivative zero at every point except 0, where it's undefined.

```{math}
:label: heavire
\frac{\delta H}{\delta x} = 
\begin{cases} 
      0 & \text{if } x \neq 0 \\
      \text{undefined} & \text{if } x=0 \\
   \end{cases}

```

That means the gradients are zero almost everywhere. Which means that gradient descent will never change any parameters, making it useless for spiking neural networks.

So, how do we solve this problem?

## So what can we do?

Well there's a lot of different approaches that have been tried and that we'll talk about in the remaining sections this week. There's no ideal solution, so in this final part of this section we'll quickly talk about the general approaches people have tried and some of their different issues.

### Solution 1: Don't use gradients (for spiking part)

The first idea is just not to use gradients, or at least, not to use them for the spiking part of the network.

* One example of this is reservoir computing. You have a spiking neural network that you don't train with gradients, and a trainable readout layer. 
    * This actually works surprisingly well, but it's not an efficient way to learn because you're not using all the available parameters. 😭

* Alternatively you can use global optimisation methods like evolutionary algorithms. 
    * This works but you have to throw a lot of compute at it. 😭

* You can try taking an artificial neural network and converting it to a spiking neural network. 
    * Again, this works reasonably well but it doesn't find solutions that make use of spiking, and so it's not using the spiking neurons efficiently. 😭


### Solution 2: Restrict to a single spike

Another type of solution is to insist that each neuron fires precisely one spike, and take derivatives with respect to the time of that spike,

* This gives a nicely differentiable quantity. 
    * This is a clever solution that works surprisingly well in practice. The downside is that it's unrealistic to assume only one spike per neuron, and that really limits what you can do with it. 😭


### Solution 3: Smooth out the spikes

A clever trick is to take the non-differentiable Heaviside function and [smooth it out](#smoothedheavi) to a sigmoid that you can differentiate.

* This has been shown to work in at least some cases
    * However it's not a spiking neural network if you do that. 😭

* A related trick is to look at probabilistic neuron models, and then the parameters of their distributions are differentiable.
    * This is theoretically nice, but in practice doesn't seem to scale well. 😭

```{figure} figures/smooth.png
:label: smoothedheavi
:alt: Use Sigmoid to Model Heaviside Function, To Differentiate
:align: center

Use Sigmoid to Model Heaviside Function, To Differentiate
```

### Solution 4: Surrogate gradients

A more generally applicable approach is a method called surrogate gradients. We'll talk more about this in a later section. The key idea is we do the same smoothing, but only when we're computing gradients, not for the the forward pass.

* It's a bit surprising, but this works. We provide gradients to a different function from the one we’re optimising, but it gives us good solutions. We have some ideas of why this works but it's not a fully rigorous explanation.
    
    * This method is still quite computationally expensive, but it is feasible to use it for networks of a few thousand neurons at least. 😑
    
    * One downside is that it uses global information that wouldn't be available to a neuron in a biological setting. This is fine if we just want to know what a good solution would look like, but makes it a bad model for how the brain learns. 😢


### Solution 5: Approximate gradients

* To address both these issues, people use approximations that reduce the computational complexity and make the learning rules only use local information.

    * Performance does tend to be worse 😢
    * But on the upside, these rules are more biologically plausible. 😊

:::{seealso} That's it!
In the next section, we’ll dive into some of the details of these different solutions.
:::