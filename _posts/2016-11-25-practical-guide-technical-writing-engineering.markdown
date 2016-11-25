---
layout: post
title:  "A Practical Guide to Technical Writing in Engineering"
date:   2016-11-25 13:48:26 -0500
categories: writing
---

# Introduction

*Despite what you may have been led to believe, the __purpose__ or __problem description__ is not to "develop deep knowledge" or "learn how to use the software". The purpose is a specific, attainable, quantifiable goal, in terminology appropriate for the field, but that one of your peers not taking the class could be reasonably expected to understand. A generic example is provided below, while the notation __ENGG*4420__ is used to suggest examples for the Real Time course. Some examples have been reproduced or adapted from previous reports with permission.*

## Problem Description

A comprehensive resource for scientific technical writing in undergraduate Engineering courses is absent. To address this information gap, a set of guidelines that make specific recommendations regarding the use of equations, figures, and writing style, are proposed.

__ENGG*4420__

+ The purpose of this lab was to implement a quarter-car suspension model in LabVIEW, and compare the performance of a passive system to that of a semi-active linear quadratic regulator (LQR) controlled system. Several performance measures were devised, including the vertical acceleration of the quarter car sprung mass, and suspension deflection, when the model was subject to sinusoidal and step inputs ...

+ The model was to be architechted using a modular plant model with the LQR controller in a separate LabVIEW timed loop, such that the controller could be evaluated deterministically in LabVIEW RTOS, and in future easily scaled up to a full-car model.


{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
