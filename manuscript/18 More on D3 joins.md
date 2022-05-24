# More on D3 Joins

This chapter covers a couple of advanced techniques when joining data.

We first look at joining arrays to groups of elements, so that each array element corresponds to a group of elements. This is a common scenario because you often need to represent data points with more than one HTML/SVG element (for example, a labelled circle requires two elements). Energy Explorer uses this technique so it's worth taking some time to understand. (This is one of the most advanced topics in this book.)

The second technique is for joining nested arrays to nested elements. This is quite an advanced technique and isn't used by Energy Explorer so feel free to skip.

## Joining an array to groups of elements

The Energy Explorer currently displays a **single circle** for each country. However we would like each country to be represented by **4 circles** (one for each of the energy types) and a **text label**. For example, Canada which uses all 4 energy types should look like:

{width: 15%}
![Each country is represented by 4 circles and a text element](1438e1a4117895d9fa2cfb0ffe646ab7.png)

A sensible approach is to:

* create a single SVG `<g>` element for each country
* add four `<circle>` elements and a single `<text>` element to the `<g>` element

so that each country looks something like:

```html
<g class="country">
  <circle class="renewable"></circle>
  <circle class="oilgascoal"></circle>
  <circle class="hydroelectric"></circle>
  <circle class="nuclear"></circle>
  <text class="label"></text>
</g>
```

D>Remember a `<g>` element represents a group of SVG elements.

There’s more than one way of achieving this using D3 and we’ll look at a simple approach that doesn’t require any additional D3 knowledge. Let’s start with an easier example. Suppose you have some data:

```js
let myData = [10, 40, 30];
```

and you’d like to join the array to `<g>` elements containing a `<circle>` and `<text>` element. And suppose you’d also like to size the circle using the data value and populate the text element with the data value (so that it acts as a label):

{width: 33%}
![Array of numbers joined to a `<g>` element containing a `<circle>` and `<text>` element](7f0c1e6af8b4838947197508746e648e.png)

We'd like the HTML/SVG to look like:

```html
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

Let’s start by joining `myData` to `<g>` elements:

```js
let myData = [10, 40, 30];

d3.select('g.chart')
  .selectAll('g')
  .data(myData)
  .join('g');
```

The above is similar to the join code seen in previous sections but the array is joined to `<g>` elements instead of `<circle>` elements. This will create the following elements:

```html
<g></g>
<g></g>
<g></g>
```

We now need to add a `<circle>` and `<text>` element to each of the `<g>` elements.

We can do this by adding a new function `updateGroup` which gets called on each `<g>` element using D3’s `.each` function:

```js
let myData = [10, 40, 30];

markua-start-insert
function updateGroup() {
  var g = d3.select(this);

  if(g.selectAll('*').empty()) {
    g.append('circle');
    g.append('text')
      .attr('y', 60);
  }
}
markua-end-insert

d3.select('g.chart')
  .selectAll('g')
  .data(myData)
  .join('g')
markua-start-insert
  .each(updateGroup);
markua-end-insert
```

D>We covered the `.each` method and `d3.select(this)` in the previous chapter.

`updateGroup` gets called for each `<g>` element. It begins by selecting `this` (which represents the `<g>` element `updateGroup` has been called on) and assigns the resulting selection to the variable `g`.

`updateGroup` then checks whether the group `<g>` is empty. If it is, it appends a `<circle>` and `<text>` element to the group. It also sets the `y` atttribute of the `<text>` element. In effect we're **initialising** each of the `<g>` elements.

D> `g.selectAll('*')` makes a selection containing all the child elements of the `<g>` element.
D> 
D> `g.selectAll('*').empty()` returns `true` if `<g>` has no child elements.

This code creates the following SVG:

```html
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

* the `transform` attribute on the `<g>` element (so that each `<g>` element is translated proportionally to the index `i`)
* the radius of the `<circle>` element to the joined value `d`
* the content of the `<text>` element to the joined value `d`:

