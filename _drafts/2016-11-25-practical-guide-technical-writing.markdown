---
layout: default
title:  "Practical Guide Technical Writing Engineering"
date:   2016-11-25 13:48:26 -0500
categories: writing
---

{% include mathjax_config %}


# Introduction

This is a technical writing guide intended for undergraduate engineering
students at my school. Your mileage may vary.

*The purpose is a specific, attainable, quantifiable goal, in terminology appropriate for the field, but that one of your peers not taking the class could be reasonably expected to understand. A generic example is provided below, while the notation __ENGG*4420__ is used to suggest examples for the Real Time course. Some examples have been reproduced or adapted from previous reports with permission.*

## Problem Description

A resource for scientific technical writing in undergraduate Engineering courses is absent. To address this information gap, a set of guidelines that make specific recommendations regarding the use of equations, figures, and writing style, are proposed.

__ENGG*4420__

+ The purpose of this lab was to implement a quarter-car suspension model in LabVIEW, and compare the performance of a passive system to that of a semi-active linear quadratic regulator (LQR) controlled system. Several performance measures were devised, including the vertical acceleration of the quarter car sprung mass, and suspension deflection, when the model was subject to sinusoidal and step inputs ...

+ The model was to be architechted using a modular plant model with the LQR controller in a separate LabVIEW timed loop, such that the controller could be evaluated deterministically in LabVIEW RTOS, and more easily scaled to a full-car model.

# Background

The background or theory section may contain equations, some in text, as in $ PV=nRT $, and some on their own, as in \eqref{eq:softmax}. Useful equations we wish to refer to later in text, are defined on their own line, centered, and with the equation number flush to the right.

$$
\begin{equation} \label{eq:softmax}
	y_i = \frac{exp(z_i)}{\sum\limits_j exp(z_j)}
\end{equation}
$$

The quotient rule is applied to \eqref{eq:softmax} to obtain the gradient, $ \frac{\partial{y_i}}{\partial{z_j}} $, for the case when $ j = i $. Given that, $ \frac{\partial{\sum_j exp(z_j)}}{\partial{z_j}} = exp(z_i) $, and not labeling equations corresponding to intermediate steps, $\frac{\partial{y_i}}{\partial{z_j}} $ can be evaluated as:

$$
	\frac{\partial{y_i}}{\partial{z_i}} = \frac{ exp(z_i) \cdot \sum_j exp(z_j) - exp(z_i) \cdot exp(z_i) }{ \big({\sum_j exp(z_j)}\big)^2}
$$

$$
	\frac{\partial{y_i}}{\partial{z_i}} = \frac{ exp(z_i) \cdot \sum_j exp(z_j) - exp(z_i) \cdot exp(z_i) }{ \sum_j exp(z_j) \sum_j exp(z_j) }
$$

$$
	\frac{\partial{y_i}}{\partial{z_i}} = \frac{ exp(z_i) \big( \sum_j exp(z_j) - exp(z_i) \big) }{ \sum_j exp(z_j) \sum_j exp(z_j) }
$$

\begin{equation} \label{eq:penultimate}
	\frac{\partial{y_i}}{\partial{z_i}} = \frac{exp(z_i)}{\sum_j exp(z_j)} \cdot \big( 1 - \frac{exp(z_i)}{\sum_j exp(z_j)} \big)
\end{equation}

Recognizing that \eqref{eq:penultimate} is composed of \eqref{eq:softmax}, \eqref{eq:penultimate} can be reduced to \eqref{eq:grad_i_eq_j}.

\begin{equation} \label{eq:grad_i_eq_j}
	\frac{\partial{y_i}}{\partial{z_i}} = y_i \cdot \big( 1 - y_i \big)
\end{equation}

*There is universal agreement in the Engineering community on formatting equations as above, however this is IEEE inspired in how equations are referred to in text, with only circle braces. You don't have to use IEEE style, but do always use circle braces. Equation (1), equation (1), eq. (1) are also accepted.*

The class of image $f$, is taken to be that of the template, $t$, corresponding to the maximum correlation coefficient, $\gamma$, in the normalized 2D cross-correlation \cite{match_template} given by \eqref{eq:ncc}. In \eqref{eq:ncc}, $\overline{t}$ is the template mean, and $\overline{f}_{u,v}$ is the image mean in the region $f(x,y)$ spanned by $t$ centered at $u$,$v$.

$$
\begin{equation} \label{eq:ncc}
 \gamma(u,v) = \frac{\sum_{x,y}[f(x,y)-\overline{f}_{u,v}][t(x-u, y-v)-\overline{t}]}{\sqrt{\sum_{x,y}[f(x,y)-\overline{f}_{u,v}]^2[t(x-u, y-v)-\overline{t}]^2}}
 \end{equation}
$$

__ENGG*4420__

+ The vertical acceleration of the sprung mass is the dominant force experienced by a vehicle's occupants, and is therefore a suitable proxy for ride quality ...

+ An additional goal of a suspension system is to maintain good road handling on a variety of surfaces. Tire deflection is a good
measure of how effective the suspension system is at road handling ...

