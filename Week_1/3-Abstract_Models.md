# Abstract neuron models

[Download the slides here](slides/W1-V2-abstract-models.pptx)

:::{iframe} https://www.youtube.com/embed/GX1SIWfhGKc
:width: 100%
:::
---

```{danger} Work in progress
The text below has been transcribed by hand from the video above but has not yet been reviewed. Please use the videos and slides as the primary material and the text as support until I have a chance to proofread everything. When I have done this, I will remove this message.
```

## Introduction

So far this week we’ve seen a lot of biological detail about neurons.
The topic of this section is how we abstract and simplify this into very simple models.

:::{attention} Note!
In this section, we’ll focus on models without a lot of biological detail like the spatial structure of the neuron, and we’ll come back to that in the next section.
:::

## Artificial neuron

You’re probably familiar with the artificial neuron from machine learning.

This neuron has n inputs, which have activations $x_1$ to $x_n$ with weights $w_1$to $w_n$. Each of these activations and weights is a real number.

It performs a weighted sum of its inputs and then passes that through some nonlinear function $f$.
Various functions can be used here.

In older work you’ll often see the sigmoid function, and that’s partly because it was considered a good model of real neurons.

Nowadays, you’ll more often see the ReLU, which turns out to be much easier to work with and gives results that are often better.

```{figure} figures/artificial.png
:label: neuronmodel
:alt: Models for Artifical Neurons
:align: center

Models for Artificial Neurons
```

