---
authors: goodman
---

# Training SNNs with limited gradients

[Download the slides here](slides/W5-V1-limited-gradients.pptx)

:::{iframe} https://www.youtube.com/embed/XnNcOLASZX0
:width: 100%
:::
---

:::{note}
In this section we'll look at some of the many methods for training spiking neural networks with either no attempt to use gradients, or only using gradients in a limited or constrained way.
:::

## Reservoir computing

Let's start with reservoir computing, also known as "liquid state machines" and "echo state networks" in different contexts.

In [this setup](#rescomp) we start with some time varying input sequence connected to a randomly recurrently connected group of neurons.

The connections included in the marked red section are not trained. The recurrent group is connected to a linear readout layer which is trained with a supervised algorithm to reproduce a desired time-varying output.

```{figure} figures/rescomputing.png
:label: rescomp
:alt: Reservoir Computing Setup
:align: center

Reservoir computing setup.
```

You initialise the recurrent neurons with weights which put the network dynamics into a near chaotic state. The idea is that near chaos, you will find a rich set of trajectories in the dynamics of these neurons, and by doing a linear readout you can approximate any dynamics you like.

And this works! 

You can prove that this setup allows you to approximate any time-varying function, with enough neurons. [Here's an example](#examptarget) of a target trajectory and a reconstruction using reservoir computing.

```{figure} figures/targettraj.png
:label: examptarget
:align: center

Example of a target trajectory and a reconstruction using reservoir computing.
```

There are lots of variants of this, for example with spiking neural networks you can add unsupervised training to the reservoir neurons using STDP.

A particularly interesting variant is FORCE training, where you write the recurrent weights as a sum of a fixed term that induces chaos, and a trainable term that can be trained with a recursive least squares algorithm.

:::{seealso} For more
:class: dropdown

See the following:

* [Julien Vitay lecture notes on reservoir computing](https://julien-vitay.net/lecturenotes-neurocomputing/4-neurocomputing/4-Reservoir.html)
* [Nicola and Clopath (2017) "Supervised learning in spiking neural networks with FORCE training"](https://doi.org/10.1038/s41467-017-01827-3)
:::

## Evolutionary algorithms

Another approach is to use global optimisation algorithms that don't require derivatives.

A particularly successful approach pioneered by Katie Schuman {cite:p}`https://doi.org/10.1145/3381755.3381758` is to use evolutionary algorithms. In these algorithms you generate a population of networks, evaluate them, and then create new networks by mixing up the most successful networks, repeating until you get good performance.

```{figure} figures/katie.png
:label: evalgo
:alt: Katie Schuman's Approach to Evolutionary Algorithms
:align: center

Training SNNs with evolutionary algorithms {cite:p}`https://doi.org/10.1145/3381755.3381758`.
```

[Here's an example](#breastcancer) network evolved with this method to classify breast cancer images.

```{figure} figures/cancernetwork.png
:label: breastcancer
:alt: Example Network evolved to Classify Breast Cancer Images
:align: center
:width: 500px

Example network evolved to classify breast cancer images {cite:p}`https://doi.org/10.1145/3381755.3381758`.
```

The advantage of this method is that it can find surprising architectures like this one that you might never otherwise have found. But, it does tend to be computationally demanding and limited to fairly small networks like this.

It's also been used to train neuromorphic hardware that we'll talk about later in this course, for example a controller for this [little robot](#robot).

```{figure} figures/littlerobot.png
:label: robot
:alt: Robot Controlled by Trained Neuromorphic Hardware
:align: center
:width: 300px

Robot controlled by neuromorphic hardware {cite:p}`https://doi.org/10.1109/IRIS.2017.8250111`.
```

You can see [an example](#robotnetwork) of the sort of network it finds. Another advantage here is that you can easily adapt it to the type of hardware available. In this case, a field programmable gate array or FPGA.

```{figure} figures/littlerobotnet.png
:label: robotnetwork
:alt: Robot Controlled by Trained Neuromorphic Hardware Network
:align: center
:width: 500px

Robot controller network {cite:p}`https://doi.org/10.1109/IRIS.2017.8250111`.
```

And [here's an example](#robottraj) of a trajectory showing that this robot was able to learn to drive around, avoid obstacles, etc.

```{figure} figures/littlerobottraj.png
:label: robottraj
:alt: Robot Controlled by Trained Neuromorphic Hardware Trajectory Map
:align: center
:width: 400px

Robot trajectory {cite:p}`https://doi.org/10.1109/IRIS.2017.8250111`.
```

## Converting artificial to spiking neural networks

Rather than trying to work with spiking neural networks directly, we can start by doing something we know how to do, like training an artificial neural network, and then convert the result into a spiking neural network.

There's a huge literature on this but I'm just going to mention two approaches from {cite:t}`https://doi.org/10.48550/arXiv.1510.08829`.

### Soft-LIF

1. The first starts by creating a non-spiking artificial neuron called a soft-LIF that approximates the input-output behavior of a spiking neuron.

2. You then train this soft-LIF network with standard algorithms, possibly adding training noise to make it more noise robust.

3. Then just replace those soft-LIF neurons with real LIF neurons and it works fairly well.

Eliasmith and colleagues later took this sort of approach much further, creating a very general method called the [Neural Engineering Framework (NEF)](http://compneuro.uwaterloo.ca/research/nef.html).


### Neural engineering framework

1. In this approach, you can directly implement vector function and differentials by encoding input values into a random high dimensional overcomplete representation

2. And then linearly decoding this, which they show allows you to approximate almost any function.

They implemented this in a comprehensive software package [Nengo](https://www.nengo.ai/) so it's easy to try it out if you're interested.

Once you have these building blocks, it's easy to then convert an ANN that is composed of these building blocks into their framework and implement it with spiking neurons.

## Restricting to a single spike

The last method we'll look at in this section is making the network differentiable by limiting each neuron to only be able to fire a single spike.

This might seem like a crazy limitation, but there's actually a good reason to try this coming from [Simon Thorpe and colleagues](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=297cd07d12ad74c10fee794fa947f02d561158ab).

They noted that primate brains are able to classify and respond to quite complicated visual stimuli, like distinguishing between food and not-food, with as little as a 100ms delay.

From studying the anatomy, they knew that to go from the visual system to the motor system that registered the response, the signal would have to go through around 10 layers.

Since cortical neurons usually fire only at most around 100 spikes per sec, that means that in the 10ms that each layer has to process, it likely only fires 0 or 1 spikes.

So it must be possible to do quite complex tasks with each neuron firing only 0 or 1 spikes. {cite:t}`https://doi.org/10.1142/S0129065720500276` implemented this idea.

They set up a network of integrate and fire neurons (note: not leaky integrate and fire), and only allowed them to fire one spike per stimulus presented.

With that limitation in place, they could write the output spike time of a neuron analytically in terms of the input spike times and weights. This function is differentiable, so we can apply backprop to train these networks.

They found that this gave excellent performance at the same types of visual classification tasks that Simon Thorpe had earlier studied in primates. [For example](#facetest), they could very reliably distinguish between faces and motorcycles in this dataset.

```{figure} figures/facemotor.png
:label: facetest
:alt: Distinguishing Between Faces and Motorcycles Visual Classification Task
:align: center
:width: 700px

Distinguishing between faces and motorcycles visual classification task {cite:p}`https://doi.org/10.1142/S0129065720500276`.
```

OK, that's enough for these limited gradient approaches. As usual, this section has only scratched the surface, and it's still a very active area of research.

:::{seealso} That's it!
In the next section, we'll talk about how to train spiking neural networks with the surrogate gradient method
:::