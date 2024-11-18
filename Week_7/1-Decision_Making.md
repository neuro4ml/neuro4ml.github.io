---
authors: goodman
---

# Decision making

[Download the slides here](slides/W7-V0-decision-making.pptx)

:::{iframe} https://www.youtube.com/embed/n_05hOV2hS0
:width: 100%
:::
---

## Introduction

This week's sections don't cover a single theme. Instead, we'll look at a few different strands of work in neuroscience that could be of interest to people with a machine learning or quantitative background. We may also add more sections in future years.

In this section, we'll focus on decision making - a really broad topic of course, because in a way you can think of everything the brain has to do is about making decisions.

We're going to focus on one particular aspect of decision making that has been studied a lot in neuroscience and less in machine learning: Reaction times. 

That is, how long does it take to make a decision based on a stream of information arriving continuously over time.

## Two-alternative forced choice (2AFC)

Let's make this even more concrete with a very specific sort of task, the two-alternative forced choice, or 2AFC.

In [this task](#2afc), participants are shown some sort of image or movie and asked to decide between two options. A common one is the random dot kinematogram, with random dots on the screen some of which are moving coherently either to the left or right, and some moving randomly. The participants have to determine which direction the coherent dots are moving, and sometimes they're asked to make their decision as quickly as possible. The reaction time is the time between the start of the video and the moment when they press the button.

```{figure} figures/taskreact.png
:label: 2afc
:alt: Random Dot Kinematogram
:align: center
:width: 500px

Random dot kinematogram task.
```
If you run this experiment, you see characteristic skewed distributions of reaction times. [This data](#2afcresult) is actually from a slightly different task where participants were asked whether or not they had seen the image being shown before or not.

```{figure} figures/taskresult.png
:label: 2afcresult
:alt: 2AFC Experiment Results
:align: center

Reaction times for a 2AFC task {cite:p}`https://doi.org/10.1037/0033-295X.85.2.59`.
```

## Drift diffusion / random walk model

There's a beautifully simple theory for how we make these decisions which can account for these reaction times:

Imagine that as time goes by, sometimes a bit of evidence arrives that is on its own unreliable, but suggestive that the dots are moving right rather than left. Then another, followed by one that suggests that left is more likely, and so on. We keep track of a running total of how much evidence we've received that suggests right versus left, and once it crosses some threshold we make our decision. This is a biased random walk, where it's more likely we take a step in the correct direction than the wrong direction.

(10res)=
![](#10-graph)

As we increase the number of time points from [10](#10res) to [30](#30res).

(30res)=
![](#30-graph)

Then from [30](#30res) to [100](#100res).

(100res)=
![](#100-graph)

And all the way up to [1000](#1000res), this random walk looks more and more like Brownian motion with a drift. This gives us a mathematical theory that lets us analytically compute expressions for the reaction time distributions.

(1000res)=
![](#1000-graph)

Or we can just numerically plot them, [here](#highlow) with either a low decision threshold or a high decision threshold. 

(highlow)=
![](#highlow-graph)

And sure enough, we get something that looks very much like the data from [experiments](#2afcresult).

## Probabilistic interpretation

That's nice, but still this model might seem a bit ad hoc. Fortunately, it turns out that there's a neat probabilistic interpretation of this model.

Let's set up a probabilistic model of the task. We'll say there's a true direction $D$ that can be either $L$ or $R$, and both options are equally likely, so: $$\textcolor{#fcad03}{P(D=L)}=\textcolor{#fcad03}{P(D=R)}=1/2.$$ You can actually modify this to make the options have different probabilities too.

Now we have the data observed at time t is a series of symbols $X_t$ each of which is $R$ or $L$, e.g. $\mathbf{X}=(R,R,L,R,L,R,R,R,\ldots)$.

The probability you observe the correct symbol is $p$ and the probability you observe the incorrect symbol $1-p$ (note that $p>1-p$ for this to make sense):

```{math}
\begin{align}
    P(X_t=D)&=\textcolor{#03bafc}{p}\\
    P(X_t=-D)&=1-\textcolor{#03bafc}{p} \\
\end{align}
```

Now given we know the observed data and we want to infer the unknown value of $D$, we compute which of the two options is more likely. We'll write that as $\textcolor{#a503fc}{r(X)}$ equals the ratio of the probability that $D=R$ given the observations $X$, divided by the probability that $D=L$. If this ratio is greater than 1 then $D=R$ is more likely (increasingly so the larger the ratio), and if it's less than 1 then $D=L$ is more likely.

$$\textcolor{#a503fc}{r(X)}=\frac{P(D=R|X)}{P(D=L|X)}$$

We use Bayes' theorem to rewrite the probability that $D=R$ given $\mathbf{X}$ in terms of the probability of $\mathbf{X}$ given $D=R$ times the probability $D=R$ over the probability of $X$. And the same thing for the probability that $D=L$ on the bottom.

```{math}
    \textcolor{#a503fc}{r(X)}=\frac{P(X|D=R)\textcolor{#fcad03}{P(D=R)}/\textcolor{#128f04}{P(X)}}{P(X|D=L)\textcolor{#fcad03}{P(D=L)}/\textcolor{#128f04}{P(X)}}
```

The $\textcolor{#128f04}{P(X)}$ cancels and both the prior probabilities $D=R$ and $D=L$ are both ½ so they cancel too.

```{math}
    \textcolor{#a503fc}{r(X)}=\frac{P(X|D=R)}{P(X|D=L)}
```

The observations at different time points are independent, so we can expand these as a product of the probabilities at each time point.

```{math}
    \textcolor{#a503fc}{r(X)}=\frac{\prod_{t}P(X_t|D=R)}{\prod_{t}P(X_t|D=L)}
```

And this product is the same at the top and bottom so we can pull it out.

```{math}
    \textcolor{#a503fc}{r(X)}=\prod_{t}\frac{P(X_t|D=R)}{P(X_t|D=L)}
```

Now to see what's going on more clearly we take the log of this ratio to get the log likelihood ratio.

```{math}
    \log\textcolor{#a503fc}{r(X)}=\log\prod_{t}\frac{P(X_t|D=R)}{P(X_t|D=L)}
```    

The log of a product is the sum of the logs.

```{math}
    \log\textcolor{#a503fc}{r(X)}=\sum_{t}\log\frac{P(X_t|D=R)}{P(X_t|D=L)}
```

And we'll write the individual terms as the “evidence at time $t$” $\epsilon(X_t)$.

```{math}
     \log\textcolor{#a503fc}{r(X)}=\sum_{t}\epsilon(X_t)
```

This evidence will be log of $\textcolor{#03bafc}{p}/ 1- \textcolor{#03bafc}{p}$ when $X_t=L$. If $X_t=R$ it's log of $(1- \textcolor{#03bafc}{p})/ \textcolor{#03bafc}{p}$ but this is just negative log of $\textcolor{#03bafc}{p} / 1- \textcolor{#03bafc}{p}$.

```{math}
\epsilon(X_t) = 
\begin{cases}
    \log\frac{\textcolor{#03bafc}{p}}{1-\textcolor{#03bafc}{p}} & \text{ if } X_t = L \\
    -\log\frac{\textcolor{#03bafc}{p}}{1-\textcolor{#03bafc}{p}} & \text{ if } X_t = R
\end{cases}
```

With that we can write the log likelihood ratio as a constant term multiplied by the sum of terms $\textcolor{#fc0505}{\sum_{t}\delta(X_t)}$ which are $+1$ if $X_t=R$ and $-1$ if $X_t=L$.

```{math}
    \log r(X)=\left(\log\frac{\textcolor{#03bafc}{p}}{1-\textcolor{#03bafc}{p}}\right) \cdot \textcolor{#fc0505}{\sum_{t}\delta(X_t)}
```

where

```{math}
\textcolor{#fc0505}{\delta(X_t)} = 
\begin{cases}
    1 & \text{ if } X_t = L \\
    -1 & \text{ if } X_t = R
\end{cases}
```

But this sum $\textcolor{#fc0505}{\sum_{t}\delta(X_t)}$ is precisely the random walk we saw previously!

When $D=R$ the sum increases by 1 with probability $\textcolor{#03bafc}{p}$ and decreases by 1 with probability $1-\textcolor{#03bafc}{p}$, and vice versa if $D=L$.

We can understand the decision threshold in terms of probability now. We wait until the log likelihood ratio is bigger than some threshold $\theta$ or equivalently that the likelihood ratio is bigger than $e^{\theta}$. That is, we guess that $D=R$ if $\log \textcolor{#a503fc}{r(X)} > \theta$. Similarly, we guess $D=L$ if $\log \textcolor{#a503fc}{r(X)} < -\theta$.

And this happens when the sum of the deltas is bigger (or smaller) than some threshold, precisely as in the drift diffusion model. That is, we guess $D=R$ if

```{math}
    \textcolor{#fc0505}{\sum_{t}\delta(X_t)} >  \text{const} = \theta / \log\frac{\textcolor{#03bafc}{p}}{1-\textcolor{#03bafc}{p}}
```

## Electrophysiological data

And finally, perhaps the best thing about this theory is that having come up with the model based on fitting behavioural observations and then finding a rigorous probabilistic interpretation, electrophysiological [experiments](#electrophys) find traces of these evidence accumulation processes across multiple different brain regions in multiple species.

```{figure} figures/electro.jpg
:label: electrophys
:alt: Experimental Evidence of Accumulation Processes Across Multiple Brain Regions and Species
:align: center

Experimental evidence of accumulation processes across multiple brain regions and species {cite:p}`https://doi.org/10.1016/j.tins.2018.06.005`.
```

Of course, it's never quite as clean cut as the theory and all sorts of modifications have been proposed, like adaptive thresholds that get closer to the origin over time to represent the increasing urgency of making some sort of decision, or thresholds that adapt to wider context in various ways. There are also much more comprehensive Bayesian theories of decision making in which this model is just one special case, and so on. 

:::{seealso} For more
:class: dropdown
If you're interested in reading more, there are some suggested starting points

* [Ratcliff (1978) "A theory of memory retrieval"](https://doi.org/10.1037/0033-295X.85.2.59)
* [Gold and Shadlen (2007) "The Neural Basis of Decision Making"](https://doi.org/10.1146/annurev.neuro.29.051605.113038)
* [O'Connell et al. (2018) "Bridging Neural and Computational Viewpoints on Perceptual Decision-Making"](https://doi.org/10.1016/j.tins.2018.06.005)
:::