+ A suspension system also has to support the vehicle's static weight under gravity. It was required that the suspension deflection remain within fixed physical bounds at all times under this load, and for all road disturbances in the design specification ...

+ Letting $x_1$ be the suspension deflection, $x_2$ the absolute velocity of the sprung mass, $x_3$ the tire deflection, and $x_4$ the velocity of the unsprung mass, we may represent the passive suspension system in state space form in \eqref{eq:ss-passive}.

\begin{equation}
  \label{eq:ss-passive}
  \dot{X} = AX + L\dot{z_r}
\end{equation}

Adding a variable damper, $B_{semi}$, with matrix N to \eqref{eq:ss-passive} results in the semi-active model \eqref{eq:ss-semi}.

\begin{equation}
  \label{eq:ss-semi}
  \dot{X} = AX + NXB_{semi} + L\dot{z_r}
\end{equation}

tions using only a handful of blocks. Examples of these were; a bumpy road simulated
by a sine wave, a flat road simulated by a constant, and a sharp curb simulated by a step input.

+ It is expensive to find the perfect road conditions to test on, and by using
LabVIEW one can save a lot of time by simulating the input that is required rather than creating the physical real world conditions.

+ LabVIEW allows one to simulate smaller components in isolation, prior
to testing a complete system. In this lab, several assumptions are made regarding
the tire/road interface, and vehicle body to simulate the suspension of a quarter car
section. This is very challenging to do in the real world where these assumptions to not hold.

+ In this manner, a system can be thoroughly tested before going into production,
knowing how that system will behave under all road conditions and the specifications of all
the components.

+ Perhaps the magnetic fluid used in variable dashpots has not yet been invented, but we wish to determine if there is any advantage to semi-active suspension systems in terms of their dynamics. Knowing how dashpots behave in general, opposing motion with a force proportional to the difference in velocity of the objects connected to it on either end, we can proceed with a simulation of how the technology *would* work, if all the technology was in place.

# Implementation

*Depending on the course, this section might be called __Detailed Design__, __Methodology__ or something entirely different. Regardless, this is where you want to present the work that you did, and any novel contributions as clearly and effectively as possible.*

## Figures

Every figure should communicate something that you can't quite do effectively with words. Every figure must have a purpose and be legible to someone with normal human vision. Figure 1 shows how momentum velocity affects learning in a multi-layer perceptron (MLP), with cross-entropy at epoch 250 labelled clearly, to let the reader easily compare different settings for $\alpha$. Don't be afraid to write a long caption. The one in Figure 1 is on the shorter end. Imagine the page containing your Figure has been separated from the report, is the caption descriptive enough for someone to make sense of it?

![MLP]({{site.url}}/img/mlp.png){: height="600px" width="800px"}
Figure 1: MLP cross entropy for training and test sets with four settings of momentum velocity, $\alpha$, up to 500 training epochs, a fixed learning rate of 0.01, and 250 hidden units.

*If you calculated something by legitimately __using__ a figure or reading a value from a curve, then say so, but be as specific as possible. Mostly likely this __isn't__ the case for the Real Time* course.

The Reynold's number, Re, was read from Figure 2, with friction factor, $ f $, and relative roughness, $ k/d $, and given the assumptions stated in Section 2.

Figure 2. Not reproduced due to excessive file size. Available **https://grantingram.wordpress.com/2009/04/22/moody-diagram/**

If someone else generated a figure that you would like to reuse, and you have their *permission*, state that it has been __reproduced__. It's not sufficient to cite the source of the figure, this implies that you created the figure yourself, you were simply inspired by their work. If you substantially modified someone else's figure, it's fair to say __adapted__ instead. If you generated a figure with someone else's data, you can simply cite the source of the data. The Python source code for Figure 2 is GNU GPL licensed, so it's fair game.

__ENGG*4420__

*If it's the lab manual for the course, you can assume you have permission to reproduce, but allow me to make a plea as to why you shouldn't take screenshots of equations and paste them into the lab manual.*

+ *It wouldn't be accepted in any other context, so why learn a bad habit now?*

+ *It is much harder to mindlessly throw equations into your report without context, after going through the effort of type-setting them yourself.*

+ *There is probably research to suggest that it makes me subconsciously biased against the rest of the report.*

+ *What should you even call that thing? A Figure? It should be an equation, or a matrix!*

## Source Code

*What about your source code? The truth is, no one wants to look at raw source code in the body of a report, especially not a raster graphic screenshot of the code from the Eclipse IDE. If you must show code, keep it short, to the point, and formatted. If you are using Latex, __'lstinputlisting[]'__ is your friend. Otherwise, paste the code as __text__, not a raster graphic. A good way to keep the code clean if you don't want to comment it is to refer to specific listing, in the same way that you refer to a figure. Please, don't refer to __the code above/below__, as it sometimes gets pushed 3 pages later when the report is said and done.*

Listing 1: Python source for multinomial logistic regression while sweeping the number of principal components representing a cropped and downsampled image.

```python
for i in xrange(n_samples):
    for c in xrange(start_class, end_class+1):     
        # Predict with j transposed principal components    
        p=logreg.predict(pca.components_.T[j].reshape(1,-1))
```       

