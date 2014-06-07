---
layout: post
title: "ProjectEuler Problem 15"
quote: "Solving the ProjectEuler Problem 15 using Permutation/Combination"
image: /media/2014-06-07-Euler-Problem15/cover.jpg
video: false
comments: true
---

<div class="message">This is an alternate method only for those who have tried it their own way.
Do not use this as your solution.</div>

###Question:

Starting in the top left corner of a 2x2 grid, and only being able to move to the right and down, there are exactly 6 routes to the bottom right corner.

{% include image.html url="/media/2014-06-07-Euler-Problem15/grid.jpg" width="50%" description= "How many such routes are there through a 20x20 grid?" %}

###Solution:

One of the methods of solving this is using regular BackTracking Method.

The other way is to use Combinations ^_^

Consider the given 2x2 matrix, from the figure there are 6 possible ways to arrive at the bottom-right corner from the top-left corner, making moves only to the right and down :

<div class="message"><strong>RRDD RDRD RDDR DRRD DRDR DDRR</strong></div>

All of these possible ways are a combination of 2N moves with N moves
right and N moves down.

Therefore all possible combinations can be given by a <sup>2N</sup>C<sub>N</sub>.

As 2N possible ways of moving with N right and N down.

Therefore in the following problem it is **<sup>40</sup>C<sub>20</sub> = 137846528820**.




