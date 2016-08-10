---
layout: post
title: Displaying full page previews on Ghost
---

I've decided to document any hacks I encounter and find helpful in my work method, and to share them here for any reader who may also find them helpful.

Ghost is my blogging platform of choice, and I sorely missed the feature in Wordpress where you can hit "Preview Post" and have it display a live rendering of the post au naturel—not a pseudo-preview like what Ghost does. Ghost's preview is certainly awesome, but sometimes you'd like to see the final product to ensure everything looks right before you publish.

A [member on the Ghost forums](https://ghost.org/forum/using-ghost/5219-page-preview-of-posts/2/) suggests the following: make the post a static page and publish it. This prevents the post from arriving on your blog frontpage or RSS, and your blog's url with the slug appended to it will contain the "preview". Then whenever you're satisfied with the preview, unset the post as a static page, unpublish the post, and republish it (to reset the publish date).