Interestingly, there are papers still being published about what the best model of real neurons’ activation functions, like [this one](https://www.biorxiv.org/content/10.1101/2023.09.13.557650v1) from 2023 which looks a bit intermediate between sigmoid and ReLU.

The relationship between the model and the biological data is established by computing an input-output function, typically by counting the number of spikes coming in to the neurons versus the number of spikes going out, averaged over multiple runs and some time period.

What this model misses out is the **temporal dynamics** of biological neurons.

## Biological neuron

So let's talk about temporal dynamics. We saw in previous sections that when a neuron receives enough input current it pushes it above a threshold that leads to an action potential, also called a [spike](#spikes).

The source of the input current is incoming spikes from other neurons, and we’ll talk more about that in next week's sections on synapses.

For this week, [here’s](#ideal-graph) a simple, idealised picture of what this looks like. It’s from a model rather than real data, to more clearly illustrate the process.

(ideal-graph)=
![](#biological-neuron-fig-label)

The curve shows the membrane potential of a neuron receiving input spikes at the times indicated by the red circles. Each incoming spike causes a transient rush of incoming current.

For the first one, it’s not enough to cause the neuron to spike, and after a while the membrane potential starts to decay back to its resting value.

And then, more come in and eventually the cumulative effect is enough to push the neuron above the threshold, and it fires a spike and resets.

## Integrate and Fire Neuron

The simplest way to model this is using the "integrate and fire neuron". [Here's](#integrate-and-fire-fig-label) a plot of how it behaves.

![](#integrate-and-fire-fig-label)

Each time an incoming spike arrives, every 10 milliseconds here, the membrane potential instantaneously jumps by some fixed weight until it hits the threshold, at which point it fires a spike and resets.

We can write this down in a standard form as a series of event-based equations.

Firstly, when you receive an incoming spike:

```{math}
:label: rxspike

v \leftarrow v + w_i

```

Set the variable $v$ to be $v+w_i$, if the threshold condition ($v > 1$) is true, fire a spike.

After a spike, set $v$ to 0.

We can see that already this captures part of what’s going on in a real neuron, but misses the fact that in the absence of new inputs, the membrane potential decays back to rest. Let’s add that.

(LIF-section)=
## Leaky integrate-and-fire (LIF) neuron

We can model this decay by treating the membrane as a capacitor in an electrical circuit. Turning this into a differential equation, $v$ evolves over time according to the [differential equation:](#de)

```{math}
:label: de

\tau \frac{dv}{dt} = - v

```
where $\tau$ is the membrane time constant we discussed in the last section.

This differential equation can be solved to show that in the absence of any input, $v$ decreases exponentially over time with a time scale of $\tau$.

Here's how that looks:

```{math}
\begin{gather}
\text{Continuous evolution over time:}\\
\tau \frac{dv}{dt} = -v\\
\text{Solution is } v(t) = v(0)e^{\frac{-t}{\tau}}
\end{gather}
```

![](#LIF-fig-label)

We can see that after the instantaneous increase in membrane potential it starts to decay back to the resting value. Although this doesn’t look like a great model, it turns out that very often this captures enough of what is going on in real neurons.

But there is another reason why adding this leak might be important.

## Reliable spike timing

In experiments, if you inject a constant input current ([horizontal line, top left](#reliable)) into a neuron and record what happens over a few repeated trials, you see that the membrane potentials ([top left above input current](#reliable)) and the spike times ([bottom left](#reliable)) are fairly different between trials. That’s because there’s a lot of noise in the brain which adds up over time.

On the other hand, if you inject a fluctuating input current ([top right](#reliable)) and do the same thing, you’ll see both the membrane potentials ([top right, above fluctuating current](#reliable)) and the spikes ([bottom right](#reliable)) tend to occur at the same times.


```{figure} figures/reliable1.png
:label: reliable
:alt: Spike Timing
:align: center

Spike Timing Graphs for Constant and Fluctuating Input Current (see [paper](https://doi.org/10.1126/science.7770778))
```

The vertical lines on the bottom right plot show that on every repeat, the spike is happening at the same time.

:::{note} Note
We’ll come back to why this happens in a moment, but first let’s have a look at some models in the same situation.
:::

We see the same thing with a LIF neuron. [Here](#LIFSpike) are the plots for the membrane potentials as semi-transparent so that you can clearly see that both the spike times and membrane potentials tend to overlap for the fluctuating current, but not the constant current.

(LIFSpike)=
![](#Brian-LIF-fig-label)

But now if we repeat that with a simple integrate and fire neuron without a leak, you can see you get unreliable, random spike times for both the constant and fluctuating currents.

(LIFSpikeNOLEAK)=
![](#Brian-non-LIF-fig-label)

In other words, adding the leak made the neuron more robust to noise. An important property for the brain, and perhaps also for low power neuromorphic hardware that we’ll talk about later in the course.

So why does the leak make it more noise robust? Well that was answered by Romain Brette in a [paper](https://doi.org/10.1162/089976603762552924) in 2003. The mathematical analysis is complicated, but it boils down to the fact that if you either have a leak, or a nonlinearity in the differential equations, the fluctuations due to the internal noise don’t accumulate over time, whereas with a linear and non-leaky neuron, they do.

## LIF neuron with synapses

OK, so we have an LIF neuron which we’ve just seen has some nice properties that you’d want a biological neuron model to have. But still, when you look at the graph [here](#ideal-graph), you can see that these instantaneous jumps in the model are not very realistic. So how can we improve that?

A simple answer is to change the model so that instead of having an instantaneous effect on the membrane potential, instead it has an instantaneous effect on an input current, which is then provided as an input to the leaky integrate-and-fire neuron.

You can see on the bottom plot [here](#LIFimproved) that the input current is now behaving like the membrane potential before, with this exponential shape. You can think of that as a model of the internal processes of the synapses that we’ll talk more about next week.

:::{figure} #LIF-with-synapses-fig-label
:label: LIFimproved

Improved LIF Model
:::

We model that by adding a [differential equation](#de2), with its own time constant $\tau_I$:

```{math}
:label: de2
\tau_I \frac{dI}{dt} = -I 

```

We also add that current $I$ to the differential equation for $V$ here, multiplied by a constant $R$. The rest of the model is as it was before.

We can see that this gives a better approximation of the shape.
There’s more that can be done here if you want, for example modelling changes in conductance rather than current, but we’ll get back to that next week.

## Spike frequency adaptation

If you feed a regular series of spikes into an LIF neuron (without any noise) you’ll see that the time between spikes is always the same. It has to be because the differential equation is memoryless and resets after each spike.

However, real neurons have a memory of their recent activity. [Here](#memory) are some recordings from the [Allen Institute cell types database](https://celltypes.brain-map.org/data) that we’ll be coming back to later in the course.

```{figure} figures/spikefreq.png
:label: memory
:alt: Spike Frequency Adaptation
:align: center

Evidence of Neuron Memory Based on Spike Frequency
```

In these recordings (from {cite:t}`https://doi.org/10.7554/eLife.65459`), they’ve injected a constant current into these three different neurons, and recorded their activity.
We can see that rather than outputting a regularly spaced sequence of spikes, there’s an increasing gap between the spikes.
There are various mechanisms underlying this, but essentially it comes down to slower processes that aren’t reset by a spike. Let’s see how we can model that.

Actually there are many, many different models for spike frequency adaptation. We'll just show one very simple approach, but you can take a look at [this review](http://dx.doi.org/10.4249/scholarpedia.30643) if you want some more ideas.

In the simple model [here](#memorymodel), we just introduce a variable threshold represented by the variable $v_T$. Each time the neuron spikes, the threshold increases, making it more difficult to fire the next spike, before slowly decaying back to its starting value.

:::{math}
\begin{gather}
\text{Continuous evolution over time:} \\
\tau_T \frac{dv_T}{dt} = 1 - v_T\\
\text{Threshold condition, fire spike if:}\\
v > v_r\\
\text{Reset event, after a spike set:}\\
v_T \leftarrow v_T + \delta v_T
\end{gather}
:::

:::{figure}  #adaptive-LIF-fig-label
:label: memorymodel
:alt: Spike Frequency Adaptation Memory Model
:align: center

Spike Frequency Adaptation Memory Model
:::

In the equations, we represent that by having a new exponentially decaying differential equation for the threshold, modifying the threshold condition so that it’s $v>v_T$ instead of $v>1$, and specifying that after a spike the threshold should increase by some value. You can see that it does the job, there’s a smaller gap between earlier spikes than later spikes.

In general, spike frequency adaptation can give really rich and powerful dynamics to neurons, but we won’t go into any more detail about that right now.

## Other abstract neuron models

This has been a quick tour of some features of abstract neuron models. There’s a lot more to know about if you want to dive deeper.

```{figure} figures/othermodels.png
:label: moremodels
:alt: More Neuron Models
:align: center

More Abstract Neuron Models
```

Some of the behaviours of real neurons that we haven’t seen in our models so far include:
* Phasic spiking, i.e. only spiking at the start of an input.
* Bursting, both phasic at the start or tonic ongoing.
* And post-inhibitory rebound, where a neuron fires a spike once an inhibitory or negative current is turned off.

You can capture a lot of these effects with two variable models such as the adaptive exponential integrate and fire or Izhikevich neuron models.

There's also stochastic neuron models based either on having a Gaussian noise current or a probabilistic spiking process.

Taking that further there are Markov chain models of neurons where you model the probability of neurons switching between discrete states.

:::{seealso} For more
:class: dropdown
You can find the figures from this section in the exercise section.

Further reading:

* [Gerstner and Brette (2009) " Adaptive exponential integrate-and-fire model"](http://dx.doi.org/10.4249/scholarpedia.8427)
* [Gerstner et al. (2014) "Neuronal Dynamics" - Part 1 Foundations of Neuronal Dynamics](https://neuronaldynamics.epfl.ch/online/Pt1.html)
* [Biological neuron models](https://en.wikipedia.org/wiki/Biological_neuron_model) - good Wikipedia article
:::

:::{seealso} That's it!
In the next section, we’ll talk about more biophysically detailed models of neurons.

:::
