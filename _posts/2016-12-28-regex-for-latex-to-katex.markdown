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

{% highlight ruby %}
\\section{([A-Za-z]+)}
# $1
{% endhighlight %}

Subsections

{% highlight ruby %}
\\subsection{([A-Za-z]+)}
## $1
{% endhighlight %}

Labels

{% highlight ruby %}
\\label{[a-z:]+}
{% endhighlight %}


Italics

{% highlight ruby %}
\\emph{([A-za-z .,?']+)}
*$1*
{% endhighlight %}

Bold

{% highlight ruby %}
\\textbf{([A-za-z\\_]+)}
__$1__
{% endhighlight %}

Equations
\$([A-Za-z_\d{}]+)\$
{% latex %} $1 {% endlatex %}
