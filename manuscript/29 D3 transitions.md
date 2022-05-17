# D3 transitions

A powerful effect that adds visual flair to a data visualization is **animation**.

Not only can animation make a visualization **more engaging** it also serves a practical purpose.

Suppose you’ve a visualization consisting of a number of circles and the positions of the circles change (for example, a new indicator has been selected).

If the circles **animate into their new positions** it’s possible to **track individual circles** and to see where they move to. Without animation it’s not obvious where each circle has moved to.

Here’s a CodePen example that illustrates this:

Navigate to [https://codepen.io/createwithdata/pen/pogPwOy](https://codepen.io/createwithdata/pen/pogPwOy) to view the example in CodePen

If animations are switched off (_Use transitions_ is unchecked) the circles jump immediately to their new positions. It’s not possible to track the movement of each circle. Furthermore, it’s not discernable whether you’re seeing circle movement, or an entirely new set of circles.

With animations switched on, it’s clear that the circles are changing position. This is known as **object constancy**.

D3 has a powerful system for animating (or ‘transitioning’) HTML/SVG elements. You’ll be glad to know you’ve already done a lot of the hard work and adding animations to a selection of elements is relatively straightforward.

### D3 transitions

A D3 transition is a **special type of selection** that animates (or ‘transitions’) style and attribute updates.

For example if you use `.style` to update the `opacity` of a selection:

```
selection.style('opacity', 0);
```

a D3 transition will animate `opacity` from its current value to its new value.

To create a transition add a single call to `.transition()` before the `.style` and `.attr` calls.

For example:

```
selection
.transition()
.style('opacity', 0);
```

This results in the opacity animating from its current value to zero. (If its current value is already zero there’ll be no animation.)

Let’s look at a more substantial example.

Suppose you have the following code which repeatedly draws a set of circles at random positions:

```
var myData = [];
var width = 800, height = 600, numCircles = 20;
function updateData() {
for(var i=0; i<numCircles; i++) {
myData[i] = {
x: Math.floor(Math.random() * width),
y: Math.floor(Math.random() * height),
r: Math.random() * 30
};
}
}
function update() {
d3.select('#chart')
.selectAll('circle')
.data(myData)
.join('circle')
.transition()
.attr('cx', function(d) {
return d.x;
})
.attr('cy', function(d) {
return d.y;
})
.attr('r', function(d) {
return d.r;
});
}
window.setInterval(function() {
updateData();
update();
}, 1000);
```

`window.setInterval` creates a timer which calls the provided function every 1000 milliseconds. This results in `updateData` and `update` being called every second.

`updateData` updates each element of `myData` with an object containing `x`, `y` and `r` properties.

`update` joins `myData` to `circle` elements and updates the circle center (`cx` and `cy`) and radius (`r`) using the joined value.

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/jOqbBzp](https://codepen.io/createwithdata/pen/jOqbBzp) to view the example in CodePen

Currently the circles jump to their new positions and radii.

To introduce a transition, add a call to `.transition()` just before the first `.attr` call:

```
...
function update() {
d3.select('#chart')
.selectAll('circle')
.data(myData)
.join('circle')
.transition()
.attr('cx', function(d) {
return d.x;
})
.attr('cy', function(d) {
return d.y;
})
.attr('r', function(d) {
return d.r;
});
}
...
```

This causes the `cx`, `cy` and `r` attribute updates to animate to their new values.

Here’s the CodePen example with the transition added:

Navigate to [https://codepen.io/createwithdata/pen/BaKoWQZ](https://codepen.io/createwithdata/pen/BaKoWQZ) to view the example in CodePen

Only style and attribute updates after the `.transition()` will animate. Thus the following only animates the circle radius:

```
d3.select('#chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('cx', function(d) {
return d.x;
})
.attr('cy', function(d) {
return d.y;
})
.transition()
.attr('r', function(d) {
return d.r;
});
```

### Transition duration

The duration of the transition can be modified by calling `.duration` and passing in the transition duration (in milliseconds).

For example:

```
selection
.transition()
.duration(1000)
.attr('r', function(d) {
return d.r;
});
```

The above sets the transition duration to 1 second. (The default duration is 250 milliseconds.)

As an exercise try adding `.duration(1000)` to the [CodePen example](https://codepen.io/createwithdata/pen/BaKoWQZ).

### Transition delay

You can add a delay to a transition by calling `.delay` and passing in the delay (in milliseconds).

For example:

```
selection
.transition()
.delay(250)
.attr('r', function(d) {
return d.r;
});
```

The above delays the transition for 250 milliseconds.

Adding a delay becomes more interesting when you pass in a function:

```
selection
.transition()
.delay(function(d, i) {
return i * 20;
})
.attr('r', function(d) {
return d.r;
});
```

The above delays each element in the selection by `i * 20` where `i` is the element’s index. This results in a staggered transition (the first element in the selection moves immediately, the second element after 20 milliseconds etc.). You’ll use this technique in the next practical.

As an example, try adding:

```
.delay(function(d, i) {
return i * 20;
})
```

to the previous CodePen example.

### Key functions

By default when you join an array to HTML/SVG elements, D3 joins the **1st array element to the 1st HTML/SVG** **element** in the selection, the **2nd array element to the 2nd HTML/SVG** **element** and so on.

This means that if you sort the array, and perform the join, the array elements **might get joined to different HTML/SVG elements**.

In a lot of cases this doesn’t cause issues but if you’re positioning the HTML/SVG elements according to the array index `i` and using transitions you’ll **lose object constancy**.

For example, examine the following code:

```
var myData;
function updateData() {
myData = [];

for(var i=0; i<7; i++) {
myData[i] = {
id: i,
value: Math.floor(Math.random() * 50)
};
}

myData = _.sortBy(myData, function(d) {
return d.value;
});
}
function update() {
d3.select('#chart')
.selectAll('circle')
.data(myData)
.join('circle')
.transition()
.attr('cx', function(d, i) {
return i * 100;
})
.attr('r', function(d) {
return d.value;
});
}
window.setInterval(function() {
updateData();
update();
}, 1000);
```

Every 1 second, `updateData` and `update` are called.

`updateData` updates `myData` with objects containing two properties `id` and `value`. `myData` is then **sorted by value**.

`update` joins `myData` to `circle` elements and updates the `cx` and `r` attributes of each circle. `cx` is set according to the array index `i`. (Because `myData` is sorted in ascending order, the leftmost circle will always be the smallest circle.)

When the above code runs you’ll see the circles changing radius, but not shuffling into their new locations:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/d3-join-no-key-1.gif)

Circles stay in same positions even though array has shuffled. (This is an animated gif. Reload this page to restart it.)

To ensure that each array element stays joined to the same `circle` element you need to specify a **key function**. This is a function that’s passed into `.data` and returns a unique id for each element:

```
function update(data) {
d3.select('#chart')
.selectAll('circle')
.data(data, function(d) {
return d.id;
})
.join('circle')
.transition()
.attr('cx', function(d, i) {
return i * 100;
})
.attr('r', function(d) {
return d.value;
});
}
```

Now the circles will shuffle into their new locations:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/d3-join-key.gif)

Key functions are simple to add but not particularly easy to understand. The situations in which they are essential are quite subtle.

I tend to add a key function if I run into the situation described above but in general you won’t go wrong if you always add a key function when joining data.

### Summing up

This section has shown how you can add animations (or ‘transitions’) to a D3 selection. It’s as simple as preceding the `.style` and `.attr` calls (which you want to animate) with a single `.transition()` call.

You can change the duration of transitions using `.duration` and can also create staggered transitions by passing a function into `.delay`.

You also saw the importance of adding a key function if your array changes order, you’re positioning by index and are using transitions.

In the next practical you’ll add transitions to the Energy Explorer so that when a new indicator is chosen the circle groups animate into their new locations.