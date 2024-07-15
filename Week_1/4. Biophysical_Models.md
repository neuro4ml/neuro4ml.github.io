# Biophysical Neuron Models

[Downlod the slides here](W1-V3-biophysical-models.pptx)

:::{iframe} https://www.youtube.com/embed/9zD430EQga8
:width: 100%
:::
---

## Introduction

This section is going to be much shorter than the last one. We don’t want to go deep into models that are more biophysically realistic, but we do want to mention them.

:::{attention} Note!
In this section, we’ll focus on models with more biological detail than before
:::

[](#hodkin-huxley-label)

## Hodgkin-Huxely Type Neuron

We’ll start with the famous Hodgkin-Huxley neuron. You can see an action potential plotted with this model [here](#hux).

```{figure} hodgkinhuxely.png
:label: hux
:alt: Hodgkin-Huxely Action Potential Plot
:align: center

Hodgkin-Huxely Action Potential Plot
```
[These models](#model) are derived by treating the cell membrane as an electrical circuit consisting of a capacitor and a number of voltage-dependent resistors corresponding to the different ion channels. Each type of ion channel $k$ has an associated current $I_k (t)$ with its own dynamics, and there are many different types.

Actually, there are thousands of papers on different types of ion channels and their associated dynamics.

```{math}
:label: model

C \frac{dv}{dt} = \Sigma_k I_k (t) + I(t)

```

Where:

* $v$ is the membrane potential
* $C$ is the membrane capactiy
* $I(t)$ is the applied current

In the [original paper](https://doi.org/10.1113/jphysiol.1952.sp004764) by Hodgkin and Huxley they considered just three channels corresponding to sodium (Na), potassium (K) and a leak (L).

By hand – quite literally as they used a hand cranked mechanical calculator to solve the equations over several weeks – they found a series of functions that approximated these dynamics quite well.

```{math}
:label: model2

\Sigma_k I_k (t) = g_{Na}m^{3}h(v-E_{Na}) + g_{K}n^{4}(v-E_{K})+g_{L}(v-E_{L})

```

Their [model](#model2) fitted $m$, $n$ and $h$ into equations like [this](#equation). 

```{math}
:label: equation

\frac{dm}{dt} = a_{m}(v)(1-m) - \beta(v)m

```

With the functions defined like [so](#function):

```{math}
:label: function

a_{m}(v) = \frac{(a_{m}v + b_{m})}{1 - e^{c_{m}v+d_{m}}}

```

Ultimately, this comes down to a nonlinear differential equation with four dynamic variables and a lot of choices of functions and constants to fit to data.

It’s also a bit of a nasty one to simulate, either requiring very small time steps or a specific numerical integration method.

:::{seealso} For more
:class: dropdown
This has just been a very quick glimpse at this type of model.
We've provided an example of the code to simulate these in the notebook accompanying this section, and we’ve given a link to [further reading](https://doi.org/10.1113/jphysiol.1952.sp004764) if you’re interested in knowing more.
:::

## Spatial / Multicompartamental Neuron

One thing we’ve totally ignored so far in all the models we’ve talked about is that neurons have a very complex spatial structure, with dendrites and axons and – as you can see in the [picture](#gif) – activity that travels around the cell.

```{figure} neurongif.gif
:label: gif
:alt: Neuron Gif
:align: center

Neuron Activity
```

We can capture this by thinking of the dendrites and axons as [cylinder](#cyl), then break these cylinders into a number of short cylindrical segments that are relatively uniform. We then think of each as being nodes in an electrical circuit as we did with the Hodgkin-Huxley neuron.

```{figure} cylinders.png
:label: cyl
:alt: Cylinder Modeling of Dendrites and Axons
:align: center

Cylinder Modeling of Dendrites and Axons
```

If we take this approach and follow the mathematical analysis through, we get the [cable equation](#cable), a second order partial differential equation.
This can be simulated by breaking the cable into compartments as above, but as you can imagine it gets quite heavy quite quickly, as real neurons can have thousands of these compartments that need to be simulated.

```{math}
:label: cable

\frac{\delta^2}{\delta x^2}v(t,x) = cr_{L} * \frac{\delta}{\delta x}v(t,x) + r_{L} * \sum_k i_{k} (t,x) - r_{L}i(t,x)

```

Where:

* $t$ is time
* $x$ is the distance along the cable
* $i_k$ is ionic current per unit length
* $i_{ext}$ is externally applied current per unit length
* $r_L$ is longitudinal resistance per unit length
* $c$ is capacity per unit length

:::{hint} Linker
So, does any of this matter in terms of how the network as a whole functions or is this just implementation details?

We don’t know, but it has been argued that it may be important and it’s an active area of research, for example see [this recent paper](https://doi.org/10.48550/arXiv.2306.08007).

The problem is that it’s **too computationally demanding** to be used in a machine learning setup, or indeed in a large network without huge computational resources.
:::

## Dendrify

That’s where [Dendrify](https://dendrify.readthedocs.io/en/latest/) comes in. It’s a relatively new software package to automatically simplify these very complex models into something that can be easily simulated while still capturing a lot of the relevant dynamics, like [these examples here](#dendrifyslide). If you’re interested in exploring what dendritic structure could add to network level computations, this package is probably worth a look.

```{figure} dendrify.png
:label: dendrifyslide
:alt: Dendrify Models
:align: center

Dendrify Models
```

:::{seealso} That's it!
That’s the end of the sections on modelling neurons. The next section will be an introduction to this week’s coding exercise, and then next week we’ll move on to synapses and networks.

:::