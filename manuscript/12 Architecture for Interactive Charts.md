# Architecture for Interactive Charts

## Layout functions

A layout function is a function that **takes an array of data** and **returns a new array** containing position, size and other visual variables for each item.

For example, given the data:

```
var myData = [30, 10, 20, 40];
```

a layout function returns an array containing position and radius data:

```
[
{ x: 100, y: 100, r: 30 },
{ x: 200, y: 100, r: 10 },
{ x: 300, y: 100, r: 20 },
{ x: 400, y: 100, r: 40 }
]
```

Each object in the above array corresponds to an element in `myData`.

The layout function that produces the above is:

```
function layout(data) {
var layoutData = data.map(function(d, i) {
var item = {};
item.x = 100 + i * 100;
item.y = 100;
item.r = d;
return item;
});
return layoutData;
}
```

This function iterates through `data` and computes `x`, `y` and `r` for each array element. It then returns a new array containing the position and radius data.

> See the [JavaScript iteration section](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/javascript-iteration/) in the Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation if you’re not familiar with `map`.

Typically you’d assign the output of `layout(myData)` to a new variable:

```
var layoutData = layout(myData);
```

and then join `layoutData` to HTML or SVG elements:

```
d3.select('#chart')
.selectAll('circle')
.data(layoutData)
.join('circle')
.attr('cx', function(d) { return d.x; })
.attr('cy', function(d) { return d.y; })
.attr('r', function(d) { return d.r; });
```

There are a few benefits of this approach:

* the join code is much **cleaner and easier to read** because it doesn’t contain any code for computing size, positions and other visual variables
* the **layout and rendering code is decoupled**, so swapping D3 for a different library (such as React or Vue) to update the HTML or SVG elements will have lower impact
* **debugging is easier** because you can inspect the output of the layout function
* it’s relatively easy to run **automated tests** on the layout function

Here’s a full example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/gOaezWJ](https://codepen.io/createwithdata/pen/gOaezWJ) to view the example in CodePen

`update()` is called at the end of the JavaScript file. `update` calls `layout(myData)` and assigns the output to `layoutData`. It then joins `layoutData` to `circle` elements.

The layout function `layout` calls `.map` on the array of data and returns an object containing `x`, `y` and `r` for each array element.

You don’t **have** to use layout functions but for more complex visualisations they can make your code easier to understand and maintain.

D3 also provides a number of layout functions but it adds geometric information directly on each array element. The approach in this section creates a new array and doesn’t change (or mutate) the original array. This is more in keeping with modern JavaScript practice.

You’ll have an opportunity to learn more about layout functions when you add one to arrange the circles in the Energy Explorer.