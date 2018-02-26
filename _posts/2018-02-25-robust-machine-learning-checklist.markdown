---
layout: default
title:  "A Practical Checklist for Robust Machine Learning Models"
date:   2018-02-25 15:48:26 -0500
categories: writing
---

# Introduction

This is a living document where i'm compiling a list of practical tests that 
machine learning researchers and practitioners can use to verify the robustness 
of a model, or at least tell if they are on the right track. 

But first, __why should you care about the robustness of your machine learning models__? 

After all, you have been a good, careful, student and carefully split up your dataset into 
separate training, validation, and test splits. You used common regularization techniques, 
e.g explicit methods like dropout and weight decay, or implicit methods such as early stopping, 
to make sure that you didn't overfit the training set. You used the validation set to tune your
hyper-parameters, and you experienced little generalization error on the held out test set. 
You may have even set an impressive score on a public leaderboard, or improved on a baseline 
you found in a published paper. You convince yourself you have done a good job and your model 
ought to generalize well in the real world since it does well on the test set and you didn't cheat.

Not so fast...

A plethora of machine learning models, such as decision trees, SVMs, linear regression, and 
deep neural networks (both feedforward and recurrent) have been found to fail spectacularly 
when only subtle, imperceptible changes are applied to natural examples (e.g the images in the test set for a
particular dataset). Such inputs that have been perturbed, causing models to yield undesirable
behaviour are known as __adversarial examples__. 

Undesirable behaviour could mean:

- an incorrect prediction (a sample of code is *not* malware, when it *is* in fact malware, or that a stop sign is a yield sign)

- consistently making a specific incorrect prediction (CCT cameras that consistently confuse you with a wanted criminal, 
or an autonomous car mistaking a toddler for a small animal)

- confusing a nonsensical noisy input with a natural class (an unmanned aircraft or vehicle enters a low-visibility foggy area, triggering a dangerous emergency manoever to avoid what the system believes is a white building.)

- medical instrumentation yields a noisy or incomplete measurement, causing a model to be even *more* confident in its prediction, 
when it ought to have *less* confidence (suggesting that a human should double check the result, or repair the machine).

Adversarial examples are frequently contextualized by having to do with computer or cybersecurity, which is true, but their 
implications extend far more broadly than this. Some may argue that these concerns are over exaggerated, after all adversarial
examples represent __worst case__ perturbations, and humans are known to experience similar phenomenon on occasion as well 
(e.g optical illusions). Firstly, I don't believe human vision is necessarily the gold standard for machine vision, after all 
we're trying to build autonomous cars that are safer than human drivers, not equally safe. More importantly, optical illusions
with respect to human vision are few and far between, whereas adversarial examples exist for nearly *all possible inputs
not spanned by the training set*. Humans are also very good at knowing when a situation is ambiguous, e.g for the famous Rabbit-duck
illusion, regardless of which animal you see first, few would say that the *only* legitimate answer is "100% rabbit, and 0%
duck. A more reasonable answer might be 50% rabbit, and 50% duck. Yet the former is exactly how deep neural nets tend to fail 
when presented with adversarial examples, if rabbit __or__ duck are among the top two predictions at all.

<p align="center">
  <img src="{{site.url}}/img/Duck-Rabbit_illusion.jpg" width="260" height="175"/> 
</p>

That said, I really don't want to dwell on, or emphasize any further comparisons between adversarial examples and optical illusions 
because I find it a stretch and generally unhelpful. The vast majority of adversarial examples we encounter are __anything but__ worst case
in terms of both human perception, __and__ in terms of the model. Although we typically make use the *best information 
available* (i.e the gradient of the loss wrt the input) when crafting the adversarial perturbation, the example itself 
is seldom worst case because in practice we use a *very small* perturbation size, and in high-dimensions the difference 
between natural and adversarial examples is usually imperceptible to humans. Sometimes the difference is even imperceptible 
with respect to the quantization used by popular image storage formats, i.e less than that resolvable with 8-bits. My view is that
adversarial examples commonly found in the literature are __valid examples__. We must do better.

# What can we do about it

1. Don't use orders of magnitude more parameters than the number of training examples, otherwise your model almost certainly
won't be robust. E.g a polynomial with an equal number of parameters as data points can perfectly memorize the data, 
a strategy that doesn't generalize. We don't quite know how precisely this intuition extends to say, convolutional neural networks, 
but we can be pretty certain this logic still holds roughly speaking.

