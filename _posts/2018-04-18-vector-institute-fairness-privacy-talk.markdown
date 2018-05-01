---
layout: default
title:  "Invited Talk at Vector Institute on Fairness and Privacy"
date:   2018-04-18 15:48:26 -0500
categories: writing
comments: true
---

<a href="{{site.url}}/dl/ESS_Fairness_Privacy_Program.pdf">Full program</a> for Vector Endless Summer School on Fairness and Privacy, April 18, 2018.

# Abstract

Learning models that generalize to novel data is the ultimate goal
in machine learning. The adversarial examples phenomenon
challenges the notion that the current instantiation of deep neural
networks generalize well, or that they are aware of what they
don’t know. I’ll introduce various threat models in the adversarial
setting, demonstrate practical attacks, and explore limitations of
using threat models to characterize robustness. I believe privacy,
“interpretability”, and generalization can be satisfied
concurrently, and will summarize our recent work bridging these
areas.

# Summary

I suggested that generalization, privacy, and fairness in machine 
learning systems are complementary objectives. In layperson terms, 
a machine learning model is said to generalize well if it models 
the data generating distribution, but not the actual data.

Since we usually don't know the real data generating distribution 
(otherwise why use machine learning), performance on an unseen 
test set is usually used as a proxy for a model's generalization 
ability. The problem with this metric is that the data are usually
collected all at once, and the training/test splits are likely to
share many of the same global statistics and biases.

Training by maximum likelihood estimation (MLE) can be related to
electricity, in that they both take the path of least resistance
to satisfy their goals: empirical risk minimization in the former, and 
energy dissipation in the latter.

Adversarial attacks serve to highlight issues relating to fairness and 
privacy arising due to MLE in the high-capacity setting. One aspect 
of privacy could be a model's tendency to reveal secrets in the 
training data, which we can measure by back-propgating into the 
input space such that we maximize a target class.

We observe that carefully regularized low-capacity discriminative models 
can generate plausible examples using this procedure, with no
constraints on the initial noise structure. Although the images are 
plausible, they omit fine grained detail, thus preserving some privacy. 

Top row is uniform noise, bottom row is gaussian noise with mean 0.5 and
standard deviation 0.25. Left column is for the dog class, middle is horse, 
and right is boat.

<p align="center">
  <img src="{{site.url}}/img/t5_animation.gif" width="200" height="200"/> 
  <img src="{{site.url}}/img/t7_animation.gif" width="200" height="200"/>
  <img src="{{site.url}}/img/t8_animation.gif" width="200" height="200"/> 
</p>
<p align="center">
  <img src="{{site.url}}/img/t5_gauss_animation.gif" width="200" height="200"/> 
  <img src="{{site.url}}/img/t7_gauss_animation.gif" width="200" height="200"/>
  <img src="{{site.url}}/img/t8_gauss_animation.gif" width="200" height="200"/> 
</p>