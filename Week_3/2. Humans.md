# Humans

[Download the slides here](slides/W3-V1-humans.pptx)

:::{iframe} https://www.youtube.com/embed/Mf9Qco49p4Q
:width: 100%
:::
---

## Introduction

In the last section we discussed connectomes - full diagrams describing how every neuron connects to every other neuron in an animal.

:::{attention} Note!

We don't have a connectome for the human brain so instead, in this section, we’ll look at a more macro level
:::

## Glial Cells

First, let's point out something we've not mentioned yet - neurons may only be some of the story, as there are other cells in the brain too.

These are known as **Glial cells**. In humans it's estimated that the ratio of neurons to Glial cells is roughly 60 to 40 and there are many different types of Glial cells. 

So what are they for?

Historically, it was thought they mainly played supporting roles. For example:
* **Oligodendrocytes**, [shown in purple](#glialdiagram), form the fatty mileage sheaths around absons
* **Microglia**, [shown in blue](#glialdiagram), act as immune cells by removing dead cells and responding to pathogens

But it's increasingly thought that Glial might play computational roles too. For example **Astrocytes**, which have star shapes, [shown in green](#glialdiagram), both detect and respond to neural activity through multiple mechanisms like:

* Modulating the concentration of potassium around neurons
* Or even using their own neurotransmitters known as glial-transmitters

```{figure} figures/glial.png
:label: glialdiagram
:alt: Diagrams of Different Types of Glial Cells
:align: center

Diagrams of Different Types of Glial Cells (See [paper](https://doi.org/10.1002/glia.24343))
```

## Human Brain

Putting it all together, the human brain is thought to be composed of roughly **86 billion** neurons, **50 billion** Glial cells and **$\approx10^{15}$** synapses - though this is hard to estimate.

At a more macro view, [these figures](#brains), show the growth structure of the brain from a lateral view, as if you're looking at someone from their left hand side.

The diagram on the left shows the brain's outer surface and there are a few interesting features to note.

```{figure} figures/sections.png
:label: brains
:alt: Lateral View and Sagitall Section of Brain
:align: center

Lateral View and Sagitall Section of Brain
```


The outermost layer, known as the cortex, has a folded strucutre which increases its surface area relative to brain volume.

Folds are known as **gyri** and depressions are known **sulci.**

Larger depressions or fissures divide the brain into 4 major lobes.

* Frontal
* Temporal
* Parietal
* and Occipital

The Cerebellum, which we discussed at a micro level in week 2, sits at the back. Then at the base there is the brain stem, which connects the brain to the spinal cord. 

What you can't see in the diagram is that the brain is divided, left and right, into 2 hemispheres. They are largely symmetric and connected across the midline by a structure known as the corpus callosum.

Finally [the diagram on the right](#brains) shows a sagitall section, as if we've divided the 2 hemispheres and then removed the left.

Taking a different view, if we now imagine looking at someone front on and then taking a perpendicular slice through the brain we get a coronal section, which would look something like [this](#coronal).

```{figure} figures/coronalsection.png
:label: coronal
:alt: Diagram of Coronal Section
:align: center

Diagram of Coronal Section
```

The outermost layer, the cortex, is also referred to as gray matter as it mainly contains dendrites, soma, synapses etc which are unmelinated. However, below there are tracks where melinated axons tend to travel, which are known as white matter. For example the corpus callosum, which we just mentioned.

Deeper in the brain, there are then various nuclei which are clusters of neurons.

## The Nervous System

Zooming out one last time, the entire nervous system is composed of two parts:

1. The **central nervous system** - which consists of the brain and the spinal cord
2. and the **peripheral nervous system** - which carries information in two directions

```{figure} figures/nervoussystem.png
:label: nervous
:alt: Diagram of Nervous System
:align: center

Diagram of Nervous System 
```
[Sensory neurons](#sensory) convert external stimuli to spikes and then carry these signals to the spinal cord and the brain. For example, neurons which sense pressure, have specialised channels known as **Piezo channels**, which can physically deform in response to pressure. This allows ions to flow into the neuron and then trigger action potentials.

```{figure} figures/sensoryneuron.png
:label: sensory
:alt: Diagram of Sensory Neuron
:align: center

Diagram of Sensory Neuron
```

In the other direction, [motor neurons](#motor) synapse with muscles, at what is known as the neuromuscular junction, where they use a special neurotransmiter **acetylcholine** to link neural activity to movement.

```{figure} figures/motorneuron.png
:label: motor
:alt: Diagram of Motor Neuron
:align: center

Diagram of Motor Neuron
```

## Localisation of Function

Going back to the brain, we often think about specific functions as being localised to specific brain areas.

For example, we call part of the occipital lobe the visual cortex, and part of the temporal lobe the auditory cortex.

We have 3 types of data that seem to support localisation of function

1. We observe neural activity in response to stimuli or during tasks we see that neurons with similar responses tend to be co-located. For example, in [this study](https://doi.org/10.1093/brain/123.2.291) human participants were asked to read words presented to a single eye while their brain activity was imaged using a technique known as {abbr}`FMRI(Functional Magnetic Resonance Imaging)`

    The top row of [the figure](#brainactive) shows left and right views of the brain with the inferred amount of neural activity overlaid in red in response to words presented to either the left or the right eye. So you can see that this task activates some localised areas.

```{figure} figures/brainactivity.png
:label: brainactive
:alt: Left and Right Views of Brain Activity to Stimuli
:align: center

Left and Right Views of Brain Activity to Stimuli (See [paper](https://doi.org/10.1093/brain/123.2.291))
```

2. If we manipulate specific parts of the brain, we see specific differences in behavior. So while we do these manipulations in animal models, in humans these manipulations usually result from accidents, disease processes or surgical interventions.

    For example, in the [same paper](https://doi.org/10.1093/brain/123.2.291), the authors also studied two patients who had damaged part of their corpus callosum. These patients lost relatively specific function and in this study, they were unable to read words aloud that were presented in the left visual field.

    The 2 rows of patient imaging data show how their neural activity differs from controls, and so reflects this behavioral difference. Specifically in control subjects, a region was activated irrespective of the simulated Hemi field, which is shown by [the green circle](#brainactive), but in patients it was only activated by right field stimulation.

:::{attention} Note!
**We'll cover observing and manipulating neural activity in more detail later in the course**
:::

3. If we look at the brain structure we see that connectivity tends to be sparse and modular, which could seem to imply that individual modules may perform speciallised functions. 

:::{hint} Linker
But is that necessarily the case? In other words, will a network with a modular structure necessarily modulise function? 
:::

## Structural vs Functional Specialisation

[This paper](https://doi.org/10.48550/arXiv.2106.02626) uses a really neat setup. As a model they use artificial neural networks composed of modules where each module is a recurrent neural network with dense connections internally but sparse connections to other modules.

As a task, the input are pairs of digits and train networks to output a parity based label, which means that networks could solve the task by using a modular solution, where each module would learn to recognise its own input digit. To measure functional specialisation they use three different metrics

One of which is a manipulation based metric, where they silence each module in turn and see how that affects the network's ability to recognise each digit. So a module would be defined as specialised if silencing it decreases the classification accuracy for one digit but not the other.
With that setup, they then vary the amount of connectivity between the modules labelled as $p$ on the [left diagram](#strucvsfunc) and measure functional specialisation.

One of their main results is shown on the [right figure](#strucvsfunc), where the x-axis is the amount of inter module connectivity $p$, and the y-axis is the level of functional specialisation. What you can see is that when $p$ is low, so inter module connectivity is sparse, functional specialisation is high. But as $p$ increases, specialisation decreases very rapidly.

```{figure} figures/strucvsfunc.png
:label: strucvsfunc
:alt: Study Results for Localised Function
:align: center

Study Results for Localised Function (See [paper](https://doi.org/10.48550/arXiv.2106.02626))
```

This suggests that only networks with extremely sparse inter module connections become functionally specialised, and together with other evidence, this paper adds to a growing picture that function in the brain may be less localised than previously thought.

:::{seealso} That's it!
Ok, that's all for this week, next week we will cover learning rules in both machine learning and neuroscience
:::