2. Start with very high L2 weight decay, such that your model still learns *something*. Slowly turn down the weight decay 
after proceeding through the following checklist. 

## If you are working with images

3. Look at the convolutional filters learned in the first few layers of the neural network, paying especially
close attention to the first layer. Do they seem like general concepts or primitive image processing operations? 
Do filters for primary colours and detecting edges or colour transitions emerge? If any of the filters still resemble
random noise, they are almost certainly hurting the robustness of your model. If they aren't contributing something 
useful, they should be pruned away or exactly **zero**. This type of filter pruning is essentially what L2 regularization 
accomplishes. Also, experiment with the kernel size and stride, you would assume 
that these hyper-parameters have been well characterized, but in my experience, not really. For instance
increasing the stride from 2 to 5 for a convolutional layer with sixteen 8x8 filters, followed by one linear layer, slightly improves 
robustness a few percentage points for CIFAR-10 when subject to projected gradient descent (PGD) (unpublished result).

4. Try to generate images with your model. That's right, a classifier can be used as a generative model (albeit one 
with severe mode collapse). The most direct way to do this is to iteratively maximize one of the softmax probabilities
by performing gradient _ascent_ on an image that is initially filled with random noise (any kind of noise should suffice).
Here's some code for doing this in TensorFlow, note that here i'm minimizing the loss for a target label, which is the same as
maximizing the softmax probability for that label.

Listing 1: Generating examples with a discriminative model via gradient ascent

```python
#Create placeholders for input and labels
x = tf.placeholder(tf.float32, shape=(None, img_rows, img_cols, channels))
y_ = tf.placeholder(tf.float32, shape=(None, nb_classes))

#logits = model(x, y_) or something like that here ...

preds = tf.nn.softmax(logits)

# Define training loss
total_loss = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(labels=y_, logits=logits))

'''
Get gradient of the training loss with respect to the input,
an use the sign of the gradient or the actual gradient 
normalized to have unit L2 norm (yields slightly cleaner images)
'''
grad_wrt_input, = tf.gradients(total_loss, x)
red_ind = list(range(1, len(x.get_shape())))
square = tf.reduce_sum(tf.square(grad_wrt_input),
                       reduction_indices=red_ind,
                       keep_dims=True)
normalized_grad = grad_wrt_input / tf.sqrt(square)
ascend_x = tf.clip_by_value(x - normalized_grad, 0, 1)

# Sign method, eps_ is a placeholder for the step size, I used 0.01
#ascend_x = tf.clip_by_value(x - eps_ * tf.sign(grad_wrt_input), 0, 1)

target_labels = np.zeros((1, nb_classes))

# Generate examples with the model
for i in range(nb_classes):
	fig = plt.figure(figsize=(4, 4))
	ax1 = fig.add_subplot(111)
	ax1.get_xaxis().set_visible(False)
	ax1.get_yaxis().set_visible(False)

	# Initialize new noise image
	img = np.random.rand(1, img_rows, img_cols, channels) / 10

	target_labels[0, :] = 0 # set previous label back to zero
	target_labels[0, i] = 1 # set current label active

    for j in tqdm(range(100)):
        img = sess.run(ascend_x, feed_dict={x: img, y_: labels})

        if j % DISPLAY_INTERVAL == 0:
            ax1.imshow(adv_img.reshape(img_rows, img_cols, channels))
            plt.pause(0.01)
	
	# Return the prediction confidence for the final image
    conf = sess.run(preds, feed_dict={x: adv_img})
```

Here's a CIFAR-10 horse generated using the sign method procedure from Listing 1. 
The reason for the very dark areas along the front leg, neck, shoulder and 
top part of the background is because the noise image was initialized to have zero mean, and 
the image is also clipped at zero. Also, the step size of 0.01 corresponds to a roughly
6.6bit resolution. It is predicted to be a horse by this particular model 
with 33.5% confidence. This seems reasonable, not too low, and not too high. Although 
it's not a perfect sample, we can be fairly confident that it's a horse compared to 
the other CIFAR-10 classes (e.g a ship or frog). This example wasn't cherry picked, 
all classes are predicted with between 28% and 54% confidence.

