# Networks

[Download the slides here](slides/W2-V2-networks.pptx)

:::{iframe} https://www.youtube.com/embed/YjXSh14rV08
:width: 100%
:::
---

```{danger} Work in progress
The text below has been transcribed by hand from the video above but has not yet been reviewed. Please use the videos and slides as the primary material and the text as support until I have a chance to proofread everything. When I have done this, I will remove this message.

-- Reviewed for style by Dan, not yet reviewed for accuracy by Marcus.
```

## Introduction

In the [last section](#synapses-2) we covered how neurons signal and synapse with each other. 

:::{note}
In this section we will go through how neurons connect together to form networks and circuits.
:::

## Circuit mapping

To do this, we will go through two examples of networks found in biology.

But first, **how do neuroscientists map biological circuits?**

Biological circuit mapping is essentially done by dissecting an animal's brain, slicing it very thinly, staining it, viewing it under a microscope and then analyzing the images. Historically this was all done by hand, like the drawing from Cajal in 1894 [shown below](#dissecting), but everything from the data acquisition to the analysis is now being automated.

```{figure} figures/NetworksPicture1.jpg
:label: dissecting
:width: 100%
:align: center

Cajal's drawing on the left and modern techniques on the right.
```

## The cerebellum

The cerebellum is an area of the brain involved in coordinating movement, and is composed of circuits which look something like this diagram:

```{figure} figures/NetworksPicture2.svg
:label: cerebellum-pic
:width: 100%
:align: center

The cerebellum ([source](https://commons.wikimedia.org/wiki/File:Cerebellar_Circuit.svg)).
```

There are 3 points to note here:

* The cells are found in three layers, which are labelled on the left of the [image](#cerebellum-pic)
* The major cell types are labelled, like the Granule and Purkinje
* The connections between the different cell types are marked as either excitatory or inhibitory, depending on what [neurotransmitters](#neurotransmitters-paragraph) they use

So how does information flow through this network?

In the mossy fiber pathway [(on the right of the image)](#cerebellum-pic) inputs synapse with granule cells. These then send their outputs up into the layer above where they branch out and spread through the Purkinje cell dendric ardors, forming thousands of excitatory synapses. Because of their structure, these granule cell outputs are known as parallel fibres. The Purkinje cells then send inhibitory connections to the circuit's outputs - the deep cerebeller nuclei.
In a second pathway [(on the left of the image)](#cerebellum-pic), the climbing fibre pathway, other inputs skip straight to the Purkinje cells.

As you can also see from the [image](#cerebellum-pic), there are other interesting connections in the circuit too, such as the direct connections from both input pathways (the mossy and climbing fibres) to the outputs (the deep cerebellar nuclei).

So how can we think about or model this network's function? 

Models have been propsed for over 50 years, but one simplified way to think about it is as a three-layer network with the two pathways we just discussed. Inputs arrive via the mossy fibers, which connects to both the first and third layers, the granule and nucleus cells. Then there are feedforward connections through network and the third layer generates output predictions. These outputs are then compared to input observations, and the difference between the two is fed back to the network as an error signal, via the climbing fiber pathway (shown in red in the [images below](#networks-model)).

```{figure} figures/NetworksPicture3.jpg
:label: networks-model
:width: 100%
:align: center

Model of cerebellar function {cite:p}`https://doi.org/10.1152%2Fjn.00449.2020`.
```

:::{seealso} For more
:class: dropdown
[](https://doi.org/10.1152%2Fjn.00449.2020) focuses on an interesting question, which is how the network can learn, when the connections conveying the error signal to the third layer are relatively weak (shown by thin red arrow).
:::

## Head direction cells

Around thirty years ago researchers were recording the activity of individual neurons in a rat brain, when they discovered cells which seemed to encode the animals heading direction, and so named them head direction cells. 

Here for example, are three neurons firing rates (in spikes per second) as a function of the animals head direction. You can see that each neuron is very specific.

```{figure} figures/NetworksPicture4.png
:label: firing-rates
:width: 100%
:align: center

Firing rates of neurons as a function of head direction {cite:p}`https://doi.org/10.1523/JNEUROSCI.10-02-00420.1990`.
```

This and later work would show that, as a population, neurons evenly tile the space of heading directions, and the activity of these cells depends on both visual and vestibular (balance) inputs.

(ring-attractor)=
## Ring attractors

One team proposed an attractor model in 1995, which they drew as a series of rings, with the head direction cells in the outer ring, and visual and vestibular inputs in the inner rings.

```{figure} figures/NetworksPicture5.png
:label:
:width: 300px
:align: center

Attractor model proposed in 1995 ([Skaggs et al. 1995](https://pubmed.ncbi.nlm.nih.gov/11539168/)).
```

There are lots of details, but the most salient one is that there are strong excitatory connections between neighboring head direction cells, and strong inhibitory connections between distant cells. This means that there will be just one cluster of active cells at any time, and either visual or vestibular inputs to the head direction cells will cause the peak to shift around the ring. 

Interestingly they thought of this as a somewhat abstract model, and wrote: 

> "It is helpful to think of the network as a set of circular layers; this does not reflect the anatomical organization of the corresponding cells in the brain"

Though recently experiments in fruit-flies revealed a group of neurons arranged in a ring, with a single bump of activity which tracks the fruit-flies heading direction.

[](https://doi.org/10.1038/nature14446) essentially have a fruit-fly walk on a rotating treadmill while the fly watches a screen with landmarks on – the blue ring in panel A on the [figure below](#fruit-flies-experiment).
They're also recording the fly's brain activity, which is shown in the black and red boxes [below](#fruit-flies-experiment). The recording technique used will be explained later in the course, but for now it's enough to know that red indicates more neural activity. What you can hopefully see is that there is a single bump of activity, which rotates around the ring as the fly navigates around. The panels below, [D and E](#fruit-flies-experiment), show that this bump accurately tracks the flies heading direction.

```{figure} figures/NetworksPicture6.png
:label: fruit-flies-experiment
:width: 100%
:align: center

Evidence for the ring attractor model in fruit flies {cite:p}`https://doi.org/10.1038/nature14446`.
```

To conclude, [ring attractors](#ring-attractor) are a nice example of where experiments and theory came full circle.

:::{seealso} That's it!
In the next section, we will turn the detailed biology from this section into relatively simple equations
:::