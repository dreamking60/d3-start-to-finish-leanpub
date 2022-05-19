# More on D3 joins

## Joining an array to multiple elements

The Energy Explorer currently displays a **single circle** for each country.

However we would like each country to be represented by **4 circles** (one for each of the energy types) and a **text label**.

For example, Canada which uses all 4 energy types should look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-18.png)

A sensible approach is to:

* create a single SVG `g` element for each country
* add four `circle` elements and a single `text` element to the `g` element

so that each country looks something like:

```
<g class="country">
<circle class="renewable"></circle>
<circle class="oilgascoal"></circle>
<circle class="hydroelectric"></circle>
<circle class="nuclear"></circle>
<text class="label"></text>
</g>
```

> Remember that a `g` element represents a group of SVG elements.

There’s more than one way of achieving this using D3 and we’ll look at a simple approach that doesn’t require any additional D3 knowledge.

Let’s start with an easier example.

Suppose you have some data:

```
var myData = [10, 40, 30];
```

and you’d like to join each array element to a `g` element that contains a `circle` element and a `text` element.

And suppose you’d also like to size the circle using the data value and populate the text element with the data value (so that it acts as a label):

![](http://learn.createwithdata.com/wp-content/uploads/2020/07/image-20.png)

The resulting HTML/SVG should look something like:

```
<g class="chart">
<g transform="translate(0, 0)">
<circle r="10"></circle>
<text y="60">10</text>
</g>
<g transform="translate(100, 0)">
<circle r="40"></circle>
<text y="60">40</text>
</g>
<g transform="translate(200, 0)">
<circle r="30"></circle>
<text y="60">30</text>
</g>
</g>
```

Let’s start by joining `myData` to `g` elements:

```
var myData = [10, 40, 30];
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g');
```

The above is similar to the join code seen in previous sections but the array is joined to `g` elements instead of `circle` elements. This will create the following elements:

```
<g></g>
<g></g>
<g></g>
```

We now need to add a `circle` and `text` element to each of the `g` elements.

We can do this by adding a new function `updateGroup` which gets called on each `g` element using D3’s `.each` function:

```
var myData = [10, 40, 30];
function updateGroup() {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
g.append('text')
.attr('y', 60);
}
}
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g')
.each(updateGroup);
```

> Read more about `.each` in the [More selection methods](https://learn.createwithdata.com/books/d3-start-to-finish/sections/more-selection-functions/) section.

`updateGroup` gets called for each `g` element and starts by making a selection containing `this` (which represents the `g` element) and assigns it to the variable `g`. (This is explained in more detail in the [More selection methods](https://learn.createwithdata.com/books/d3-start-to-finish/sections/more-selection-functions/) section.)

`updateGroup` then checks whether `g` has any members and if not, it appends a `circle` and `text` element. It also sets the `y` atttribute of the `text` element.

> `g.selectAll('*')` makes a selection containing all the members of `g`.
> 
> `g.selectAll('*').empty()` returns `true` if `g` has no members.

This code creates the following SVG:

```
<g>
<circle></circle>
<text y="60"></text>
</g>
<g>
<circle></circle>
<text y="60"></text>
</g>
<g>
<circle></circle>
<text y="60"></text>
</g>
```

Now we’ll modify `updateGroup` to update:

* the `transform` attribute on the `g` element (so that each `g` element is translated proportionally to the index `i`)
* the radius of the `circle` to the joined value `d`
* the content of the `text` element to the joined value `d`:

```
var myData = [10, 40, 30];
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) {
g.append('circle');
g.append('text')
.attr('y', 60);
}
var x = i * 100;
g.attr('transform', 'translate(' + x + ', 0)');
g.select('circle')
.attr('r', d);
g.select('text')
.text(d);
}
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g')
.each(updateGroup);
```

> Remember that D3 passes in the joined data `d` and index `i` to `updateGroup`. The first time `updateGroup` is called, `d` will be `10` and `i` will be `0`.

This results in the following SVG:

```
<g class="chart">
<g transform="translate(0, 0)">
<circle r="10"></circle>
<text y="60">10</text>
</g>
<g transform="translate(100, 0)">
<circle r="40"></circle>
<text y="60">40</text>
</g>
<g transform="translate(200, 0)">
<circle r="30"></circle>
<text y="60">30</text>
</g>
</g>
```

and the following output:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-20.png)

Notice that we **transform** the `g` element rather than setting the x coordinate of the `circle` and `text` elements individually.

This is a common technique when working with a group of elements. It’s usually easier (and more expressive) to **transform the group as a whole** and position the constituent elements relative to the group.

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/RwWmweL](https://codepen.io/createwithdata/pen/RwWmweL) to view the example in CodePen

(There’s a bit of additional CSS and the `g.chart` element has been transformed in the order to prevent clipping.)

The same example but using an update function (so that the data join is called each time the data changes) is [here](https://codepen.io/createwithdata/pen/abdXJYv).

## Nested joins

This section shows how to join a nested array (in other words, an array of arrays) to HTML or SVG elements.

> Although the Energy Explorer doesn’t use this technique, it’s a useful technique that isn’t widely known. Feel free to skip ahead if it’s not of interest!

Suppose you have the array:

```
[
[10, 30, 20],
[40, 10, 30, 20]
]
```

The aim is to join this to nested HTML/SVG elements. For example, you might want to join the outer array to `g` elements and the inner arrays to `circle` elements:

```
<g transform="translate(0,0)">
<circle cx="0" r="10"></circle>
<circle cx="100" r="30"></circle>
<circle cx="200" r="20"></circle>
</g>
<g transform="translate(0,100)">
<circle cx="0" r="40"></circle>
<circle cx="100" r="10"></circle>
<circle cx="200" r="30"></circle>
<circle cx="300" r="20"></circle>
</g>
```

This looks like:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-17.png)

Each `g` element is translated down the page by 100 pixels. Each `circle` element is translated horizontally by 100 pixels and sized according to the inner array values.

### Overview

The approach is to join the outer array to `g` elements. This means that each `g`‘s joined value is one of the inner arrays.

(This may feel a bit unusual because you’re used to joining arrays of numbers to HTML/SVG elements. However it doesn’t really matter what type the array elements are. They can be numbers, strings, objects or arrays.)

We then iterate through the two `g` elements and join the inner arrays to `circle` elements.

### Join outer array to `g` elements

Assume there’s an `svg` and `g` element on the page that’ll act as the container:

```
<svg width="1200" height="1200">
<g class="chart" transform="translate(50, 50)"></g>
</svg>
```

Let’s join an array of arrays named `myData` to `g` elements:

```
var myData = [
[10, 30, 20],
[40, 10, 30, 20]
];
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g');
```

The above is similar to the join code seen in previous sections but the array is joined to `g` elements instead of `circle` elements. This will create the following elements:

```
<g></g>
<g></g>
```

(The first `g` element’s joined value is the array `[10, 30, 20]`. The second `g` element’s joined value is the array `[40, 10, 30, 20]`.)

We’ll name this join the **outer** join.

### Add `updateGroup` function

Now add a new function `updateGroup` and call it on each `g` element using D3’s `each` method:

```
var myData = [
[10, 30, 20],
[40, 10, 30, 20]
];
function updateGroup(d, i) {
}
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g')
.each(updateGroup);
```

`updateGroup` will get called for each element in `myData`. Each time it’s called, the joined value is passed in as the first parameter and the index as the second parameter.

Therefore the first time `updateGroup` is called, `d` is the array `[10, 30, 20]` and `i` is `0`. The second time it’s called, `d` is the array `[40, 10, 30, 20]` and `i` is `1`.

`updateGroup` has a couple of tasks:

* position the `g` elements
* join `d` to `circle` elements

### Position the `g` elements

Each `g` element is translated by `(0, i * 100)` where `i` is the index of the `g` element:

```
var myData = [
[10, 30, 20],
[40, 10, 30, 20]
];
function updateGroup(d, i) {
var g = d3.select(this);
g.attr('transform', 'translate(0,' + i * 100 + ')');
}
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g')
.each(updateGroup);
```

`updateGroup` starts by selecting the current `g` element (represented by `this`) and assigning it to the variable `g`. (See the `.each` section in the [More selection methods](https://learn.createwithdata.com/books/d3-start-to-finish/sections/more-selection-functions/) section for a reminder.)

We then transform the `g` element using D3’s `.attr` method. (The variable `g` is a D3 selection containing the current `g` element.)

The resulting SVG will look like:

```
<g class="chart" transform="translate(50, 50)">
<g transform="translate(0,0)"></g>
<g transform="translate(0,100)"></g>
</g>
```

### Join `d` to `circle` elements

We’ll now join `d` to `circle` elements using standard join code:

```
var myData = [
[10, 30, 20],
[40, 10, 30, 20]
];
function updateGroup(d, i) {
var g = d3.select(this);
g.attr('transform', 'translate(0,' + i * 100 + ')');
g.selectAll('circle')
.data(d)
.join('circle');
}
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g')
.each(updateGroup);
```

This may take a bit of getting used to! The key thing to remember is that each time `updateGroup` is called, `d` is one of the inner arrays. The first time `updateGroup` is called, `d` will be `[10, 30, 20]`. The second time, `d` will be `[40, 10, 30, 20]`.

Remember that the variable `g` is a D3 selection containing the current `g` element. (The first time `updateGroup` is called, this selection contains the first `g` element.)

The join code is:

```
g.selectAll('circle')
.data(d)
.join('circle');
```

We’ll name this join the **inner** join.

It’s helpful to know it’s equivalent to:

```
d3.select(this)
.selectAll('circle')
.data(d)
.join('circle');
```

which is similar to the join code you encountered in the [Data joins](https://learn.createwithdata.com/books/d3-start-to-finish/sections/how-to-join/) section:

* the first line selects the container element (a `g` element in this case)
* the second line selects the elements we’re joining (`circle` elements in this case)
* the third line specifies the array we’re joining
* the last line specifies the type of element we’re joining (`circle` elements in this case)

A key thing to remember is that `d` is an array, because the `g` elements have been joined to arrays: see the join code beginning `d3.select('g.chart')`.

The resulting SVG will look like:

```
<g class="chart" transform="translate(50, 50)">
<g transform="translate(0,0)">
<circle></circle>
<circle></circle>
<circle></circle>
</g>
<g transform="translate(0,100)">
<circle></circle>
<circle></circle>
<circle></circle>
<circle></circle>
</g>
</g>
```

### Update the circles

The final step is to update the position and radius of each circle.

We only need to set the `x` position of the circle because the translate transform on the `g` element takes care of vertical positioning:

```
var myData = [
[10, 30, 20],
[40, 10, 30, 20]
];
function updateGroup(d, i) {
var g = d3.select(this);
g.attr('transform', 'translate(0,' + i * 100 + ')');

g.selectAll('circle')
.data(d)
.join('circle')
.attr('cx', function(d, i) {
return i * 100;
})
.attr('r', function(d) {
return d;
});
}
d3.select('g.chart')
.selectAll('g')
.data(myData)
.join('g')
.each(updateGroup);
```

Note that the `i` parameter in the function that sets `cx` refers to the inner join. In other words, it’s the index of an element of the inner array.

This produces the output:

```
<g class="chart" transform="translate(50, 50)">
<g transform="translate(0,0)">
<circle cx="0" r="10"></circle>
<circle cx="100" r="30"></circle>
<circle cx="200" r="20"></circle>
</g>
<g transform="translate(0,100)">
<circle cx="0" r="40"></circle>
<circle cx="100" r="10"></circle>
<circle cx="200" r="30"></circle>
<circle cx="300" r="20"></circle>
</g>
</g>
```

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-17.png)

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/dyNgzqJ](https://codepen.io/createwithdata/pen/dyNgzqJ) to view the example in CodePen

### Update function with nested join

The nested join introduced in this section can be adapted to an update function. (If you’ve forgotten what an update function I suggest revisiting the [Update functions section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/update-loops/).)

A function named `getData` is added which creates a randomized array of arrays. (See the upcoming CodePen example for this function’s body.)

Wrap the outer join in a function named `update` and use `window.setInterval` to call `getData` and `update` at regular intervals:

```
function getData() {
// Return a randomized array of arrays
}
function updateGroup(d, i) {
var g = d3.select(this);
g.attr('transform', 'translate(0,' + i * 100 + ')');

g.selectAll('circle')
.data(d)
.join('circle')
.attr('cx', function(d, i) {
return i * 100;
})
.attr('r', function(d) {
return d;
});
}
function update() {
d3.select('g.chart')
.selectAll('g')
.data(data)
.join('g')
.each(updateGroup);
}
window.setInterval(function() {
data = getData();
update();
}, 1000);
```

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/gOLmMrg](https://codepen.io/createwithdata/pen/gOLmMrg) to view the example in CodePen