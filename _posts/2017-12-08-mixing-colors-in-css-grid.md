---
layout: post
title:  "Mixing colors in CSS Grid"
date:   2017-12-08 10:00:00 -0600
categories: jekyll update
---

Last weekend I realized that in all my focus on current projects, I've done zero CSS in the last few months. A quick search on CSS brought up numerous recently updated articles on CSS Grid, a topic I was unfamiliar with. After some initial reading, I learned that CSS Grid is the "print layout" of CSS. As someone with a background in academic publishing and all its associated layout, typesetting, etc., I wanted to learn more.

What follows is a basic demo for getting a feel of CSS Grid if you are unfamiliar. Let's mix some colors!

## First, our HTML

{% highlight html %}
<html>
  <head>
      <link rel="stylesheet" type="text/css" href="colors.css">
  </head>

  <body>
    <div class="colors">
      <div class="red"></div>
      <div class="blue"></div>
      <div class="yellow"></div>
    </div>
  </body>
</html>
{% endhighlight %}

This is all the HTML we'll need.

## What is CSS Grid?

CSS Grid is simply a 2-dimensional system for layout using columns and rows.

## Setting up a grid container

The most basic step in using CSS Grid is setting up a grid container.

First off, a grid is just a series of "lines" that ultimately create the columns and rows for layout of items.

A grid container is the parent element that holds all the items in the grid. A grid container is created by including the `display: grid` declaration in the container. Our grid container is called `colors`:

{% highlight css %}
.colors {
  display: grid;
}
{% endhighlight %}

## Get our rows and columns going

From here we only need to add a few more lines to set up some rows and columns. We'll want 6 columns and 6 rows. The basic format is this:

{% highlight css %}
.colors {
  display: grid;
  grid-template-rows: ;
  grid-template-columns: ;
}
{% endhighlight %}

From here we have a few options.

We can define our columns/rows individually using whatever unit of measurement. CSS Grid introduces a new unit of measurement: `fr`.

`fr` is a fractional unit of measurement which divides the child items into fractions of the container size.  For instance, if you had two rows `1fr 2fr` and a container sized 150px, you would have rows measuring `50px 100px`.

For our purposes, we won't use `fr` though. We'll stick with `px`, creating columns and rows of 100px. So, we could write this:

{% highlight css %}
.colors {
  display: grid;
  grid-template-rows: 100px 100px 100px 100px 100px 100px;
  grid-template-columns: 100px 100px 100px 100px 100px 100px;
}
{% endhighlight %}

Or, we could take advantage of another notation to achieve the same effect:

{% highlight css %}
.colors {
  display: grid;
  grid-template-rows: repeat(6, 100px);
  grid-template-columns: repeat(6, 100px);
}
{% endhighlight %}

Blam, we have columns and rows.

Now, imagine we have the following html and css with items added:

{% highlight html %}
<html>
  <head>
      <link rel="stylesheet" type="text/css" href="colors.css">
  </head>

  <body>
    <div class="colors">
      <div class="red"></div>
      <div class="blue"></div>
      <div class="yellow"></div>
    </div>
  </body>
</html>
{% endhighlight %}

{% highlight css %}
.red {
  background: rgba(255, 0, 0, 0.5);
}

.blue {
  background: rgb(30,144,255);
}

.yellow {
  background: rgba(255, 255, 0, 0.5);
}

.colors {
  display: grid;
  grid-template-rows: repeat(6, 100px);
  grid-template-columns: repeat(6, 100px);
}
{% endhighlight %}

We end up with this:

<img src="/images/CSS-Grid_initial-arrangement.png" style="width: 50%; margin: auto; display: block;">

All our items are currently lined up in consecutive columns in a row. Plus, we'd love to mix these colors to create some new ones.

## Setting item location

This is easily remedied by assigning specific locations to our items.

An important thing to note before we do this: when you work in CSS Grid, you'll often think in terms of lines. It's a simple point, but I still found myself thinking in terms of cells, the space between two adjacent row lines and two adjacent column lines.

Our goal is to adjust our red item so it sits between column lines 1 and 4 and row lines 1 and 4, our blue item between column lines 2 and 5 and row lines 2 and 5, and our yellow item between column lines 3 and 6 and row lines 3 and 6.

Starting with our red item, we'll use `grid-row` and `grid-column` declarations. Each with have two numbers in this format: `starting point / ending point`. So, our red item could be updated to this:

{% highlight css %}
.red {
  background: rgba(255, 0, 0, 0.5);
  grid-row: 1 / 4;
  grid-column: 1 / 4;
}
{% endhighlight %}

Our red item now spreads across a larger area.

<img src="/images/CSS-Grid_big-red.png" style="width: 50%; margin: auto; display: block;">

If we wanted, we could use a slightly different notation to achieve the same effect:

{% highlight css %}
.red {
  background: rgba(255, 0, 0, 0.5);
  grid-row: 1 / span 3;
  grid-column: 1 / span 3;
}
{% endhighlight %}

So, we still start at line 1 but then span across three more lines.

The total CSS update for all our items looks like this:

{% highlight css %}
.red {
  background: rgba(255, 0, 0, 0.5);
  grid-row: 1 / span 3;
  grid-column: 1 / span 3;
}

.blue {
  background: rgb(30,144,255);
  grid-row: 2 / span 3;
  grid-column: 2 / span 3;
}

.yellow {
  background: rgba(255, 255, 0, 0.5);
  grid-row: 3 / span 3;
  grid-column: 3 / span 3;
}
{% endhighlight %}

Cool, with this new layout we now have green:

<img src="/images/CSS-Grid_green.png" style="width: 50%; margin: auto; display: block;">

Using our old friend z-index, we can create even more colors by adding a declaration on our blue item:

{% highlight css %}
.blue {
  background: rgb(30,144,255);
  grid-row: 2 / span 3;
  grid-column: 2 / span 3;
  z-index: -1;
}
{% endhighlight %}

Now we have more colors, including a debatably horrendous shade of orange/brown!

<img src="/images/CSS-Grid_orange-brown.png" style="width: 50%; margin: auto; display: block;">

## Wrap up

This is just a short example of the possibilities of CSS Grid. If you are like me and enjoy learning through play, take a trip over to [CSS Grid Garden](http://cssgridgarden.com/) to learn more.
