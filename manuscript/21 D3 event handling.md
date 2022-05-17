# D3 event handling

In this section you’ll learn how to add **event handlers** to HTML and SVG elements. An event handler is a function that gets called when the **user interacts** with an HTML or SVG element.

The most common interactions are:

* **click** (the user clicks an element)
* **mouseover** (the user moves the pointer into an element)
* **mouseout** (the user moves the pointer out of an element)
* **mousemove** (the user moves the pointer within an element)

You can add an event handler to the HTML/SVG elements of a D3 selection using the `.on` method:

```
function handleClick() {
console.log('A circle was clicked');
}
d3.selectAll('circle')
.on('click', handleClick);
```

In the above code D3 selects all `circle` elements on the page. It then attaches an event listener to each circle so that `handleClick` is called when a circle is clicked.

Here’s the example on CodePen (click on a circle and an alert should appear):

Navigate to [https://codepen.io/createwithdata/pen/PoZvrmy](https://codepen.io/createwithdata/pen/PoZvrmy) to view the example in CodePen

Things get more interesting if the selection has been joined to an array of data.

For example, suppose your HTML is:

```
<svg>
<g class="circles" transform="translate(50, 50)">
</g>
</svg>
```

and you join an array to circle elements:

```
var myData = [10, 40, 30];
d3.select('g.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d;
})
.attr('cx', function(d, i) {
var circleSpacing = 100;
return i * circleSpacing;
});
```

You can add an event handler to the selection using `.on`:

```
var myData = [10, 40, 30];
function handleClick(e, d, i) {
alert("A circle was clicked and it's value is " + d);
}
d3.select('g.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d;
})
.attr('cx', function(d, i) {
var circleSpacing = 100;
return i * circleSpacing;
})
.on('click', handleClick);
```

When a circle is clicked, the event handler `handleClick` is called and the **joined value is passed in as the second parameter** (usually named `d`). The index of the element is passed in as the third parameter (usually named `i`).

> Event handlers in D3 versions up to including 5 look like `function(d, i) {...}` where the **first** parameter is the joined value and the second parameter is the index.
> 
> In version 6 of D3 the browser `MouseEvent` object is passed in as the first parameter to an event handler function.

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/jOWogEr](https://codepen.io/createwithdata/pen/jOWogEr) to view the example in CodePen

This approach lets you create **data-driven interactions**. In other words you can create interactivity that uses data that’s been joined to HTML/SVG elements.

Here’s another example where a simple information panel is updated when an element is clicked. Here’s the HTML:

```
<div id="info"></div>
<div>
<svg width="400" height="100">
<g class="circles" transform="translate(50, 50)">
</g>
</svg>
</div>
```

Notice there’s a `div` element (with class attribute `info`) that acts as an information panel. The JavaScript is:

```
var myData = [10, 40, 30];
function handleClick(e, d) {
d3.select('#info')
.text("Value is " + d);
}
d3.select('g.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d;
})
.attr('cx', function(d, i) {
var circleSpacing = 100;
return i * circleSpacing;
})
.on('click', handleClick);
```

When a circle is clicked, `handleClick` is called. `handleClick` selects the information panel and updates its text content with the joined value of the clicked circle.

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/zYBoxpy](https://codepen.io/createwithdata/pen/zYBoxpy) to view the example in CodePen

### Event types

The most common event types you’ll use when building data visualisations are `click`, `mouseover` and `mouseout`.

This table shows when each event type occurs:

<table class=""><tbody><tr><td><strong>Event type</strong></td><td><strong>When it occurs</strong></td></tr><tr><td><code>click</code></td><td>When an element is clicked</td></tr><tr><td><code>mouseover</code></td><td>When the mouse pointer first moves over an element</td></tr><tr><td><code>mouseout</code></td><td>When the mouse pointer first moves out of an element</td></tr></tbody></table>

You can call the `.on` method more than once if you want to react to more than one event type.

For example, let’s modify the previous example so that all three event types are handled:

```
var myData = [10, 40, 30];
function handleClick(e, d) {
d3.select('#info')
.text("Clicked element with value " + d);
}
function handleMouseover(e, d) {
d3.select('#info')
.text("Value is " + d);
}
function handleMouseout(e, d) {
d3.select('#info')
.text(null);
}
d3.select('g.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d;
})
.attr('cx', function(d, i) {
var circleSpacing = 100;
return i * circleSpacing;
})
.on('mouseover', handleMouseover)
.on('mouseout', handleMouseout)
.on('click', handleClick);
```

When the mouse pointer first moves over a circle, `handleMouseover` is called and the information panel updates with the circle’s joined value.

When a circle is clicked, `handleClick` is called and the information panel updates with a message saying an element has been clicked.

When the mouse pointer moves off a circle `handleMouseout` is called which clears the information panel.

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/oNbRKEV](https://codepen.io/createwithdata/pen/oNbRKEV) to view the example in CodePen

### index and this

The element index (usually named `i`) is passed in as the third parameter to the event handler.

The HTML/SVG element which triggered the event is assigned to the `this` keyword within the event handler function.

They can be used in this manner:

```
function handleClick(e, d, i) {
console.log('the HTML/SVG element', this, 'was clicked');
console.log('the joined value is', d);
console.log('the element index is', i);
}
```

### Coming up

In the next section we’ll look at the [Flourish popup library](https://github.com/kiln/flourish-popup) which lets you add a popup to a web application.