__ENGG*4420__

The counting\_task, shown in Listing 2, increments a counter when LCD\_count semaphore is posted in __event\_task__, up to COUNT\_MAX value, then rolls over to 0.

Listing 2: Altera __counting\_task__ with priority 9.

{% highlight ruby %}
void counting_task(IOdevices_control *IOdevices) {

	INT8U err1, err2;
	INT8S count_i = -1;

	while (1)
	{
		OSSemPend(LCD_count, 0, &err1);
		if(OS_NO_ERR == err1)
		{
			count_i = (count_i + 1) % COUNT_MAX;
			printf("%4d\n", count_i);
		}
	}
}
{% endhighlight %}

# Results

*There are many ways to present your results, certain things are best illustrated with figures, others with tables. Say at least one thing that is non-obvious, and insightful, regarding each of the figures.*

![MLP]({{site.url}}/img/canny_sigma_1_3_hough.png)

Figure 3: (*Left*) Output of Canny edge detector with Gaussian smoothing parameter, $ \sigma $, equal to 1, 2, and 3, for rows 1, 2, and 3 respectively. (*Right*) Resulting probabilistic Hough lines \cite{prob_hough}, with line gap of 3px, and minimum line length of 25px. Produced with scikit-image Python library \cite{scikit-image}.

*Some figures compare many things at once and have an inherrent structure, as in Figure 3. In this case, use circle braces and italics to explain each section of the figure where natural to do so. The style you use for this doesn't matter as long as it is consistent and sufficiently descriptive.*

An encouraging result was obtained when Listing 1 was run with $ds\_v$, $ds\_h$ = 2, and keeping only the first three principal components, yielding 100% accuracy. For the sake of minimizing the algorithm execution time, the downsampling factors $ds\_v$ and $ds\_h$ were incremented by hand in steps of one until the accuracy began to drop. It was found that $ds\_v$ and $ds\_h$ could be increased all the way to 21 while maintaining 100% accuracy, where nearly all of the detail was lost in terms of what is visible to the human eye. Despite losing much of the image content, a suitable representation for the classification task could be obtained, yielding the results summarized in Table 1.

Table 1: Three-class logistic regression classification accuracy for dataset X. Principal components from cropped and downsampled $ 3 \times 15 $ px images as features.

| Principal-components | #-Correct | Accuracy (%) |
--- | --- | ---
1 | 19 | 48.7
2 | 30 | 76.9
3 | 37 | 94.9
4 | 36 | 92.3
5 | 36 | 92.3
6 | 37 | 94.9
7 | 37 | 94.9
8 | 38 | 97.4
9 | 38 | 97.4
10 | 39 | 100.0

__ENGG*4420__

*Knowledge of control theory is not assumed, but systems concepts like position, velocity, and acceleration are fair game.*


+ The set of weights $ \rho_1 $ resulted in 30% less suspension deflection than $ \rho_2 $

+ The maximum velocity occurs at t = x, halfway to the maximum suspension deflection ...

+ The maxium acceleration occurs at t=0, the instant the step is applied, and gradually decreases until ...

# Conclusion

*Try to find a clever way to avoid a boring boilerplate ending that begins with "In summary" or "To conclude". The Conclusion is not for leftovers, i.e things you couldn't fit into the discussion. You shouldn't need to reference figures nor equations. You are trying to take a step back, see the big picture, and make sense of what was found in the Results section. What were the most important findings, and why do they matter to your audience?*

__ENGG*4420__

+ Both passive and semi-active suspension systems were implemented in LabVIEW, and evaluated against the road disturbances from the formal design specifications. It was found that a semi-active system suppressed un-desireable transient behaviour, reducing peak vertical acceleration by 50%, and settling time by 70%, for step input. There was however, little benefit to the variable damper under steady state harmonic inputs which is analogous to driving at constant speed on a washboard road surface ...

+ It was found that, among two sets of weights that penalized various performance characteristics in the LQR objective function, the set that more heavily penalized X resulted in Y.

# Epilogue

## The Restaurant

Imagine you own a small restaurant that serves appetizers, entrees, drinks, and
deserts. Your profit margin is the highest on drinks and deserts, naturally you
would like to sell as many drinks and deserts as possible. If the appetizers and
entrees are lousy, patrons are unlikely to order desert. If the water is foul,
do you think your patrons will order cocktails?

Illegible figures, lack of punctuation (e.g *let's eat Grandma* vs. *let's eat,
Grandma*), and ambiguous wording are all things that contribute to a poor dining
experience in this mythical restaurant that is your report. If blurry figures
and imprecise wording are the entrees, then verbosity is the free bread, or
chips and salsa, at the restaurant. You want your patrons, the reader, to save
room for desert, but verbosity will quickly satisfy their appetite for more.

## Quoth the Raven Furthermore

Watch out for your use of words like further, furthermore, and additionally.
These words are like salt and pepper, their appropriate use can enhance the
flavour of the meal, but if the waiter empties the contents of the salt and
pepper shakers on to your plate, the result is quite unpleasant. Nevermore
should you begin a paragraph with furthermore.

### To do

+ fix citations
