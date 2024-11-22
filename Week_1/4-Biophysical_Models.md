---
authors: goodman
---

# Biophysical neuron models

[Download the slides here](slides/W1-V3-biophysical-models.pptx)

:::{iframe} https://www.youtube.com/embed/9zD430EQga8
:width: 100%
:::
---

## Introduction

This section is going to be much shorter than the last one. We don't want to go deep into models that are more biophysically realistic, but we do want to mention them.

(hodkin-huxley-label)=
## Hodgkin-Huxley type neuron

We'll start with the famous Hodgkin-Huxley neuron. You can see an action potential plotted with this model [here](#hux).

```{figure} #hodgkin-huxley-fig-label
:label: hux
:align: center

Action potential in a Hodgkin-Huxley model.
```

These models are derived by treating the cell membrane as an electrical circuit consisting of a capacitor and a number of voltage-dependent resistors corresponding to the different ion channels. Each type of ion channel $k$ has an associated current $I_k (t)$ with its own dynamics, and there are many different types.

````{important} Hodgkin-Huxley-type model definition
```{math}
:label: ion-channel-models
C \frac{\ud v}{\ud t} = \Sigma_k I_k (t) + I(t)
```

where:
* $C$ is the membrane capacity
* $v$ is the membrane potential
* $I_k(t)$ is the current associated to ion channel $k$
* $I(t)$ is the applied current
````

Actually, there are thousands of papers on different types of ion channels and their associated dynamics.
In the original paper {cite:p}`https://doi.org/10.1113/jphysiol.1952.sp004764` they considered just three channels corresponding to sodium (Na), potassium (K) and a leak (L).

````{margin}
```{figure} figures/hand-cranked-calculator.jpg

A Brunsviga hand cranked mechanical calculator as used by Hodgkin and Huxley to compute numerically the solutions to their differential equations for the action potential in the squid giant axon in 1952. 
```
````

By hand – quite literally as they used a hand cranked mechanical calculator to solve the equations over several weeks – they found a series of functions that approximated these dynamics quite well.

```{math}
:label: model2

\Sigma_k I_k (t) = g_{Na}m^{3}h(v-E_{Na}) + g_{K}n^{4}(v-E_{K})+g_{L}(v-E_{L})

```

Their [model](#model2) fitted $m$, $n$ and $h$ into equations like [this](#equation). 

```{math}
:label: equation

\frac{dm}{\ud t} = a_{m}(v)(1-m) - \beta(v)m

```

With the functions defined like [so](#function):

```{math}
:label: function

a_{m}(v) = \frac{(a_{m}v + b_{m})}{1 - e^{c_{m}v+d_{m}}}

```

Ultimately, this comes down to a nonlinear differential equation with four dynamic variables and a lot of choices of functions and constants to fit to data.

It's also a bit of a nasty one to simulate, either requiring very small time steps or a specific numerical integration method.

:::{seealso} For more
:class: dropdown
This has just been a very quick glimpse at this type of model.
We've provided an example of the code to simulate these in [](./code-for-figures.ipynb).

Further reading:
* [Gerstner et al. (2014) "Neuronal Dynamics" - Chapter 2 Ion Channels and the Hodgkin-Huxley Model](https://neuronaldynamics.epfl.ch/online/Ch2.html)
* [Gerstner et al. (2014) "Neuronal Dynamics" - Chapter 3 Dendrites and Synapses](https://neuronaldynamics.epfl.ch/online/Ch3.html)
:::

## Spatial / Multicompartamental Neuron

One thing we've totally ignored so far in all the models we've talked about is that neurons have a very complex spatial structure, with dendrites and axons and – as you can see in the [picture](#gif) – activity that travels around the cell.

```{figure} figures/neurongif.gif
:label: gif
:align: center

Propagation of electrical activity in a multicompartmental neuron model.
```

We can capture this by thinking of the dendrites and axons as [cylinder](#cyl), then break these cylinders into a number of short cylindrical segments that are relatively uniform. We then think of each as being nodes in an electrical circuit as we did with the Hodgkin-Huxley neuron.

```{figure} figures/cylinders.png
:label: cyl
:align: center

Modelling dendrites and axons as cylinders broken into compartments.
```

If we take this approach and follow the mathematical analysis through, we get the [cable equation](#cable), a second order partial differential equation.
This can be simulated by breaking the cable into compartments as above, but as you can imagine it gets quite heavy quite quickly, as real neurons can have thousands of these compartments that need to be simulated.

(cable)=
````{important} Cable equation definition
$$\frac{\partial^2}{\partial x^2}v(t,x) = cr_{L} \cdot \frac{\partial}{\partial x}v(t,x) + r_{L} \cdot \sum_k i_{k} (t,x) - r_{L}i(t,x)$$
where:
* $t$ is time
* $x$ is the distance along the cable
* $v(t,x)$ is the membrane potential at time $t$ position $x$
* $c$ is capacity per unit length
* $r_L$ is longitudinal resistance per unit length
* $i_k$ is ionic current per unit length
* $i$ is externally applied current per unit length
````

So, does any of this matter in terms of how the network as a whole functions or is this just implementation details?

We don't know, but it has been argued that it may be important and it's an active area of research, for example see [](https://doi.org/10.48550/arXiv.2306.08007).

The problem is that it's too computationally demanding to be used in a machine learning setup, or indeed in a large network without huge computational resources.

## Dendrify

That's where [Dendrify](https://dendrify.readthedocs.io/en/latest/) comes in. It's a relatively new software package to automatically simplify these very complex models into something that can be easily simulated while still capturing a lot of the relevant dynamics, like [these examples here](#dendrifyslide). If you're interested in exploring what dendritic structure could add to network level computations, this package is probably worth a look.

```{figure} figures/dendrify.png
:label: dendrifyslide
:align: center

Simplified multicompartmental neurons as used in Dendrify.
```