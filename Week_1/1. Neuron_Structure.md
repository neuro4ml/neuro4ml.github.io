# Neuron Structure

[Downlod the slides here](W1-V0-structure.pptx)

:::{iframe} https://www.youtube.com/embed/WMRUZT9NljM
:width: 100%
:::
---

## Introduction

In this first week we're going to focus on neurons - which are thought to be the brain's primary processing units. We'll cover 3 topics:

* Neuron Structure
* Neuron Function, and
* How we can model neurons mathematically and simulate them in code

:::{attention} Note!
**This lecture focuses on Neuron Structure**
:::

## Sensorimotor Transformation

We can think of both artificial neural networks and brains as computing input-output transformations. That is, they take **input** data, and **output** decisions.

For example, a trained {abbr}`ANN(Artificial Neural Network)` may take in images and output classes. Similarly, a predator may use its senses, like vision and hearing, to distinguish prey from other animals. In neuroscience we call this the sensorimotor transformation.

```{figure} sensorimotor.png
:label: SensTransformation
:alt: Example {abbr}`ANN(Artificial Neural Network)`
:align: center

Example Sensorimotor Transformation
```


:::{hint} Linker
So, how do these systems compute these transformations?
:::

## Units and Neurons

In {abbr}`ANNs(Artificial Neural Network)`, these transformations are realized by units – which sum their weighted inputs and pass them through an activation function (like {abbr}`ReLU(Rectified Linear Unit)`). 

In brains, the equivalent are **neurons** which are not just simple points, but complex 3D structures, like the neuron shown [here](#neuronactivation). Just to give you an idea of scale, the human brain contains around 86 billion neurons. 

```{figure} activationvsneuron.png
:label: neuronactivation
:alt: Activation Function vs Neuron Diagram
:align: center

Activation Function vs Neuron
```

:::{hint} Linker
So, what makes up a neuron? 
:::

## Neurons

Like other cells, neurons have:

* A fatty membrane which separates their inner contents from their surroundings 
* Then inside, they are filled with a fluid known as cytoplasm 
* In their cytoplasam, you'll find things which are found in other cells, like a nucleus containing genetic material, which sits in their main cell body or soma.

But, unlike many other cells, neurons act as information processing units:

* They receive inputs from other neurons via their dendritic tree. [Shown on the left](#neuron). 
* Then they signal to other neurons, muscles or glands, via their axon. [Shown on the right](#neuron). 

```{figure} neurondiagram.png
:label: neuron
:alt: Neuron Diagram
:align: center

Diagram of Neuron
```
:::{note} Remember
While [this](#neuron) is the sort cartoon we often use, **neurons are actually incredibly diverse**
:::

For example, [this paper](https://doi.org/10.1038/s41586-020-2907-3
) made [detailed measurements](#paperresults) from over 1,000 neurons and concluded that there were around 70 types: which differ in several features – including their {abbr}`morphology(Size, shape and form)`, which you can see here. Some of the neurons in blue look like the diagram we saw on the previous slide, but others, like those in pink look quite different. 

```{figure} neurondiversity.png
:label: paperresults
:alt: Neuron Diversity Measurements
:align: center

Paper Findings of Neuron Diversity
```

While you might assume that this complexity was reserved for, or unique to, the human brain.

All of these 70 neuron types, and actually this whole study, is focused on just one part of the mouse brain.  

:::{hint} Linker
So, is this structural diversity just random, i.e. the result of noisy biological processes, or not?
:::

## Is Structure Random?

In [this paper](https://doi.org/10.1098/rspb.2018.2727 ), the authors analyse the {abbr}`morphology(Size, shape and form)` of over 10,000 real neurons from an open source dataset and show that they balance {abbr}`wiring costs(the amount of material needed to construct the branches)` and {abbr}`conduction delays(the distance between their cell body and their points of contact with other neurons)`.

```{figure} morphology.png
:label: randomstructure
:alt: Morphology of Neurons
:align: center

Analysis of Neuron {abbr}`Morphology(Size, shape and form)`
```

In [Figure 5a](#randomstructure), the black circles represent the soma or cell body of the neuron. The green circles, show the inputs, and the red circle shows a branch point. And what this figure shows is how it's possible to wire the connections from green to black in different ways. 

On the left, is the tree with the minimum {abbr}`wiring cost(the amount of material needed to construct the branches)`. 
In the middle is the tree with the minimum {abbr}`conduction delay(the distance between their cell body and their points of contact with other neurons)`. 
On the right is an intermediate tree. 


Now, as these two objectives, minimizing {abbr}`wiring cost(the amount of material needed to construct the branches)` and {abbr}`conduction delay(the distance between their cell body and their points of contact with other neurons)`, compete with each other, they form a Pareto front, where improving one leads to a loss in the other. This is shown in [Figure 5b](#randomstructure), where the x axis is the {abbr}`wiring cost(the amount of material needed to construct the branches)`, the y-axis is the {abbr}`conduction delay(the distance between their cell body and their points of contact with other neurons)`, and the front shows how improving one impairs the other. For example, decreasing the {abbr}`conduction delay(the distance between their cell body and their points of contact with other neurons)`, leads to an increase in {abbr}`wiring cost(the amount of material needed to construct the branches)`. 

Then with this front defined, you can measure how far any given tree falls from it, as shown by the red cross.  

What the authors conclude is:
* That {abbr}`neural arbors(dendrites and axons)` are much closer to being Pareto optimal than you would expect by chance. 
* Suggesting that neuron structure is not random, but strikes a balance between optimizing wiring cost and conduction delays. 

:::{hint} Linker
So, how does this sort of structure impact computation? 
:::

## How Does Structure Impact Computation?

Frankly, that's an open question, so rather than giving you an answer, I’m just going to give you one example of the type of work people are pursuing in this direction. 

The authors of [this paper](https://doi.org/10.1162/neco_a_01390 
) model a single neuron and study how changing the properties of it's dendritic tree (which process it's inputs) alters it's ability to solve classic machine learning benchmarks like {abbr}`MNIST(Modified National Institute of Standards and Technology Dataset)`. 

```{figure} dendcomp.png
:label: structurecomp
:alt: Study of Changing Dentritic Tree and Computation
:align: center

Study of Changing Dentritic Tree and Computation
```

Shown on the left here is an outline of a real neuron. It's cell body is marked in pink and the black lines show it's dendritic tree. From examining these trees, researchers have found two interesting features: their branched {abbr}`morphology(Size, shape and form)` and repeated inputs.  

These features are shown schematically in the middle. Here we have a single neuron with it's soma in pink, an output axon, and four numbered inputs, in dark blue, to it's dendritic tree. As you can see these inputs are divided into two branches, and this structure is repeated across sub-trees which are shown in light blue. 

These features are shown schematically in the middle. Here we have a single neuron with it's soma in pink, an output axon, and four numbered inputs, in dark blue, to it's dendritic tree. As you can see these inputs are divided into two branches, and this structure is repeated across sub-trees which are shown in light blue. 

What they conclude is that:
* The performance of their tree model improves as you increase k (the number of repeated subtrees)
* But degrades when you make the trees more realistic by making them asymmetrical. 

Which suggests that there is still lots left to explore in this space! 

:::{seealso} That's it!
Okay, that’s all for neuron structure. In the next section we’ll move onto neuron function. 
:::