# Rate-Based Models

[Download the slides here](slides/W4-V1-rate-based-models.pptx)

:::{iframe} https://www.youtube.com/embed/UIPn7COOYcg
:width: 100%
:::
---

:::{attention} Note!
In this section we'll talk about rate-based Hebbian models of learning.
:::

## General Form

We'll only discuss learning of a synaptic weight $\color{#0f73bf}{w}$, in response to a pre-synaptic neuron firing spikes at a rate $\color{#b35f10}{r_{pre}}$, and a post-synaptic neuron firing at a rate $\color{#b80909}{r_{post}}$.

```{figure} figures/Picture1.png
:width: 300px
:align: center
```

We model this with a [differential equation](#hebbian-rule-eq) saying that the weight changes at a rate $\color{purple}{\tau_w}$ proportionate to some function $F$ of the weight and the pre- and post-synaptic weights.

(hebbian-rule-eq)=
```{math}
\textcolor{purple}{\tau_w} \frac{d \textcolor{#0f73bf}{w}}{dt} = F (\textcolor{#0f73bf}{w}, \textcolor{#b35f10}{r_{pre}}, \textcolor{#b80909}{r_{post}})
```

Different functions $F$ give you different models.

## Simplest Possible Hebbian Rule

That's quite abstract, so let's take a look at a concrete example.

Let's choose the function $F$ to be the product of the pre- and post-synaptic firing rates so that $w$ just grows in proportion to the correlation between the firing rates.

```{math}
\textcolor{purple}{\tau_w} \frac{d \textcolor{#0f73bf}{w}}{dt} = \textcolor{#b35f10}{r_{pre}} \textcolor{#b80909}{r_{post}}
```

This clearly satisfies the idea of _"Cells that fire ($\textcolor{#b35f10}{r_{pre}}$ and $\textcolor{#b80909}{r_{post}}$ both large) together wire together ($\textcolor{#0f73bf}{w}$ grows)"_

But we can already see a problem with this. There’s no reason for the weight to stop growing ($\textcolor{#0f73bf}{w}$ grows without bound). It will keep just getting bigger and bigger. This actually turns out to be an issue with a lot of learning rules, and we’ll keep coming back to it.

One solution is to have a hard bound, just clip w at some maximum value.

```{math}
\begin{gather}
\text{Hard Bound:} \\
\text{Fix } 0 \le \textcolor{#0f73bf}{w} \le w^{max}
\end{gather}
```

Another solution is a softer bound which just reduces the rate of change of the weight as you get close to the maximum value. There are various ways you can do this, [this equation](#soft-bound-eq) is just one example.

(soft-bound-eq)=
```{math}
\begin{gather}
\text{Soft Bound:} \\
\textcolor{purple}{\tau_w} \frac{d \textcolor{#0f73bf}{w}}{dt} = \text{const } \cdot (w^{max} - \textcolor{#0f73bf}{w}) \cdot \textcolor{#b35f10}{r_{pre}} \textcolor{#b80909}{r_{post}}
\end{gather}
```

Another problem is that in this simple formulation, the weight will only grow, never get smaller, since both the pre- and post-synaptic firing rates are always positive. It is possible to fix this by adding another term, but let’s move on to another model.

## Oja's Learning Rule

Oja’s learning rule was designed to solve the problem of the weights growing without bound.
You take the standard [Hebbian rule](#hebbian-rule-eq) and subtract off a term proportionate to the product of the weight and the square of the postsynaptic firing rate.
In other words, after growing for a while the weights will stop changing.

```{math}
\textcolor{purple}{\tau_w} \frac{d \textcolor{#0f73bf}{w}}{dt} = \textcolor{#b35f10}{r_{pre}} \textcolor{#b80909}{r_{post}} - \textcolor{#0f73bf}{w} \textcolor{#b80909}{r_{post}^2}
```

This has two nice properties:
* The first is that as hoped for, the weights don’t keep growing.
* The second is more surprising: this learning rule extracts the first principal component of its inputs. A link between a biological learning rule and a statistical or machine learning algorithm: principal component analysis.

In the next subsection, we're going to show you how to prove this, and it gets a bit hairy. So to keep things a simpler, we’ll make a couple of not quite right assumptions.
We’ll look at the case of a vector of inputs $\bold{x}$ and a single linear output neuron $y$. So we can write $y$ as the dot product of the weight and the inputs. We’ll assume the vector $\bold{x}$ represents the pre-synaptic firing rates, but we’re also going to assume it has mean value 0. This won’t change the result but makes the analysis simpler.
With these assumptions, we can rewrite the learning rules like this, using a dot above the w to indicate a derivative, and replacing 1 over time constant with gamma.

```{math}
\text{Vector } \bold{x} = \textcolor{#b35f10}{r_{pre}} \text{ has mean value } \langle \mathbf{x} \rangle = 0
```

```{math}
\text{Single neuron output scalar } y = \sum_i w_i x_i = \bold{w}^T \bold{x} = \bold{w \cdot x}
```

```{figure} figures/Picture3.png
:label: single-vector
:width: 250px
:align: center
```

With these assumptions, we can rewrite the learning rules [like this](#learning-rule-eq), using a dot above the $\bold{w}$ to indicate a derivative, and replacing 1 over time constant with $\gamma$.

(learning-rule-eq)=
```{math}
\bold{\dot{w}} = \gamma (\bold{x} y - \bold{w} y^2) \text{ where } \gamma = \frac{1}{\tau_w}
```

## Oja's Learning Rule Analysis

Now let's get into the hairy mathematical analysis. You might want to work through this on a piece of paper. We start with the same setup up as [before](#single-vector), with a vector of inputs and a single output.
The rate of change of the weight follows [this equation](#weigth-eq), and the output $y$ [this equation](#output-eq). Note that since $y$ is a scalar, the dot product of weight $\bold{w}$ and input $\bold{x}$, we can write $\bold{w}^T \bold{x}$ or $\bold{x}^T \bold{w}$.

(weigth-eq)=
```{math}
\bold{\dot{w}} = \gamma (\bold{x} y - \bold{w} y^2)
```

(output-eq)=
```{math}
y= \bold{w}^T \bold{x} = \bold{x}^T \bold{w}
```

We’re interested in the fixed point after a long period of time when no more learning is happening. In other words, when on average the rate of change of $\bold{w}$ is 0. We're using the angle brackets to mean an average.

```{math}
\langle \bold{\dot{w}} \rangle = 0
```

Expanding out w dot, we get this:

```{math}
\langle \bold{x} y - \bold{w} y^2 \rangle = 0
```

Replacing $y$ with either $\bold{w}^T \bold{x}$ or $\bold{x}^T \bold{w}$ you get [this](#expanded-eq) – you’ll see why we did it like this in a moment.

(expanded-eq)=
```{math}
\bold{\langle x(x}^T \bold{w) \rangle - \langle (w}^T \bold{x)(x}^T \bold{w)w \rangle} = 0
```

Note that the mean of $\bold{x x}^T$ is $\bold{C}$ the covariance matrix of $\bold{x}$. Using this, we can rewrite the equation [above](#expanded-eq) like [this](#covariance-eq).

(covariance-eq)=
```{math}
\bold{Cw - w}^T \bold{Cww} = 0
```

Since $\bold{w}^T \bold{Cw}$ is a scalar, which we'll call $\lambda$, [this equation](#covariance-eq) becomes:

```{math}
\bold{Cw} = \lambda \bold{w} \text{ where } \lambda = \bold{w}^T \bold{Cw} \text{ is a scalar}
```

But this means that $\lambda$ is an eigenvalue of $\bold{C}$ and $\bold{w}$ is an eigenvector.
This is exactly the definition of $\bold{w}$ being a **principal component**. In fact, you can show that Oja’s rule will always converge to the first principal component, but that analysis is more complicated. Because we get this fixed point of $\bold{w}$, we know that $\bold{w}$ doesn’t grow without bounds, and we can actually show a bit more than that by computing the fixed point of the norm of $\bold{w}$.

(norm-eq)=
```{math}
|\bold{w}|^2 = \bold{w}^T \bold{w}
```

We set its derivative to 0, and by expanding that out in components we can see that this is $\bold{w}^T \frac{d}{dt} \bold{w}$, which give us the following:

```{math}
0 = \langle \frac{d}{dt} \bold{w} ^T \bold{w} \rangle = \langle \frac{d}{dt} \sum_i w_i^2 \rangle = \langle \sum_i 2 \dot{w_i} w_i \rangle = 2 \langle \bold{w}^T \bold{\dot{w}} \rangle
```

Expanding the derivative out using the formula we calculated [above](#norm-eq), we get a second $\lambda$ term appearing and this simplifies to $\lambda (1 - |\bold{w}|^2)$.

```{math}
\begin{gather}
0 = \langle \bold{w} ^T \bold{\dot{w}} \rangle = \bold{w}^T (\bold{Cw -} \lambda \bold{w}) \\
= \bold{w}^T \bold{Cw} - \lambda \bold{w}^T \bold{w} = \lambda \bold{w} - \lambda \bold{w}^T \bold{w} \\
= \lambda (1 - |\bold{w}|^2)
\end{gather}
```

In other words, we get weight normalisation from Oja’s rule.

## BCM Rule

We’ve seen that Oja’s rule keeps weights bounded and leads to the neuron learning principal components. Another approach is the BCM rule, named after Bienenstock, Cooper and Munro.
Their aim was a model of the development of selectivity in the visual cortex, and so they set out to find a learning rule that maximises selectivity. They defined this as 1 minus the mean response of the network divided by the maximum. In other words, for high selectivity, overall, the network should respond very little, but for certain inputs it should have a very strong response.

```{math}
1 - \frac{\text{mean ressponse of network}}{\text{maximum response of network}}
```

Their rule multiplies the simple [Hebbian rule](#hebbian-rule-eq) from before with a term that can be positive or negative. It’s positive if the postsynaptic firing rate is high, and negative if it’s low. High and low and controlled by a threshold $\textcolor{#18a326}{\theta}$.

```{math}
\textcolor{purple}{\tau_w} \frac{d \textcolor{#0f73bf}{w}}{dt} = \textcolor{#b35f10}{r_{pre}} \textcolor{#b80909}{r_{post}} (\textcolor{#b80909}{r_{post}} - \textcolor{#18a326}{\theta})
```

This means that synapses can get stronger or weaker. The intuition here is that it promotes selectivity because those inputs that cause a high firing rate will be driven even higher, and those with a low firing rate even lower. However, you can already see that if $\textcolor{#18a326}{\theta}$ is just a constant, there’s nothing to stop this from blowing up. Once the post-synaptic firing rate goes above $\textcolor{#18a326}{\theta}$ it can just keep growing without bounds.

```{figure} figures/Picture4.png
:align: center
:width: 300px
```

So they made theta into a dynamic variable that averages the square of the postsynaptic firing rate. If you imagine a learning environment where different inputs are constantly being presented, you can imagine this either as a running average over time, or as a running average over the whole dataset.

```{math}
\textcolor{#18a326}{\theta} = \langle \textcolor{#b80909}{r_{post}^2} \rangle
```

Now if $\textcolor{#b80909}{r_{post}}$ gets too large, the threshold will increase and the sign changes to negative and the synapse will weaken. Similarly, if $\textcolor{#b80909}{r_{post}}$ gets too small it will strengthen it. So the output rate shouldn't go to 0 or $\infty$

(BCM-selectivity)=
It also encourages selectivity because if $\textcolor{#b80909}{r_{post}}$ is equal to $\textcolor{#18a326}{\theta}$ as it would be if all inputs led to the same output, then you’d be at this fixed point. This is unstable as a slight perturbation would push you away from the fixed point. Hence this rule is making sure you don’t settle in a fixed point where the selectivity is low. You can imagine that at the start of learning, the output firing rate is low for all inputs, but higher for some than others. The threshold will lower until one of the inputs gives a rate above the threshold. At that point, the weights that respond to this input will get stronger. This increase in the firing rate will cause the threshold to increase, and so the weights corresponding to all the other inputs will decrease, and the network will end up selective to just that one preferred input.

Sure enough, you can see that happening in [their model](#model-graph) of orientation selectivity in the visual cortex. Over time, the selectivity increases, and the end result is a network with a strong preference for one particular orientation. This qualitatively matches what happens in development.

```{figure} figures/Picture5.png
:label: model-graph
:align: center
:width: 500px

Model of orientation selectivity in the visual cortex
```

Incidentally, you might wonder why do we take the square of the firing rate. Well, the exact choice of taking the square isn’t essential. It’s only important that it be nonlinear for reasons we won’t go in to here.

## Summary

We’ve seen how Hebb’s principle can be translated into learning models based on firing rates, and that this can generate quite interesting computational properties like doing principal component analysis or developing feature selectivity. However, it misses an important feature which is the timing of spikes. 

:::{seealso} That's it!
We’ll turn to that in the next section.
:::