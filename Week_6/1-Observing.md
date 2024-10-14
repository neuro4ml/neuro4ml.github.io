---
authors: ghosh
---

# Observing

[Download the slides here](slides/W6-V0-observing.pptx)

:::{iframe} https://www.youtube.com/embed/c8JCgX5JPBw
:width: 100%
:::
---

```{danger} Work in progress
The text below has been transcribed by hand from the video above but has not yet been reviewed. Please use the videos and slides as the primary material and the text as support until I have a chance to proofread everything. When I have done this, I will remove this message.
```

## Introduction

This week we're going to explore how researchers try to **understand** neural networks. 

:::{note}
In this section we'll think about what it actually means to understand neural networks.
:::

## Understanding

Again, we can think of both artificial neural networks and brains as computing input-output transformations: from sensory stimuli to behavior.
But how do all of a networks features; like its unit properties, architecture and activity patterns combine to implement these transformations?
Both neuroscientists and machine learning researchers are interested in this question for a variety of reasons like: using knowledge from biology to improve artificial neural networks or building brain-machine interfaces – which record neural activity and use it to control various devices.
This section will cover three topics related to understanding neural networks – how to:
* Observe neural activity,
* analyze it, and 
* manipulate it. 

So how can we observe activity?

In an artificial neural network it's as simple as running a forward pass through the network and calculating the unit activations.  
In a spiking neural network we can pass spikes into the network and record its hidden unit's: membrane voltages or spiking outputs.

But in a biological system we need to choose how we're going to record neural activity.

```{figure} figures/sensorimotor.png
:label: SensTransformation
:alt: Example ANN
:align: center
```

## Recording neural activity

There are different methods for recording neural activity, with different pros and cons, but one way to describe them is on a 2d axis where: 

* The x-axis – shows the methods temporal resolution 
* The y-axis – shows the number of neurons the method can record simultaneously

```{figure} figures/observingPicture1.png
:label: axis
:align: center
:width: 500px
```

In previous sections we introduced electrophysiology <!-- find a link to this --> – a technique which lets you record a single neuron with high temporal resolution, so that would sit [here](#axis) on this graph.  

To get data from more neurons, with this approach, studies will often record different neurons sequentially over trials and then pool the data over trials and subjects. But even so, you're often limited to tens to hundreds of neurons and pooling them in this way isn't ideal. 

So how can we record more neurons simultaneously? 

## Neuropixels

One approach is to use high-density probes, like the Neuropixels probe shown in [panel c](#neuropixels-pic).
The main part of this probe is its extremely thin shank, which is inserted into the brain surgically, and is covered with hundreds of recording sites; which are shown schematically in [panel A](#neuropixels-pic) and in a microscope image in [panel B](#neuropixels-pic).

```{figure} figures/observingPicture2.png
:label: neuropixels-pic
:align: center
:width: 700px

High-density probes results for simultaneous neuron recording {cite:p}`https://doi.org/10.1126/science.abf4588`.
```

These sites record nearby electrical activity, and so each sites' signal results from the combined activity of many neurons. However, from this data it's possible to infer the underlying spiking activity of individual neurons, using spike-sorting algorithms which make use of the fact that different neurons have distinct spike shapes. 
And so, these probes allow you to record from hundreds to thousands of neurons simultaneously. For example, in the raster plots on the [right](#neuropixels-pic):

* The y-axis shows the recording sites along the shank
* The x-axis shows time in seconds
* The colored blocks show different trials

These probes represent a huge technological advance. But, it's worth keeping in mind that they still only let us record from a small fraction of all neurons in the brain. For example, they're often used in mice which have around 70 million neurons. And even if we can record 1,000 neurons simultaneously, that's still only around 0.001% of the whole brain.

So how can we get more comprehensive coverage? 

## Calcium imaging

One alternative is calcium imaging. 
Remember that when an action potentials reaches the axon terminal, it causes voltage-gated calcium channels to open, and calcium to flow into the cell. So we can use changes in calcium concentration to infer neural activity. 

Using this approach we can measure neural activity in roughly 4 steps:

* First, we need a calcium indicator – something which changes its fluorescence in the presence of calcium.
* Second, we need to put this indicator inside neurons, and we can do this by injecting them, or by genetically modifying them so that they produce the indicator themselves. 
* Third, we need to put our specimen under a microscope and measure each neurons change in fluorescence over time.
* Then finally, we can analyze either the continuous changes in fluorescence or we can infer the underlying spikes using deconvolution algorithms.  

The end result of this is shown [here](#calcium-imaging-pic) in a larval zebrafish. The brain is shown from a top-down (or dorsal view), as well as from the front and side. Brain structure is shown in grey, and then calcium activity is overlaid in red-yellow, and the video is played at about 20 times real-time.

:::{figure} figures/Media1.mp4
:label: calcium-imaging-pic
:width: 500px

Calcium activity of larval zebrafish {cite:p}`https://doi.org/10.3389/fncir.2013.00065`.
:::

While you can use this technique in any animal model, it works particularly well in larval zebrafish as they’re small, and transparent - which, we're not - so how can we image neural activity in humans?  

## fMRI

In humans, one of the most commonly used methods is functional magnetic resonance imaging or fMRI: which measures changes in blood flow to regions of the brain. As more active areas require more oxygen, and vice versa, this serves as another proxy for neural activity. 

fMRI is widely used as it is: 

* Non-invasive – i.e. we don’t need to implant electrodes or inject dyes into subjects.
* It provides whole-brain information.
* It has reasonable spatial and temporal resolution on the order of millimeters and seconds.  

Though it is worth keeping in mind that it's a proximy measure of neural activity, and even though the spatial resolution is on the order of millimeters, each cube or voxel (the smallest measured spatial unit in fMRI) will still contain around 1 million neurons.

```{figure} figures/observingPicture4.png
:label: fmrifig
:align: center
:width: 400px

fMRI Benefits and Challenges {cite:p}`https://doi.org/10.1038/s41586-023-06670-9`.
```
Okay, stepping back a bit, lets approximately map these techniques onto our axis.

## Recording neural activity

* High-density probes like Neuropixels have high temporal resolution and can record from hundreds to thousands of neurons.
* Whole-brain calcium imaging has medium temporal resolution, but can record from thousands to tens-of-thousands of neurons.
* fMRI has slow temporal resolution. Though it's hard to place in terms of number of neurons as it records a whole-brain signal, but can’t resolve individual neurons.

```{figure} figures/observingPicture5.png
:align: center
:width: 600px
```

Before moving on, there are 2 more things we need to mention.

Firs, there are many other methods to record neural activity, such as:

*  EEG – uses external electrodes to measure the brains electrical activity.
* Voltage imaging – uses indicators whose fluorescence changes with the neuron's membrane potential. It's thought that voltage indicators will be the next big thing in neuroscience, and if you'd like to learn more Mark Humpheries has a great blog post on that (which is in this week's reading material).

Second, there are lots of other things to consider beyond a methods temporal and spatial resolution. For example, most neural recordings are done on static subjects, fixed under a microscope or lying in a scanner, but there is increasing interest in methods which allow neural activity to be recorded during free movement.

:::{seealso} That's it!
Okay, so hopefully that gave you some insight into how we can record neural activity. 

In the next section we'll think about how to analyze or interpret that data.  
:::
