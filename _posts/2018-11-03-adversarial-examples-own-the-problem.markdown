---
layout: default
title:  "Adversarial Examples and Owning the Problem"
date:   2018-11-02 15:48:26 -0500
categories: writing
comments: true
---

# Adversarial Examples

I do a lot of work in the area of *adversarial* machine learning, which aims
to characterize a machine learning model's predictions for
challenging unseen inputs (not to be confused with generative adversarial networks
(GANs)). Despite having worked in this area
for some time, I still struggle with the basic definition of
an *adversarial example*, as proposed by [OpenAI](https://blog.openai.com/adversarial-example-research/):

> Adversarial examples are inputs to machine learning models that an attacker has
intentionally designed to cause the model to make a mistake; they’re like optical
illusions for machines.

Aside from being informal, my problem with this definition is that it tends to
absolve designers of their responsibility to a reasonable standard of care, it
doesn't __own the problem__. By focusing on the
role of the _attacker_, it makes it seem as though the designer has done their
absolute best, and that such unpredictable behaviour at test time is simply
beyond one's control. After all, I am not aware of any fix to make humans immune
to optical illusions.

At least this definition is more general than arbitrary constraints that invoke
an $$L_p$$ norm directly on the inputs like $$ \| x - x_{adv} \|_p  \leq \epsilon $$,
but I believe this fundamental emphasis on security, instead of say, stability
or fault tolerance, is limiting. The unfortunate reality is that
state-of-the-art deep neural nets for computer vision are overwhelmingly
unstable, i.e., a small change in the bounded input may induce a large change in
the output. As designers of useful systems, we would like to avoid
instability __in general__, not only in security.

Optical illusions simply do not arise that often in nature, and when they do,
humans are generally aware that their visual system is being manipulated, and
proceed with caution. One could argue that adversarial examples are similarly
rare, given that they might violate the independent and identically distributed
(i.i.d) data assumption fundamental to machine learning. Recently, this latter view
has been taken for granted, but a dependence between an example and the
model's parameters does not necessarily imply that the example is from a different
distribution, we usually don't know the distribution from which our inputs are
drawn, which is why we're using machine learning. The tendency to question the
i.i.d. assumption, a fundamental assumption made by a whole community,
rather than assuming user error, demonstrates how the aforementioned
security-centric definition can be wielded to absolve the designer of
responsibility.

I don't have a significantly better definition of an *adversarial example*,
recent work is concerned with the design of better methodology for evaluating
robustness that would render
such definitions somewhat unnecessary. However, I would shorten the previous one
by removing the necessary role of an attacker, and stipulate that we're really
concerned with confident mistakes (where a suitable confidence threshold is
application specific). Something like:
> Adversarial examples are bounded inputs to machine learning models for which the
prediction differs with high-confidence from that of an oracle.

I continue to use *adversarial examples* in relation to the problem of maximizing out-of-sample
performance, e.g., max-margin generalization, and ensuring model confidence is
calibrated *for all* legitimate inputs. Naturally, following the gradient is one
of the quickest ways to test these things, but I consider myself an ally not an
adversary; a designer of stable systems, not defense mechanisms.
