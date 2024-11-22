---
authors: goodman
---

# STDP

[Download the slides here](slides/W4-V2-STDP.pptx)

:::{iframe} https://www.youtube.com/embed/fvzzwHKlMzk
:width: 100%
:::
---

## Introduction

Spike Timing-Dependent Plasticity (STDP) is a form of [Hebbian learning](#hebbian) that takes the timing of individual spikes into account.

## Experimental measurement

Let's start with how it's measured. We find a pair of neurons connected by a [synapse](#chemical-synapses). We then force the pre-synaptic neuron to fire a spike at time 0 and the post-synaptic neuron to fire a spike at time $\Delta t$. We then repeat this pairing about 60 times, wait around an hour, and measure the change in the synapse. We measure this by computing the height of a post-synaptic current from a single excitatory spike before and after the pairing.

```{figure} figures/stdpPicture1.png
:align: center
:width: 350px
```

And we get results that look like [this](#stdp-results-graph). It's a bit noisy, but you can generally see that if the post-synaptic neuron fires after the pre-synaptic neuron, the weight goes up. If the post-synaptic neuron fires before the pre-synaptic neuron, the weight goes down. The closer in time, the stronger the effect.

```{figure} figures/stdpPicture2.png
:label: stdp-results-graph
:align: center
:width: 400px
```

We can roughly fit this with a [pair of exponentials](#stdp-exp-graph) with different heights and time constants:

```{math}
\Delta w = 
\begin{cases}
    A_+ e^{-\frac{\Delta t}{\tau_+}} \quad \text{if } \Delta t > 0 \\
    A_- e^{+\frac{\Delta t}{\tau_-}} \quad \text{if } \Delta t < 0
\end{cases}
```

Shown in this figure:

```{figure} figures/stdpPicture3.png
:label: stdp-exp-graph
:align: center
:width: 400px
```

We talked before about Hebbian learning (what fires together, wires together) but this is actually closer to what Hebb originally suggested. He suggested that when a pre-synaptic neuron repeatedly contributes to the firing of a post-synaptic neuron, the synapse will get stronger. STDP realises that idea because if the post-synaptic neuron fires before the pre-synaptic neuron, of course it couldn't be that the pre-synaptic neuron contributed to that firing. Similarly, if the timing of the spikes is far apart, it's unlikely they were related. So far, this looks like a very neat story of discovering experimental evidence for an intuitive theoretical idea and then finding a simple way to fit the data. But, the brain never makes it that easy on us.

As well as finding the shape that Hebb would have predicted, we also find the opposite, as well as synapses that get stronger if the spikes are close in time regardless of sign, and so on.

```{figure} figures/stdpPicture4.png
:align: center
:width: 300px
```

With that said, the pre before post shape is the one we're normally talking about, and that what we'll focus on in this section.

## Modelling STDP

So how do we model this? The simplest thing would be just to start with the single pair weight change:

$$\Delta w = F(\Delta t) =  
\begin{cases}
    A_+ e^{-\frac{\Delta t}{\tau_+}} \quad \text{if } \Delta t > 0 \\
    A_- e^{+\frac{\Delta t}{\tau_-}} \quad \text{if } \Delta t < 0
\end{cases}$$

Then sum the weight change over all spike pairs.

$$w \leftarrow w + \sum_i \sum_j F(t_j^\post - t_i^\pre)$$

The issue is that this is computationally expensive. Suppose we have $N$ neurons all-to-all connected to $N$ neurons, and each neuron fires around $R$ spikes. In that case, computing this sum takes around $N^2$ $R^2$ operations, and each operation contains a call to the exponential function which is itself very heavy.

There is a trick that can simplify this, using the fact that it's exponentials and linear sums. For each pre-synaptic neuron we introduce what is called a **trace variable**, $a_{\pre}$. We do the same with the post-synaptic neurons and a trace variable $a_{\post}$.

We update them according to the following rules. In the absence of a spike, they decay exponentially like we've seen in the leaky integrate-and-fire neuron. The pre-synaptic trace with time constant $\tau_+$ and the post-synaptic with time constant $\tau_-$.

```{math}
\begin{aligned}
\tau_+ a_\pre' &= -a_\pre \\
\tau_- a_\post' &= -a_\post \\
\end{aligned}
``` 

When a pre-synaptic spike arrives:

```{math}
\begin{aligned}
a_\pre &\leftarrow a_\pre + A_+ \\
w &\leftarrow w + a_\post \\
\end{aligned}
``` 

And similarly for a post-synaptic spike arrives but with pre and post swapped and + and - swapped. 

```{math}
\begin{aligned}
a_\post &\leftarrow a_\post + A_- \\
w &\leftarrow w + a_\pre \\
\end{aligned}
``` 

:::{tip} Exercise for the reader
It's a good exercise to work out why this gives the same result. Try checking what happens for a single pair of spikes first, say a pre-synaptic spike at time 0 and a post-synaptic spike at time $\Delta t > 0$. Draw a plot of what happens to both $a_\pre$ and $a_\post$ and at each spike time, what happens to $w$. Now do the same when the post-synaptic spike happens first. This gives you the single pair weight change equation at the top, and if you use the fact that all equations are linear to sum over all spike pairs, you get the all pairs weight change rule.
:::

Computationally, we now only have to do $O(N^2 R)$ operations, and they're all arithmetic instead of exponential. This is essentially free because we had to this many operations anyway. For each spike at a pre-synaptic neuron, we have to add a value to each post-synaptic neuron it's connected to. There are $N$ neurons affected by each spike, and there are $NR$ pre-synaptic neurons, so that's $N^2 R$ operations we would have had to do anyway.

One thing that's worth pointing out is that the models slightly differ from the experimental data here. In the experimental data, it takes around an hour before you see the weight change, with it slowly increasing over time. With the first implementation, we could run the weight change rule an hour after the spikes we want to learn, but even that doesn't match the slow change you see in experiments. With the second implementation, it's automatically updated immediately after each spike.

It's also worth noting that there are some subtleties with delays here. You should do a slightly different thing depending on whether the delays are dendritic, axonal, or some combination of the two. In any case, the experimental data is less clear about delays, so it's not even obvious that there is a right thing to do here.

## Latency reduction

Now that we know how to model STDP, let's start taking a look at the things it can learn. Let's start with a result from {cite:t}`https://doi.org/10.1038/78829`.

We set up a model with a layer of input neurons connected to a single output neuron, with STDP on the synaptic weights. Now, we make those input neurons all fire a burst of spikes for 20 ms but with a random latency, and look at what the network learns.

```{figure} figures/stdpPicture5.png
:align: center
:width: 700px
```

What we see is that the neurons with a low latency have their synaptic weights strengthened to the maximum level, and those with a high latency drop to zero. The output neuron now spikes earlier than before learning. One way of interpreting this would be that if there were multiple sources of information available to an animal, it would respond preferentially to the one that arrived earliest. This would clearly be advantageous in an environment where a short delay in responding could be the difference between life and death for an animal being hunted.

```{figure} figures/stdpPicture6.png
:align: center
:width: 700px
```

## STDP learns correlated groups

In this next experiment {cite:p}`https://doi.org/10.1016/S0896-6273(01)00451-2`, we divide input neurons into two groups. One group with the white background are firing uncorrelated spikes. The other group with the blue background are firing correlated spikes.

```{figure} figures/stdpPicture7.png
:align: center
:width: 700px
```

After STDP, the weights of the neurons in the uncorrelated group go to zero, while the weights in the correlated group go to the maximum value. This makes sense biologically because being able to pick up on input correlations in your environment is very useful.

```{figure} figures/stdpPicture8.png
:align: center
:width: 300px
```

We can do the same thing but where both groups of neurons are correlated with other neurons in their group, but not with neurons in the other group. We find that STDP will learn to pick one of the two groups, but which one is random. In other words, STDP has some aspects of competition in what it learns. You can think of this as a bit like the [selectivity](#BCM-selectivity) we talked about with the [BCM rule](#BCM-Rule) in the previous section.

## STDP finds the start of spike sequences

The next model {cite:p}`https://doi.org/10.1371/journal.pone.0001377` combines the ideas from these two previous papers to learn to respond to repeated sequences of spikes. We have the same setup with a group of neurons connected via STDP synapses to a single output neuron. In this case, the input is random, uncorrelated spikes that have repeated patterns repeated at random times. The [blue dots](#dotted-graph) are the uncorrelated spikes, and the [red dots](#dotted-graph) are the repeated patterns.

```{figure} figures/stdpPicture9.png
:label: dotted-graph
:align: center
:width: 400px
```

What happens [here](#output-neuron-traces) is that the output neuron initially fires at random times, but eventually learns to fire only when the spike pattern is present. In fact, it learns to fire at the start of spike pattern

```{figure} figures/stdpPicture10.png
:label: output-neuron-traces
:align: center
:width:500px
```

If you look more carefully, at first it learns a random subset of the neurons in the spike pattern group that happen to fire at the same time. This is the correlation and competition mechanism from the previous slide, and it leads to the output spike happening at some random point in the sequence: could be early, could be late. Then, STDP's preference for lower latency kicks in, and it shifts towards the neurons in the pattern that fire earlier, until it reaches the beginning.

```{figure} figures/stdpPicture11.png
:align: center
:width: 400px
```

A later study {cite:p}`https://doi.org/10.1016/j.neuron.2010.04.014` found that humans can indeed learn random patterns in noise. Specifically, what they did was to play pairs of bursts of random noise to human listeners, and they had to indicate whether it was the same noise burst repeated, or two different ones. Unknown to them, sometimes the repeated bursts of noise were the same as ones they had heard before. The result was that listeners learnt to recognise repeated bursts of noise more accurately for the ones they had heard before than for fresh bursts of noise, even though all the bursts were entirely meaningless samples of white noise.

## STDP learns spatial maps

In week 2's [network models](#spatial-structure), we saw the example of a spatial map in the barrel cortex of the rat. As a reminder, that's the system that processes inputs from the rat's whiskers. What we saw is that these neurons have a preference for motion in a particular direction, and that preference has a spatial structure.

```{figure} figures/stdpPicture12.png
:align: center
:width: 400px
```

To model this {cite:p}`https://doi.org/10.1523%2FJNEUROSCI.6541-10.2011`, we used a slightly more complicated setup than the previous studies, designed to match the structure of layers 2 through 4 of the barrel cortex. The input stimuli were waves of activity that moved across the whiskers in a linear pattern, with the direction chosen randomly each time.

```{figure} figures/stdpPicture13.png
:align: center
:width: 500px
```

This input structure together with the STDP synapses was enough for the model to learn a very similar input selectivity map to what was recorded in the rats.

```{figure} figures/stdpPicture14.png
:align: center
:width: 200px
```

## Issues with STDP

So STDP is pretty cool and can learn some fun stuff, but there are some problems too.

### Saturation

We saw in the section on rate-based models that without some careful control, weights tend to go to the extremes and earlier in this section you could see the same thing happening with STDP – the weights all push towards the maximum or minimum allowed values.

```{figure} figures/stdpPicture15.png
:align: center
:width: 300px
```

This doesn't match what you see experimentally.

```{figure} figures/stdpPicture16.png
:align: center
:width: 400px
```

You can fix this with a noisy, multiplicative version of the STDP learning rule {cite:p}`https://doi.org/10.1523/JNEUROSCI.20-23-08812.2000`, although it's harder to get strong competition and specialisation with this form of STDP.

### Bidirectional pairs

Another issue is that STDP will always force synaptic connections between neurons to be unidirectional. You can't have a situation where A excites B and B excites A. However this is common in cortex.

```{figure} figures/stdpPicture17.png
:align: center
:width: 400px
```

You can fix this with a more complicated STDP rule that takes voltage into account {cite:p}`https://doi.org/10.1038/nn.2479`, and the properties of these sorts of models are still being investigated.
It is structurally similar to the BCM rules we saw in the last section, and also gives rise to selectivity and something like independent component analysis.

### Stability across time scales

A more fundamental problem that has not yet been solved is how to handle stability of learning across multiple time scales. {cite:t}`https://doi.org/10.3389%2Ffncom.2015.00138` has a nice review of many models from a dynamical systems point of view, showing that when you combine different forms of learning at different time scales, all sorts of problems start to occur like oscillations that disrupt memories.

In general, we know that it's important to combine multiple time scales because we want to be able to learn fast and forget slowly, but this seems to be hard to achieve. This is related to the similar problem of catastrophic forgetting found in machine learning, and solutions to this problem in either neuroscience or machine learning may prove helpful in the other field.

### Other issues

This isn't a complete list of all the issues with STDP, and if you're interested in taking it further {cite:t}`https://doi.org/10.3389/fncom.2010.00019` has quite a nice review of some of the problems and attempted solutions.

## Other forms of learning

OK, that's the end for this week on learning rules. There's a lot more stuff that could be covered here, and that we might add later.
In case you're interested, there are some key words to look up to take it further.

This includes **reward modulated STDP** that lets you combine STDP with rewards to learn more interesting tasks.

There's **reservoir computing** that applies a linear readout layer to a random network with or without STDP, and turns out to be capable of learning arbitrarily complex functions with some assumptions.

There's the famous **Hopfield network** and associative memory.

Of course, there's **reinforcement learning**.

And many, many more!