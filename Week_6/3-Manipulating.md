# Manipulating

[Download the slides here](slides/W6-V2-manipulating.pptx)

:::{iframe} https://www.youtube.com/embed/_V0PPQjYgeo
:width: 100%
:::
---

```{danger} Work in progress
The text below has been transcribed by hand from the video above but has not yet been reviewed. Please use the videos and slides as the primary material and the text as support until I have a chance to proofread everything. When I have done this, I will remove this message.
```

## Introduction

In the last two sections we've looked at how we can record and analyze neural activity.

But, to some extent these methods simply correlate neural activity with different variables, and we can’t really be sure that our findings are casually relevant.

In other words, once we’ve found neurons that respond to a stimulus or precede a behavior, we would like to know if disrupting those neurons would have any effect on the animal’s ability to respond to that stimulus or implement that behavior.

We can do this by manipulating neural activity, and these manipulations can be either irreversible or reversible.

:::{note}
In this section we'll look at manipulating neural activity
:::

## Irreversible

In humans, irreversible changes usually result from accidents, disease processes or surgical interventions. For example, earlier in the course we mentioned a paper which used fMRI to study two patients with damage to their corpus callosums.

But in animal models, we can irreversibly destroy single neurons or even whole parts of the brain, to study the effect.

For example, {cite:t}`https://doi.org/10.3389/fnins.2023.971980` used rats to study the role of motor cortex: a part of the brain where many studies have observed neural activity correlated with movement.

In this paper, the authors:

* Took a group of rats, destroyed or lesioned motor cortex in half of the animals, and left the other half as controls (with no motor cortex damage).
* They then trained all of the rats to cross the steps shown here.

[The video](#rat-vid1) shows one animal with a motor cortex lesion learning the task (lesion A), and then one control animal. Hopefully, you can see how similar the two animals movement patterns are. And surprisingly, the authors found no differences between the two groups.

:::{figure} figures/Media2.mp4
:label: rat-vid1
:::

One conclusion from this result could be that motor cortex isn’t needed for a simple task like this.
So, the authors then increased the difficulty of the task by making more and more of the steps unstable.

Surprisingly, again they didn’t see any difference between the two groups. Until they looked at their data very carefully.

What they found was that when the animals first encountered the unstable steps, they responded in one of the three ways shown in [this video](#rat-vid2):

* Some controls stopped to investigate the unstable step
* Some controls compensated by adjusting their movement
* But the animals with motor cortex lesions stopped moving for several seconds.

This suggests that the main role of this brain area may be to help the animal to adapt its behaviour to unexpected situations.

Though, in the context of [this video](#rat-vid2), I think this paper nicely illustrates that while we may assign roles to neurons or brain areas based on observing their activity, we can only really confirm or refute their roles by manipulating them.

:::{figure} figures/Media3.mp4
:label: rat-vid2
:::

However, we don’t always have to use irreversible manipulations, as there are reversible methods too.

## Reversible

In humans, one approach is called **trans-cranial magnetic stimulation** or TMS, which uses magnetic fields to alter the activity of brain regions.
But, in animal models we can control neural activity more precisely and one great method for doing this is **optogenetics**: which uses light-gated proteins to control neural activity.
Some of these light-gated proteins are genetically engineered, but some occur naturally in things like algae.

For example, 

* Channelrhodopsin – shown on the [left of this figure](#reversible-pic). Is an ion channel which, when exposed to blue light, changes its structure and allows positive ions to flow into neurons, increasing their membrane potential and spiking activity. 
* In contrast, the proteins shown on the [right](#reversible-pic) [Halorhodopsin and Archaerhodopsin] respond to yellow light by either moving chloride ions into the neuron or moving hydrogen ions out, both of which will decrease the neurons membrane potential and spiking activity. 

So, by expressing these channels in neurons and then triggering them with light we can study neurons roles by reversibly activating or silencing them. 

```{figure} figures/manipulatingPicture1.jpg
:label: reversible-pic
:align:center
:width: 500px

Examples of Optogenetics
```

While, this may seem a bit detached from machine learning, we can actually use the same types of manipulations to interrogate artificial neural networks.  

## Single-element lesions in ANNs

For example, in {cite:t}`https://doi.org/10.1371/journal.pcbi.1010250` the authors studied an ANN by lesioning it extensively.
To create their ANN, they used an evolutionary algorithm called (NEAT) to evolve a neural network which could play the arcade game Space Invaders.

Their evolved network is [shown here](#single-lesions-pic1):

* On the left are the networks 12 input nodes – which receive a compressed version of the video game. 
* On the right are the networks 6 output nodes – which encode actions like move left, move right, and fire.
* And then in between is a single hidden node and many connections whose weights are color-coded. 

```{figure} figures/manipulatingPicture2.png
:label: single-lesions-pic1
:align:center
:width: 100%

Evolved space invader network {cite:p}`https://doi.org/10.1371/journal.pcbi.1010250`.
```

To manipulate their network they then silenced each node one-by-one and checked how well the network performed with each node silenced individually.
[This figure](#single-lesions-pic2) shows their results: the y-axis shows the networks score – with higher being better. For comparison, the blue stripe shows the networks normal performance, and the red stripe shows it’s performance if you shuffle its weights.

On the [left of the figure](#single-lesions-pic2), each point on the x-axis corresponds to silencing a single node, and you can see that ablating different nodes leads to different scores – which implies that some nodes are more important for the task than others. And interestingly you can also see that ablating two of the nodes, increases the networks score; suggesting they hinder the network.

On the [right of the figure](#single-lesions-pic2), the authors also silence every weight in turn and study the effect on performance, and interestingly their results suggest that many weights could be removed without much effect.

```{figure} figures/manipulatingPicture3.png
:label: single-lesions-pic2
:align:center
:width: 100%

Manipulated evolved network results {cite:p}`https://doi.org/10.1371/journal.pcbi.1010250`.
```

However, the results from single-element manipulations like these could be misleading. For example, imagine if two units in this network perform the same function in parallel. Then silencing either one alone, may not result in a change to the networks score and we could wrongly conclude that neither of these units are important.

Taking this further, because of the complex interactions between elements in a network (either artificial or biological), there are likely many variants of this problem.

To overcome this issue, the authors compare their results to a multi-element approach. 

(multi-element-lessions)=
## Multi-element lesions in ANNs

In this multi-element approach, you sample combinations of lesions.
For example, you silence node 1 and check the networks score, then silence node 1 and 2 together, 1, 2, 3 together etc.
Then you calculate each nodes importance by comparing the networks score with and without the node in different combinations.

The results are shown as before in [panel B](#multi-lesions), and [in panel C](#multi-lesions) on the right you can see that the single and multi-node ablations assigns different importance to different nodes.
This and other results lead the authors to conclude that even small ANNs can be challenging to interpret.
And so we should perhaps be cautious when interpreting manipulation results from larger and more complex systems.
If you’d like to learn more about this approach and these results, we highly recommend [the paper](https://doi.org/10.1371/journal.pcbi.1010250).

```{figure} figures/manipulatingPicture4.png
:label: multi-lesions
:align:center
:width: 100%

Manipulated evolved network results using multi-element approach {cite:p}`https://doi.org/10.1371/journal.pcbi.1010250`.
```

:::{seealso} That's it!
Hopefully that’s given you an overview of how we can manipulate neural activity. 
In the next section we're going to outline this week’s exercise, which will challenge you to observe and manipulate an ANN yourself.
:::