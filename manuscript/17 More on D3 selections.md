# More on D3 selections

This chapter covers additional methods you can call on a D3 selection. It also covers the **update function** which is a handy technique that keeps your HTML/SVG elements synchronised with your data.

## More selection methods

In this section we’ll look at some additional methods you can call on a D3 selection. The main ones to take note of are `.append` and `.each` as you’ll use them later on.

### .size, .empty, .node & .nodes

In the following code samples assume the SVG looks like:

```html
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

and that you select the circles and assign the selection to variable `s`:

```js
let s = d3.selectAll('circle');
```

You can query the number of elements in a selection using `.size`:

```js
s.size();  // 3
```

You can check whether a selection is empty using `.empty`:

```js
s.empty();  // false
```

You can get the first element in a selection using `.node`:

```js
s.node();  // <circle r="10"></circle>
```

Finally you can get an array containing all the elements using `.nodes`:

```
s.nodes();  // [<circle r="10" />, <circle r="20" />, <circle r="30" />]
```

You can see these four methods in action at [https://codepen.io/createwithdata/pen/NWxaZyM](https://codepen.io/createwithdata/pen/NWxaZyM). (Open your browser’s Developer Tools console to see the output.)

{width: 66%}
![Example output of .size, .empty, .node and .nodes selection methods](fa560c54ab380c618d05ee9c2c6a70a7.png)

### .append & .remove

You can add a new element to each element of a selection using `.append`. The syntax of `.append` is:

```js
s.append(elementType)
```

where `s` is a selection and `elementType` is the type of element you wish to append. For example suppose the HTML and SVG looks like:

```html
<g></g>
<g></g>
```

You can append an SVG circle to each `g` element using:

```js
let s = d3.selectAll('g');
s.append('circle');
```

This will result in a circle inside both `g` elements:

```html
<g>
  <circle></circle>
</g>
<g>
  <circle></circle>
</g>
```

You can also remove elements using `.remove`. For example:

```js
var s = d3.selectAll('g');
s.remove();
```

which removes both `g` elements.

### .each

`.each` lets you call a function on each element in a selection. It’s analogous to JavaScript’s `.forEach` method. The syntax of `.each` is:

```js
s.each(fn)
```

where `s` is a D3 selection and `fn` is a function. Within the function, the current HTML or SVG element is assigned to the `this` keyword. Suppose your HTML and SVG looks like:

```html
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

You can iterate over each circle using:

```js
var s = d3.selectAll('circle');
s.each(function() {
  console.log(this);
});
```

In the above example `this` is being output to the console. As previously explained, the HTML or SVG element of the current iteration step is assigned to `this` so the following (or similar) will be output in the console:

```html
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

A common pattern within the callback function is to use D3 to select the element assigned to `this`:

```js
d3.select(this);
```

The above allows you to modify the element. For example:

```js
var s = d3.selectAll('circle');
s.each(function() {
  d3.select(this)
    .style('fill', 'red');
});
```

changes the `fill` of each circle to `red`. This is equivalent to:

```js
var s = d3.selectAll('circle')
  .style('fill', 'red');
```

### .on

Another selection method of interest is `.on` which lets you attach event handlers to a selection’s elements. We’ll cover this in more detail in the D3 Event Handling chapter.

## Update functions

You’ve learned how to join an array of values to HTML/SVG elements using code similar to:

```js
var myData = [10, 40, 30];

d3.select('g.circles')
  .selectAll('circle')
  .data(myData)
  .join('circle')
  .attr(...)
  .style(...)
  etc.
```

If the array changes length or its values change you might expect the joined HTML/SVG elements to update accordingly. However **D3 doesn’t automatically update the HTML/SVG elements** each time the array changes. Instead you have to re-join the array and update the style and attributes each time the array changes. Therefore a common pattern is to put the join and update code in its own function (known as an **update function**):

```js
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

Each time the update function executes, HTML/SVG elements will be added, removed and updated to match the array. Generally this function should be called each time the **data changes** or some **user interaction** has occurred.

Let’s look at an example. Suppose the SVG is:

```html
<svg>
  <g class="circles" transform="translate(50, 0)">
  </g>
</svg>
```

Now let’s add an array `myData` and a function that updates `myData` with random data:

```js
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

D> `updateData` updates `myData` with an array with a random number of elements. Each element is a random number between `0` and `maxValue`.

Now we’ll define an update function that joins `myData` to `circle` elements. Each circle has a radius that’s 2 times it’s joined value and will be equally spaced in a horizontal direction:

```js
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

```js
window.setInterval(function() {
  updateData();
  update();
}, 1000);
```

When this example runs, `myData` changes every second and the circles are added, removed and updated accordingly.

D>`window.setInterval` sets up a timer that calls the supplied function at regular intervals. The first argument is the function and the second argument the interval in milliseconds.

Each time the timer fires (once every second) `updateData` and `update` are called. `updateData` clears `myData` and then adds a random number of random values to `myData`. `update` performs the data join which adds or removes circles so that there are `n` circles where `n` is the length of `myData`. The `cx`, `cy` and `r` attributes of each circle are also updated.

Navigate to [https://codepen.io/createwithdata/pen/Yzwaarg](https://codepen.io/createwithdata/pen/Yzwaarg) to view the example in CodePen. You'll see that every time the timer fires, circles appear or disappear and change size.

## Summing up

This chapter has introduced some additional methods on D3 selections and has shown how you can create an **update function** which updates HTML/SVG elements in a data-driven fashion. Typically the update function is called whenever the **data changes** or when some **user interaction** has occurred.

This pattern is very common when building data visualisations with D3 and will be used in Energy Explorer.