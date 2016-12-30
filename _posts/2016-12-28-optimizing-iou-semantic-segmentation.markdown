---
layout: default
title:  "Optimize IoU for Semantic Segmentation in TensorFlow"
date:   2016-12-28 13:48:26 -0500
categories: writing
---

{% include mathjax_config %}

# Introduction

Intersection over union (IoU) is a standard benchmark for assessing performance in semantic segmentation tasks. If we devise a loss function for a deep network to perform segmentation with the mindset of classification and cross-entropy, we might end up with something like this:

Listing 1: Pixelwise softmax cross-entropy loss

```python
logits=tf.reshape(logits, (-1, FLAGS.num_classes))
trn_labels=tf.reshape(trn_labels_batch, [-1])
cross_entropy=tf.nn.sparse_softmax_cross_entropy_with_logits(logits,trn_labels,name='x_ent')
total_loss=tf.reduce_mean(cross_entropy, name='x_ent_mean')
train_op=tf.train.AdamOptimizer(FLAGS.learning_rate).minimize(total_loss,global_step=global_step)

# For inference/visualization
prediction = tf.argmax(tf.reshape(tf.nn.softmax(logits), tf.shape(vgg.up)), dimension=3)
```

Instead, as per the recent work of [Y.Wang et al](http://www.cs.umanitoba.ca/~ywang/papers/isvc16.pdf "Optimizing Intersection-Over-Union in Deep
Neural Networks for Image Segmentation"), a scheme that optimizes IoU directly:

\begin{equation}
I(X) = \sum_{v \in V} X_v \times Y_v
\end{equation}

\begin{equation}
U(X) = \sum_{v \in V} X_v + Y_v - x_V \times Y_v
\end{equation}

\begin{equation}
IoU = \frac{I(X)}{U(X)}
\end{equation}

\begin{equation}
loss = 1.0 - IoU
\end{equation}


Listing 2: IoU loss in TensorFlow, not shown is the sigmoid non-linearity at output in lieu of ReLU.

```python
logits=tf.reshape(logits, (-1,FLAGS.num_classes))
trn_labels=tf.reshape(trn_labels_batch, (-1,FLAGS.num_classes))

# Eq. (1)
inter=tf.reduce_sum(tf.mul(logits,trn_labels))

# Eq. (2)
union=tf.reduce_sum(tf.sub(tf.add(logits,trn_labels),tf.mul(logits,trn_labels)))

# Eq. (4)
total_loss=tf.sub(tf.constant(1.0, dtype=tf.float32),tf.div(inter,union))

# For inference/visualization
valid_prediction=tf.reshape(logits,tf.shape(vgg.up))
```