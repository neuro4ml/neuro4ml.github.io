---
authors: goodman
---

# Decision making

[Download the slides here](slides/W7-V0-decision-making.pptx)

:::{iframe} https://www.youtube.com/embed/n_05hOV2hS0
:width: 100%
:::
---

```{danger} Work in progress
The text below has been transcribed by hand from the video above but has not yet been reviewed. Please use the videos and slides as the primary material and the text as support until I have a chance to proofread everything. When I have done this, I will remove this message.
```

## Introduction

This week’s sections don’t cover a single theme. Instead, we’ll look at a few different strands of work in neuroscience that could be of interest to people with a machine learning or quantitative background. We may also add more sections in future years.

In this section, we’ll focus on decision making - a really broad topic of course, because in a way you can think of everything the brain has to do is about making decisions.

:::{note}
We're going to focus on one particular aspect of decision making that has been studied a lot in neuroscience and less in machine learning: Reaction times. 

That is, how long does it take to make a decision based on a stream of information arriving continuously over time.
:::

## Two-alternative forced choice (2AFC)

Let’s make this even more concrete with a very specific sort of task, the two-alternative forced choice, or 2AFC.

In [this task](#2afc), participants are shown some sort of image or movie and asked to decide between two options. A common one is the random dot kinematogram, with random dots on the screen some of which are moving coherently either to the left or right, and some moving randomly. The participants have to determine which direction the coherent dots are moving, and sometimes they’re asked to make their decision as quickly as possible. The reaction time is the time between the start of the video and the moment when they press the button.

```{figure} figures/taskreact.png
:label: 2afc
:alt: Random Dot Kinematogram
:align: center
:width: 500px

Random Dot Kinematogram Experiment
```
If you run this experiment, you see characteristic skewed distributions of reaction times. [This data](#2afcresult) is actually from a slightly different task where participants were asked whether or not they had seen the image being shown before or not.

```{figure} figures/taskresult.png
:label: 2afcresult
:alt: 2AFC Experiment Results
:align: center

2AFC Experiment Results {cite:p}`https://doi.org/10.1037/0033-295X.85.2.59`.
```

## Drift diffusion / random walk model

There’s a beautifully simple theory for how we make these decisions which can account for these reaction times:

* Imagine that as time goes by, sometimes a bit of evidence arrives that is on its own unreliable, but suggestive that the dots are moving right rather than left. Then another, followed by one that suggests that left is more likely, and so on. We keep track of a running total of how much evidence we’ve received that suggests right versus left, and once it crosses some threshold we make our decision. This is a biased random walk, where it’s more likely we take a step in the correct direction than the wrong direction.

<!-- ```{figure} 10.png
:label: 10res
:alt: Biased Random Walk With 10 Time Points
:align: center

Biased Random Walk With 10 Time Points
```
-->
(10res)=
![](#10-graph)

* As we increase the number of time points from [10](#10res) to [30](#30res).

<!-- ```{figure} 30.png
:label: 30res
:alt: Biased Random Walk With 30 Time Points
:align: center

Biased Random Walk With 30 Time Points
```
-->

(30res)=
![](#30-graph)

* Then from [30](#30res) to [100](#100res).

<!-- ```{figure} 100.png
:label: 100res
:alt: Biased Random Walk With 100 Time Points
:align: center

Biased Random Walk With 100 Time Points
```
-->

(100res)=
![](#100-graph)

And all the way up to [1000](#1000res), this random walk looks more and more like Brownian motion with a drift. This gives us a mathematical theory that lets us analytically compute expressions for the reaction time distributions.

<!-- ```{figure} 1000.png
:label: 1000res
:alt: Biased Random Walk With 1000 Time Points
:align: center

Biased Random Walk With 1000 Time Points
```
-->

(1000res)=
![](#1000-graph)

* Or we can just numerically plot them, [here](#highlow) with either a low decision threshold or a high decision threshold. 

<!-- ```{figure} distr.png
:label: highlow
:alt: Plot of Reaction Time Distributions for High and Low Decision Thresholds
:align: center

Plot of Reaction Time Distributions for High and Low Decision Thresholds
```
-->

(highlow)=
![](#highlow-graph)

* And sure enough, we get something that looks very much like the data from [experiments](#2afcresult).

## Probabilistic interpretation

That’s nice, but still this model might seem a bit ad hoc. Fortunately, it turns out that there’s a neat probabilistic interpretation of this model.

* Let’s set up a probabilistic model of the task. We’ll say there’s a true direction $D$ that can be either $L$ or $R$, and both options are equally likely. You can actually modify this to make the options have different probabilities too.

```{math}
\begin{gather}
    \text{True direction } 𝐷=𝐿 \text{ or } 𝐷=𝑅 \\
    \text{ equally likely } \textcolor{#fcad03}{𝑃(𝐷=𝐿)}=\textcolor{#fcad03}{𝑃(𝐷=𝑅)}=1/2
\end{gather}
```

* Now we have the data observed at time t is a series of symbols $𝑋_𝑡$ each of which is $R$ or $L$.

```{math}
    \text{Observed data } 𝑋_𝑡=(𝑅,𝑅,𝐿,𝑅,𝐿,𝑅,𝑅,𝑅,…)
```

* The probability you observe the correct symbol is $p$ and the probability you observe the incorrect symbol $1-p$.

```{math}
\begin{gather}
    \text{Probabilities } 𝑃(𝑋_𝑡=𝐷)= \textcolor{#03bafc}{𝑝} \text{ and } 𝑃(𝑋_𝑡=−𝐷)=1− \textcolor{#03bafc}{𝑝} \\
    \text{where } \textcolor{#03bafc}{𝑝} > 1 − \textcolor{#03bafc}{𝑝}
\end{gather}
```

* Now given we know the observed data and we want to infer the unknown value of $D$, we compute which of the two options is more likely. We’ll write that as $\textcolor{#a503fc}{r(X)}$ equals the ratio of the probability that $D=R$ given the observations $X$, divided by the probability that $D=L$. If this ratio is high then $D=R$ is much more likely, and if it’s low then $D=L$ is more likely.

```{math}
\begin{gather}
    \text{Write } \textcolor{#a503fc}{r(X)}=\frac{𝑃(𝐷=𝑅|𝑿)}{𝑃(𝐷=𝐿|𝑿)} \text{ the likelihood ratio.} \\
    \text{If this is high then } 𝐷=𝑅 \text{ is more likely.}
\end{gather}
```

* We use Bayes’ theorem to rewrite the probability that $D=R$ given X in terms of the probability of $X$ given $D=R$ times the probability $D=R$ over the probability of $X$. And the same thing for the probability that $D=L$ on the bottom.

```{math}
    \textcolor{#a503fc}{r(X)}=\frac{𝑃(𝑿|𝐷=𝑅)_{\textcolor{#fcad03}{𝑃(𝐷=R)}/\textcolor{#128f04}{P(X)}}}{𝑃(𝑿|𝐷=L)_{\textcolor{#fcad03}{𝑃(𝐷=𝐿)}/\textcolor{#128f04}{P(X)}}}
```

* The $\textcolor{#128f04}{P(X)}$ cancels and both the prior probabilities $D=R$ and $D=L$ are both ½ so they cancel too.

```{math}
    \textcolor{#a503fc}{r(X)}=\frac{𝑃(𝑿|𝐷=𝑅)}{𝑃(𝑿|𝐷=L)}
```

* The observations at different time points are independent, so we can expand these as a product of the probabilities at each time point.

```{math}
    \textcolor{#a503fc}{r(X)}=\frac{\Pi_{t}𝑃(𝑿_t|𝐷=𝑅)}{\Pi_{t}𝑃(𝑿_t|𝐷=L)}
```

* And this product is the same at the top and bottom so we can pull it out.

```{math}
    \textcolor{#a503fc}{r(X)}=\Pi_{t}\frac{𝑃(𝑿_t|𝐷=𝑅)}{𝑃(𝑿_t|𝐷=L)}
```

* Now to see what’s going on more clearly we take the log of this ratio to get the log likelihood ratio.

```{math}
    log\textcolor{#a503fc}{r(X)}=log\Pi_{t}\frac{𝑃(𝑿_t|𝐷=𝑅)}{𝑃(𝑿_t|𝐷=L)}
```    

* The log of a product is the sum of the logs.

```{math}
    log\textcolor{#a503fc}{r(X)}=\Sigma_{t}log\frac{𝑃(𝑿_t|𝐷=𝑅)}{𝑃(𝑿_t|𝐷=L)}
```

* And we’ll write the individual terms as the “evidence at time $t$” $\epsilon(𝑋_𝑡)$.

```{math}
     log\textcolor{#a503fc}{r(X)}=\Sigma_{t}\epsilon(𝑋_𝑡)
```

* This evidence will be log of $\textcolor{#03bafc}{𝑝}/ 1- \textcolor{#03bafc}{𝑝}$ when $𝑋_𝑡=𝐿$. If $𝑋_𝑡=𝑅$ it’s log of $(1- \textcolor{#03bafc}{𝑝})/ \textcolor{#03bafc}{𝑝}$ but this is just negative log of $\textcolor{#03bafc}{𝑝} / 1- \textcolor{#03bafc}{𝑝}$.

```{math}
\begin{gather}
    \epsilon(𝑋_𝑡) = log\frac{\textcolor{#03bafc}{𝑝}}{1-\textcolor{#03bafc}{𝑝}} \text{ if } 𝑋_𝑡 = L \\
    \text{or } -log\frac{\textcolor{#03bafc}{𝑝}}{1-\textcolor{#03bafc}{𝑝}} \text{ if } 𝑋_𝑡 = R
\end{gather}
```

* With that we can write the log likelihood ratio as a constant term multiplied by the sum of terms $\textcolor{#fc0505}{\Sigma_{t}\delta(𝑋_𝑡)}$ which are $+1$ if $𝑋_𝑡=𝑅$ and $-1$ if $𝑋_𝑡=𝐿$.

```{math}
    log𝑟(𝑿)=log\frac{\textcolor{#03bafc}{𝑝}}{1-\textcolor{#03bafc}{𝑝}} * \textcolor{#fc0505}{\Sigma_{t}\delta(𝑋_𝑡)}
```

* But this sum is precisely the random walk we saw previously.

```{math}
    \textcolor{#fc0505}{\Sigma_{t}\delta(𝑋_𝑡)} \text{ is random walk!}
```

* When D=R the sum increases by 1 with probability $\textcolor{#03bafc}{𝑝}$ and decreases by 1 with probability $1-\textcolor{#03bafc}{𝑝}$, and vice versa if $D=L$.

* We can understand the decision threshold in terms of probability now. We wait until the log likelihood ratio is bigger than some threshold $\theta$ or equivalently that the likelihood ratio is bigger than $𝑒^{\theta}$.

```{math}
\begin{gather}
    \text{Decision threshold }\theta \text{: } 𝐷=𝑅 \text{ more likely if} \\
    log\textcolor{#a503fc}{r(X)}>\theta \text{ or } \textcolor{#a503fc}{r(X)}>𝑒^\theta
\end{gather}
```

* And this happens when the sum of the deltas is bigger than some threshold, precisely as in the drift diffusion model.

```{math}
    \text{This is true if } \textcolor{#fc0505}{\Sigma_{t}\delta(𝑋_𝑡)} >  \text{const} = \theta / log\frac{\textcolor{#03bafc}{𝑝}}{1-\textcolor{#03bafc}{𝑝}}
```

<!-- ```{figure} probinter.png
:label: probi
:alt: Probabilistic Interpretation of Model
:align: center

Probabilistic Interpretation of Model
```
-->

## Electrophysiological data

And finally, perhaps the best thing about this theory is that having come up with the model based on fitting behavioural observations and then finding a rigorous probabilistic interpretation, electrophysiological [experiments](#electrophys) find traces of these evidence accumulation processes across multiple different brain regions in multiple species.

```{figure} figures/electro.jpg
:label: electrophys
:alt: Experimental Evidence of Accumulation Processes Across Multiple Brain Regions and Species
:align: center

Experimental Evidence of Accumulation Processes Across Multiple Brain Regions and Species {cite:p}`https://doi.org/10.1016/j.tins.2018.06.005`.
```

Of course, it’s never quite as clean cut as the theory and all sorts of modifications have been proposed, like adaptive thresholds that get closer to the origin over time to represent the increasing urgency of making some sort of decision, or thresholds that adapt to wider context in various ways. There are also much more comprehensive Bayesian theories of decision making in which this model is just one special case, and so on. 

:::{seealso} For more
:class: dropdown
If you’re interested in reading more, there are some suggested starting points

* [Ratcliff (1978) "A theory of memory retrieval"](https://doi.org/10.1037/0033-295X.85.2.59)
* [Gold and Shadlen (2007) "The Neural Basis of Decision Making"](https://doi.org/10.1146/annurev.neuro.29.051605.113038)
* [O'Connell et al. (2018) "Bridging Neural and Computational Viewpoints on Perceptual Decision-Making"](https://doi.org/10.1016/j.tins.2018.06.005)
:::

:::{seealso} That's it!
In the next section, we’ll talk about Neuromorphic Computing.
:::