```js
let myData = [10, 40, 30];

function updateGroup(d, i) {
  let g = d3.select(this);

  if(g.selectAll('*').empty()) {
    g.append('circle');
    g.append('text')
      .attr('y', 60);
  }
	
markua-start-insert
  let x = i * 100;
  g.attr('transform', 'translate(' + x + ', 0)');

  g.select('circle')
    .attr('r', d);

  g.select('text')
    .text(d);
markua-end-insert
}

d3.select('g.chart')
  .selectAll('g')
  .data(myData)
  .join('g')
  .each(updateGroup);
```

D>Remember that D3 passes in the joined data `d` and index `i` to `updateGroup`. The first time `updateGroup` is called, `d` will be `10` and `i` will be `0`.

This results in the following SVG:

```html
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

and looks like:

{width: 33%}
![An array of numbers joined to a `<g>` element containing `<circle>` and `<text>` elements](34d411b8783dce8bdf04d1c28c754fc7.png)

D>Notice we **transform** the `<g>` element rather than setting the x coordinate of the `<circle>` and `<text>` elements individually. This is a common technique when working with a group of elements. It’s usually easier (and more expressive) to **transform the group as a whole** and position the constituent elements relative to the group.

Navigate to [https://codepen.io/createwithdata/pen/RwWmweL](https://codepen.io/createwithdata/pen/RwWmweL) to view the example in CodePen. (There’s a bit of additional CSS and the `g.chart` element has been transformed in the order to prevent clipping.)

The same example but using an update function (so that the data join is called each time the data changes) is at [https://codepen.io/createwithdata/pen/abdXJYv](https://codepen.io/createwithdata/pen/abdXJYv).

## Nested joins

This section shows how to join a nested array (in other words, an array of arrays) to HTML or SVG elements.

D>Although the Energy Explorer doesn’t use this technique, it’s a useful technique that isn’t widely known. However feel free to skip ahead if it’s not of interest!

Suppose you have this nested array:

```js
[
  [10, 30, 20],
  [40, 10, 30, 20]
]
```

The aim is to join this to nested HTML/SVG elements. For example, you might want to join the outer array to `<g>` elements and the inner arrays to `<circle>` elements. This would result in something like:

```html
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

This would look like:

{width: 50%}
![Nested array joined to `g` and `circle` elements](1a8c4def0472090a052c3de9930af8c0.png)

Each `<g>` element represents a row of circles. Each `<circle>` element is spaced horizontally by 100 pixels and sized according to the inner array values.

### Overview

The approach is to join the outer array to `<g>` elements. This means that each `<g>`‘s joined value is one of the inner arrays. We then iterate through the `<g>` elements and join the inner arrays to `<circle>` elements.


### Join outer array to `<g>` elements

Assume there’s an `<svg>` and `<g>` element on the page that’ll act as the container:

```html
<svg width="1200" height="1200">
  <g class="chart" transform="translate(50, 50)"></g>
</svg>
```

Let’s join an array of arrays named `myData` to `<g>` elements:

```js
let myData = [
  [10, 30, 20],
  [40, 10, 30, 20]
];

d3.select('g.chart')
  .selectAll('g')
  .data(myData)
  .join('g');
```

The above is similar to the join code seen in previous sections but the array is joined to `<g>` elements instead of `<circle>` elements. We’ll name this join the **outer** join. This will create the following elements:

```html
<g class="chart" transform="translate(50, 50)">
markua-start-insert
  <g></g>
  <g></g>
markua-end-insert
</g>
```

D>The first `<g>` element’s joined value is the array `[10, 30, 20]`. The second `<g>` element’s joined value is the array `[40, 10, 30, 20]`.


### Add `updateGroup` function

Now we add a new function `updateGroup` and call it on each `<g>` element using D3’s `.each` method. This is similar to what we did when joining arrays to groups of elements earlier in this chapter.

