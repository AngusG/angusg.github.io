---
layout: post
title:  "Regex used to convert LaTeX to KaTeX"
date:   2016-12-28 12:48:26 -0500
categories: writing
---

Partial differential equations

{% highlight ruby %}
\\pdv{([a-z_]+)}{([a-z_]+)}
\\frac{\\partial{$1}}{\\partial{$2}}
{% endhighlight %}

Sections

\\section{([A-Za-z]+)}
# $1


Subsections

\\subsection{([A-Za-z]+)}
## $1

Labels

\\label{[a-z:]+}
<empty>

\\emph{([A-za-z .,?']+)}
*$1*