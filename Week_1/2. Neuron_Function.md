# Neuron Function

[Downlod the slides here](W1-V1-function.pptx)

:::{iframe} https://www.youtube.com/embed/sYjx3VSaAME
:width: 100%
:::
---
## Introduction

Last week we saw that neurons act as information processing units. To do so, they use both chemical and electrical signals:

* Neurons receive chemical input signals, known as neurotransmitters, at their dendrites. 
* Transform this into an electrical signal, 
* And then output their own neurotransmitters to other neurons via their axon terminals. 

:::{attention} Note!
**We'll cover chemical signaling next week, and focus on the electrical part today.**
:::

```{figure} neurondiagram.png
:label: neuron
:alt: Diagram of Neuron
:align: center

Diagram of Neuron
```
:::{hint} Linker
So let's start by looking at the electrical activity of a single neuron. 
:::

## Single Neuron Recordings

In [Figure 2](#recordings) (from [this paper](https://doi.org/10.1152/jn.00408.2011 
)) we have time in ms on the x-axis and voltage in mV on the y-axis.

Exactly how researchers acquire this sort of data depends on several factors, but in general you need an electrode, an amplifier, and a specimen to record from, like an isolated neuron in a dish or even a human brain during surgery.

```{figure} singleneuronrecord.png
:label: recordings
:alt: Single Neuron Recording Graph
:align: center

Single Neuron Recording
```

From this sort of plot, we can observe two features: 

* First, there are these high amplitude, 1-2ms long events, which we call action potentials or spikes. 
* Second, between the spikes, the neurons voltage fluctuates around a baseline value, which we call the resting membrane potential.

:::{hint} Linker
So, how do neurons generate their resting potential and spikes? 
:::

## Resting Membrane Potential

The neuron's cell membrane plays a key role in generating resting potential and spikes. In [Figure 3](#restingpotential), the cell membrane seperates the inside of the cell from the outside.

Importantly, ions (charged particles), like sodium and potassium are unevenly distributed across the membrane. For example:
* K+ is at higher concentrations inside 
* Na+ and chloride are at higher concentrations 

This means that there are both chemical and electrical gradients across the membrane.

```{figure} restingmembrane.png
:label: restingpotential
:alt: Resting Membrane Potential
:align: center

Diagram Showing the Resting Membrane Potential and Ionic Movements
```

However, these charged ions can't cross membrane directly, and instead must use specialized channels – which are proteins embedded in the membrane.
* Some of these channels are **passive** – so simply allow ions to diffuse across, 
* But others are **active** and use energy to transports ions **against** their chemical gradient. 

The overall result of these ionic movements, is that each ion balances at an equilibrium: where it's concentration gradient equals its electrostatic gradient. And this is known as it's equilibrium or Nernst potential. 

Then the resting potential of the membrane is the sum of all of the equilibrium potentials, which is usually somewhere around –70mV. 

:::{important} Take-away
So the resting potential is the neurons state when it has little or no input. 
But when it receives inputs it can generate spikes.  
:::

:::{seealso} For more
:class: dropdown
Check out [this video](https://www.youtube.com/watch?v=hk09AkV5_Kc) on resting membrane potential
:::

## Spikes

[This schematic](#spike) shows time on the x-axis (on the order of a few ms) and the neuron's membrane potential on the y-axis in mV. 

1. Inputs cause Na+ channels, in the membrane, to open and Na+ to flow into the cell down it's concentration gradient. This raises the membrane potential, and if it rises high enough (past what is termed the gate threshold), voltage gated Na+ channels open, more Na+ flows in, and the membrane potential rises to it's peak. This process ic termed depolarisation. 
2. At high voltages, Na+ channels close 
3. But, K+ channels open – and potassium flows out of the neuron, reducing the membrane potential
4. This is called repolarization 
5. Interestingly, repolarization typically goes below the resting membrane potential, here going as low as –90mV. This is called hyperpolarization and it's effect is to effectively raise the threshold for new stimuli to trigger a spike for a period of time, which we term the refractory period. 
6. After hyperpolarization, a combination of active and passive ionic movements eventually bring the membrane back to its resting state of -70 mV.

:::{note} Note
Part of the importance of hyperpolarization is in preventing any stimulus already sent up an axon from triggering another action potential in the opposite direction. In other words, hyperpolarization assures that the signal is proceeding in one direction
:::

```{figure} spikes.png
:label: spike
:alt: Spike Schematic
:align: center

Spike Schematic
```

Given the complexity of this process, you may expect that spikes would be noisy, but if we return to our [single neuron recording](#recordings), we see that they are remarkably stereotypical in their shape. If we overlay, we see that the spikes from a given neuron all look alike.

For example  see [this figure](#allornothing). On the left we see more than 100 spikes recorded from a real neuron, in response to different inputs.

What this means is that each neuron's spikes are essentially binary, all or nothing responses, and if neurons need to encode more complex information they must do so by varying the number or timing of their spikes. But, we'll return to how neurons encode information later in the course. 

As each neuron's spikes are so similar, sequences of spikes or spike trains are often plotted as binary events. For example, on the right is what we call a raster plot, and each row shows the spiking activity of a different neuron over time. 

```{figure} allornothing.png
:label: allornothing
:alt: Spike Variation
:align: center

Spike Variation and Raster Plot
```

:::{seealso} For more
:class: dropdown
Check out [this video](https://www.youtube.com/watch?v=BbUcWbtVjT4) and [this website](http://hyperphysics.phy-astr.gsu.edu/hbase/Biology/actpot.html#c5) on neuron action potential
:::

:::{hint} Linker
But, while each neurons own spikes share the same shape, not all neurons are alike in their dynamics. 
:::

## Membrane Time Constant

Let's think about injecting input current into a neuron. 

If we inject enough current we'll raise it's membrane potential past it's gate threshold, and cause it to spike. 

But if we inject just a small amount of current transiently, then it's membrane potential will increase and then decay back to rest. We term how long this decay takes, the **membrane time constant**, and it has been observed experimentally that different neurons differ in this value. So decay at different rates. And that is what is plotted [here](#timeconstant) for two example neurons, though these are simulated. 

```{figure} timeconstant.png
:label: timeconstant
:alt: Membrane Time Constant
:align: center

Measured Membrane Time Constant for Two Example Neurons
```

While this may seem far from machine learning, using neural network models to explore what role these features play in computation, or using biological features to boost performance are both exciting prospects. 

Just to give you one example along these lines. In [this paper](https://doi.org/10.1038/s41467-021-26022-3) Dan and colleagues, built neural networks with heterogenous membrane time constants and showed that this lead to improvements in task performance.

But how do you make artificial units with different membrane time constants?

:::{seealso} That's it!
In the next section we'll see how to model single neurons mathematically, and that will provide a foundation for later in the course when we cover how to build and train networks made up of spiking units. 
:::