```js
let myData = [
  [10, 30, 20],
  [40, 10, 30, 20]
];

markua-start-insert
function updateGroup(d, i) {
  let g = d3.select(this);
  g.attr('transform', 'translate(0,' + i * 100 + ')');
}
markua-end-insert

d3.select('g.chart')
  .selectAll('g')
  .data(myData)
  .join('g')
markua-start-insert
  .each(updateGroup);
markua-end-insert
```

`updateGroup` gets called for each element in `myData`. Each time it’s called, the joined value is passed in as the first parameter and the index as the second parameter. Therefore the first time `updateGroup` is called, `d` is the array `[10, 30, 20]` and `i` is `0`. The second time it’s called, `d` is the array `[40, 10, 30, 20]` and `i` is `1`.

A selection containing the `<g>` element is assigned to the variable `g` (using `d3.select(this)`). The `<g>` element is then translated by `(0, i * 100)` where `i` is the index of the `<g>` element. Therefore the first `<g>` element is translated by `(0,0)` and the second one by `(0,100)`.

The resulting SVG looks like:

```html
<g class="chart" transform="translate(50, 50)">
  <g transform="translate(0,0)"></g>
  <g transform="translate(0,100)"></g>
</g>
```

### Join `d` to `circle` elements

We now join `d` to `<circle>` elements using standard join code. We'll name this the **inner join**:

```js
var myData = [
  [10, 30, 20],
  [40, 10, 30, 20]
];

function updateGroup(d, i) {
  var g = d3.select(this);
  g.attr('transform', 'translate(0,' + i * 100 + ')');

markua-start-insert
  g.selectAll('circle')
    .data(d)
    .join('circle')
    .attr('cx', function(d, i) {
      return i * 100;
    })
    .attr('r', function(d) {
      return d;
    });
markua-end-insert
}

d3.select('g.chart')
  .selectAll('g')
  .data(myData)
  .join('g')
  .each(updateGroup);
```

The important thing to note is we're now joining one of the **inner arrays** to `<circle>` elements.

`g.selectAll('circle')` selects all `circle` elements within the `<g>` element. (The first time this is called, this will be an empty selection. Return to the D3 Selections chapter to read more on this.)

The next line `.data(d)` specifies that we're joining `d` (which is one of the inner arrays such as `[10, 30, 20]`.)

The next line `.join('circle')` specifies the type of element we're joining. The `cx` attribute is then updated using the element index `i` (so that they're evenly spaced). The `r` attribute is updated using the joined value.

This produces the output:

```html
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

{width: 50%}
![Array of arrays joined to `<g>` and `<circle>` elements](e2f830fa221696db4a5aa02860c54f83.png)

Navigate to [https://codepen.io/createwithdata/pen/dyNgzqJ](https://codepen.io/createwithdata/pen/dyNgzqJ) to view this example in CodePen.

To summarise, there's two data joins: the outer one joins `myData` to `<g>` elements and the inner one joins the inner arrays (e.g. `[10, 30, 20]`) to `<circle>` elements. This can be quite tricky to understand, so don't worry too much if you don't get it at first!

### Update function with nested join

The nested join can be also put inside an update function (see the More on D3 selections chapter). For example:

```js
markua-start-insert
function getData() {
  // Return a randomized array of arrays
}
markua-end-insert

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

markua-start-insert
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
markua-end-insert
```

A function named `getData` is added which creates a randomised array of arrays. The outer join is moved to a function named `update` and we use `window.setInterval` to call `getData` and `update` at regular intervals:

Navigate to [https://codepen.io/createwithdata/pen/gOLmMrg](https://codepen.io/createwithdata/pen/gOLmMrg) to view the example in CodePen.

Every second, `getData` randomises the nested array. The update function is then called and this performs the nested join, resulting in the `<circle>` elements being added, removed and resized.