<p align="center">
  <img src="{{site.url}}/img/cifar10_7_conf335.png" width="100" height="100"/> 
</p>

If your model can't generate anything resembling a natural image, but makes a very confident prediction, it's not robust. 
If your model does generate a natural image, and does so with moderately high confidence, that's still okay. This is a
very tough test for higher-dimensional problems and requires human judgment to interpret. Confidence should be proportional to how good the image is 
in your opinion. That said, confidence should never exceed 95% for a 10-way classification. It's common for bad models to be as confident as 99.9 or 100% 
confident on this test for unrecognizable images, e.g see [Deep Neural Networks are Easily Fooled: High Confidence Predictions for Unrecognizable Images](https://arxiv.org/abs/1412.1897).

# In general

Many of these suggestions are paraphrased from: https://github.com/anishathalye/obfuscated-gradients

6. Ensure that black-box attacks are strictly weaker than white-box gradient based attacks. 
Otherwise, you model is *gradient masking*, making it difficult to measure how robust it will be in practice. 
At best, in this case it is slightly less robust than suggested by the black-box result.

7. Ensure that unbounded attacks can cause 100% error. Given sufficient perturbation, it should always be possible
to convert an example to one of the other class. This does not mean your model is not robust, but if you can't get down to 100% error, 
it's likely masking gradients, and a trivial modification to the attack should be able to succeed. If you look at the actual examples
that cause this 100% error rate, you should notice that they are bi-stable, or ambiguous, even to a human. They will also likely have high
distortion relative to the starting examples. This means your model is doing a good job, because these examples will seldom be encountered in the real-world. The whole idea behind adversarial examples is that they are normally imperceptible. 

8. Use strong attacks. For linear models, the fast gradient sign method is exact. For anything else, this is insufficient for assessing robustness, use an iterative and ideally optimizer (e.g Adam) based attack, with a small random perturbation first.

9. The easiest way to directly compare with the literature is to use the same perturbation size, (frequently denoted as epsilon), for the same
norm bound (e.g 2-norm, or infinity-norm). But this is not a comprehensive measure of robustness. 

# Deep neural networks are over-parameterized 

You will notice that it's extremely difficult to do well on any of these tests with 
a state-of-the-art deep neural network. If you still aren't convinced that deep neural networks 
are highly over-parameterized with respect to their ability to generalize
in a meaningful way, perform a randomization test, similar to that from non-parametric statistics. 
Randomly replace all the labels in your training set. A __good__ model should have a __challenging__ time 
fitting randomly assigned labels, since there will be no useful correlation between the features and the label. 
This test was previously used in [Understanding deep learning requires rethinking generalization](https://arxiv.org/abs/1611.03530) 
to show that test error is a poor measure of the generalization ability of deep neural networks (DNNs). 
Without changing anything about the model (e.g the architecture or the way it is trained), 
generalization error was easily manipulated by randomly swapping labels. Their models were able to 
achieve 0 training error on random labels, but (as one would expect) predicted no better than chance
on the test set. Clearly it is undesirable for a measure of a model's ability to generalize to 
be affected by making changes to the __data only__, without changing the __model__.

A different but related work [Intriguing Properties of Randomly Weighted Networks: Generalizing while Learning Next to Nothing](https://openreview.net/forum?id=Hy-w-2PSf) essentially showed the same result but in a different way, by leaving the majority of weights in a DNN randomly initialized, and only
training the last few layers, or a subset of filters. They found that only learning a small fraction of the parameters had a surprisingly negligible effect on test performance. Clearly these superfluous parameters are not contributing to meaningful generalization, and are a liability 
in terms of robustness. You want to do fine-grained text to image generation with attention? Machine translation? 
You bet we'll get better at these tasks too when we stop focusing on improving upon meaningless baselines, and instead
start with small, well regularized models whose components we deeply understand.

This approach may feel cumbersome, and overly nitpicky, but I truly believe that exciting things lie ahead
for the field of machine learning if we can move beyond *defending against adversarial examples* as a 
novel research area in and of itself, and instead focus on robustness __by design__. I'm not convinced we'll ever *fix*
adversarial examples for current state-of-the-art models, but by reconsidering what it means to be state-of-the-art, 
and using a principled approach, we __will__ build robust models that __do work well__ in practice, and that only fail
gracefully.