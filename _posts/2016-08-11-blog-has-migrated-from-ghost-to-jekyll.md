---
layout: post
title: Blog has migrated from Ghost to Jekyll
---

In the past few days I spent time migrating the blog from
[Ghost](https://ghost.org) to [Jekyll](https://jekyllrb.com/).

The theme builds off
[Type Theme](https://rohanchandra.github.io/project/type/), and is
heavily inspired by [Otoro](http://blog.otoro.net/), the [New York
Times](http://www.nytimes.com/), and the
[Rosenrot](http://the-rosenrot.com/). The annals-like frontpage takes
cue from Paul Graham's [essays](http://paulgraham.com/articles.html)
and Cosma Shalizi's [notebooks](http://bactra.org/notebooks/).

![]({{ site.baseurl}}/assets/2016-08-11-figure1.png)
![]({{ site.baseurl}}/assets/2016-08-11-figure2.png)
_<center>Top: Old frontpage. Bottom: New frontpage.</center>_

Hooray for math! (using KaTeX)

$$
E(w_i,c_k,z) = -\sum_{j=1}^z w_{i,j} c_{k,j} + \Big[z\log a -\sum_{j=1}^z (w_{i,j}^2 +  c_{k,j}^2)\Big].
$$

Hooray for code snippets! (taken from [Edward](http://edwardlib.org/getting-started))

```ruby
def set_seed(x):
  """Set seed for both NumPy and TensorFlow.

  Parameters
  ----------
  x : int, float
    seed
  """
  np.random.seed(x)
  tf.set_random_seed(x)
```

Hooray for bibliographies!
...
Okay I don't quite have that one set up yet. But [it's
doable](https://github.com/inukshuk/jekyll-scholar)!

The blog's permalinks are the same. The [RSS feed]({{ site.baseurl
}}/rss/) is still available.

## Why the change?

I liked Ghost, having migrated from Wordpress and with the impression of a
sleeker and more modern content management system (CMS). But CMS' have
always been a bother for me because I could never understand the
internals. It would always be a reliance on plugins in order to
accomplish things. Even with Ghost some things were too automagical
and hidden to the user.

Time to go even more bare-bones with a static site generator.
[All code and content for this blog is available](https://github.com/dustinvtran/blog).

Here's to hoping the redesign motivates me to write more often!
