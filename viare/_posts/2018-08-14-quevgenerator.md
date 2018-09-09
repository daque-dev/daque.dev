---
title: "Quev Height Function"
subtitle: "Describes how the quev height function works"
blog: viare
permalink: viare/quevgenerator

author: Miguel Angel
author_url: https://gitlab.com/quevangel
---

## Background

It is well known the bell like shape the function $$  f(x) = e^{-x^2} $$. Because of this natural
looking shape I thought it could be used in the generator. I wasn't sure how, but I knew I could.

## Adjustment

First, to make it tridimensional I changed the function from \\( f: R \rightarrow R \\) 
with $$ f(x) = e^{-x^2} $$ to $$ f: R \times R \rightarrow R $$ with $$ f(v) = e^{ -{ |v| }^2} $$ 
.This produced a bell like figure centered at $$ (0, 0) $$.

It was easy to modify it and make it centered at an arbitrary point $$ c $$ and also make it's
height adjustable as the original function only had height of $$ 1 $$. So, with a center at $$ c $$
and a height of $$ w $$, the function was $$ f(v) = w e^{ -{ |v - c| }^2} $$

The $$ w $$ parameter was a way to scale the function in the $$ y $$ axis, so I added a way to scale
it in the $$ x $$ axis, called $$ zoom $$. The function with $$ zoom $$ added as $$ z $$ is: $$ f(v) = w e^{ -{({ { |v - c| } \over z})}^2} $$

I also realized I could not just limit myself to an exponent of 2, or a base of $$ e $$, I called
them just the $$ exponent $$ and $$ base $$ parameters. So, then, if we call the exponent $$ x $$
and the base $$ b $$, the function is $$ f(v) = w b^{ -{ ( { { |v - c| } \over z } ) }^x} $$.

## Final form

This function represents a "height center" in the terrain. Thus, each center can be considered as a
tuple $$ (p, w, b, x, z) $$ where $$ p $$ is the position of the center, $$ w $$ is the height, $$
b $$ is the base, $$ x $$ is the exponent and $$ z $$ is the zoom.

This tuple is made explicit in the code in the form of the $$QuevCenter$$ structure. 

With this, given a set of height centers $$ C $$, we have a height function given by:

$$ h(v; C) = \sum_{c \in C} { c_w c_b ^ { -{ ( { { |v - c_p| } \over c_z } ) } ^ {c_x} } } $$