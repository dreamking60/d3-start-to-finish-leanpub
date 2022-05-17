# More on D3 selections

## More selection methods

In this section we’ll look at some **more methods** you can call on a D3 selection.

Assuming you’ve some `circle` elements on your page you can select them all using `d3.selectAll`:

```
var s = d3.selectAll('circle');
```

`d3.selectAll` returns a **selection object** and is assigned to the variable `s` in this example.

The selection object has a number of methods on it (such as `.style` and `.attr` which we’ve [already covered](https://learn.createwithdata.com/books/d3-start-to-finish/sections/updating-a-selection/)).

This section covers a few more selection methods. The main ones to take note of are `.append` and `.each` as you’ll use them later on when creating nested joins.

### .size, .empty, .node & .nodes

In the following code samples, assume the SVG looks like:

```
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

Given a selection you can query the number of elements in the selection using `.size`.

```
var s = d3.selectAll('circle');
s.size();  // returns 3
```

You can check whether a selection is empty using `.empty`:

```
var s = d3.selectAll('circle');
s.empty();  // returns false
```

You can get the first element in the selection using `.node`:

```
var s = d3.selectAll('circle');
s.node();  // returns <circle r="10"></circle>
```

Finally you can get an array containing all the elements using `.nodes`:

```
var s = d3.selectAll('circle');
s.nodes();  // returns [<circle r="10" />, <circle r="20" />, <circle r="30" />]
```

You can see these four methods in action in [this CodePen pen](https://codepen.io/createwithdata/pen/NWxaZyM). (Open your browser’s Developer Tools console to see the output.)

![](https://learn.createwithdata.com/wp-content/uploads/2020/10/image-5.png)

The output of the above selection methods

### .append & .remove

You can add a new element to each element of a selection using `.append`. The syntax of `.append` is:

```
s.append(elementType)
```

where `s` is a selection and `elementType` is the type of element you wish to append.

For example suppose the HTML and SVG looks like:

```
<g></g>
<g></g>
```

You can append an SVG circle to each `g` element using:

```
var s = d3.selectAll('g');
s.append('circle');
```

This will result in a circle inside both `g` elements:

```
<g>
<circle></circle>
</g>
<g>
<circle></circle>
</g>
```

You can also remove elements using `.remove`.

For example:

```
var s = d3.selectAll('g');
s.remove();
```

removes both `g` elements.

### .each

`.each` lets you call a function on each element in a selection. It’s analogous to JavaScript’s `.forEach` method.

The syntax of `.each` is:

```
s.each(fn)
```

where `s` is a D3 selection and `fn` is a function. Within the function, the current HTML or SVG element is assigned to the `this` keyword.

Suppose your HTML and SVG looks like:

```
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

You can iterate over each circle using:

```
var s = d3.selectAll('circle');
s.each(function() {
console.log(this);
});
```

In the above example `this` is being output to the console. As previously explained, the HTML or SVG element of the current iteration step is assigned to `this` so the following (or similar) will be output in the console:

```
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

A common pattern within the callback function is to use D3 to select the element assigned to `this`:

```
d3.select(this);
```

The above allows you to modify the element. For example:

```
var s = d3.selectAll('circle');
s.each(function() {
d3.select(this)
.style('fill', 'red');
});
```

will change the `fill` of each circle to `red`.

The above is equivalent to:

```
var s = d3.selectAll('circle')
.style('fill', 'red');
```

### .on

Another selection method of interest is `.on` which lets you attach event handlers to a selection’s elements. We’ll cover this in more detail in the [D3 Event Handling](https://learn.createwithdata.com/books/d3-start-to-finish/sections/d3-event-handling/) section.

## Update functions

You’ve learned how to join an array of values to HTML/SVG elements using code similar to:

```
var myData = [10, 40, 30];
d3.select('g.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr(...)
.style(...)
etc.
```

If the array changes length or its values change you might expect the joined HTML/SVG elements to update accordingly.

However **D3 doesn’t automatically update the HTML/SVG elements** each time the array changes. Instead you have to re-join the array and update the style and attributes each time the array changes.

Therefore a common pattern is to put the join and update code in its own function (known as an _update function_):

```
function update() {
d3.select('g.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr(...)
.style(...)
etc.
}
```

Each time the update function executes, HTML/SVG elements will be added, removed and updated to match the array.

Generally this function should be called each time the **data changes** or some **user interaction** has occurred.

Let’s look at an example.

Suppose the SVG is:

```
<svg>
<g class="circles" transform="translate(50, 0)">
</g>
</svg>
```

Now let’s add an array `myData` and a function that updates `myData` with random data:

```
var myData = [];
function updateData() {
var maxItems = 5, maxValue = 25;
myData = [];
var numItems = Math.ceil(Math.random() * maxItems);
for(var i = 0; i < numItems; i++) {
myData.push(Math.random() * maxValue);
}
}
```

> `updateData` updates `myData` with an array with a random number of elements. Each element is a random number between `0` and `maxValue`.

Now we’ll define an update function that joins `myData` to `circle` elements. Each circle has a radius that’s 2 times it’s joined value and will be equally spaced in a horizontal direction:

```
function update() {
d3.select('g.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('cy', 100)
.attr('r', function(d) {
return 2 * d;
})
.attr('cx', function(d, i) {
var circleSpacing = 100;
return i * circleSpacing;
})
.style('fill', '#aaa');
}
```

Now let’s set up a timer so that `updateData` and `update` are called every second (1000 milliseconds):

```
window.setInterval(function() {
updateData();
update();
}, 1000);
```

When this example runs, `myData` changes every second and the circles are added, removed and updated accordingly.

Here’s the example in CodePen:

Navigate to [https://codepen.io/createwithdata/pen/Yzwaarg](https://codepen.io/createwithdata/pen/Yzwaarg) to view the example in CodePen

Each time the timer fires (once every second) `updateData` and `update` are called.

`updateData` clears `myData` and then adds a random number of random values to `myData`. Let’s suppose `myData` is set to `[ 14.32, 15.11, 5.67, 20.34 ]`.

`update` performs the data join and updates the `cx`, `cy` and `r` attributes on each circle.

For our example time step `myData` has 4 values (14.32, 15.11, 5.67 and 20.34) and circles will be added or removed such that there are 4 circles. The `r` attribute of each circle will also be updated to the new values of `2 * 14.32`, `2 * 15.11`, `2 * 5.67` and `2 * 20.34`.

### Summing up

This section has shown how you can create an **update function** which updates HTML/SVG elements in a data-driven fashion.

Typically the update function is called whenever the **data changes** or when some **user interaction** has occurred.

This pattern is very common when building data visualizations with D3 and will be used in the Energy Explorer.