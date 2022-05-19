# Flourish popup library

One of the most useful features you can add to a data visualisation is an **information popup**. This is a box that displays more information about an HTML or SVG element that represents a piece of data.

For example the final version of the Energy Explorer has a popup that shows the energy mix as numbers:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-38.png)

It’s possible to implement your own popup using D3 but it’s such a common feature to implement that there are libraries to do this for you.

A good popup library is Flourish’s [popup component](https://github.com/kiln/flourish-popup). This is a popup that’s used in many of Flourish’s templates. (Just so you know, at the time of writing this I’m working at Flourish.)

### Installing the popup

The Flourish popup can be installed by downloading it from [here](https://cdn.flourish.rocks/popup-v1.1.js), moving it to your JavaScript directory in your project and including it using `<script>` tags. You’ll follow this approach in the next practical.

See the [popup’s documentation](https://github.com/kiln/flourish-popup) for other (more modern) ways of installing it.

### Initializing the popup

Once the popup component has been installed it can be initialized using:

```
var popup = Popup();
```

### Popup methods

The Flourish popup’s most interesting methods are: `.point`, `.html`, `.draw` and `.hide`.

The `.point` method sets the popup’s location. You can pass in either:

* `x` and `y` co-ordinates or
* an HTML/SVG element

I personally find it easier to **pass in an HTML/SVG element** because this saves you having to figure out `x` and `y` (which can sometimes get tricky).

The `.html` method sets the content of the popup. You pass in a string containing HTML code.

The `.draw` method draws (or redraws) the popup.

The `.hide` method hides the popup.

### Popup styling

In general you need to set `pointer-events` to `none` on the popup, otherwise the **popup might flicker** when the mouse pointer is close to the popup. The popup has a class attribute named `flourish-popup` so you can apply the rule using:

```
.flourish-popup {
pointer-events: none;
}
```

You can style the content of the popup using the `flourish-popup-content` class attribute:

```
.flourish-popup-content {
font-family: sans-serif;
color: #555;
}
```

### Example

Let’s add the popup to the circles example from the [event handling section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/d3-event-handling/).

The HTML looks like:

```
<svg width="400" height="200">
<g class="circles" transform="translate(50, 100)">
</g>
</svg>
```

and the JavaScript is changed to:

```
var myData = [10, 40, 30];
var popup = Popup();
function handleMouseover(e, d) {
popup.point(this)
.html('The value is ' + d)
.draw();
}
function handleMouseout() {
popup.hide();
}
d3.select('.circles')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d;
})
.attr('cx', function(d, i) {
return i * 100;
})
.on("mouseover", handleMouseover)
.on("mouseout", handleMouseout);
```

The code is mostly the same as the code in the [D3 event handling section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/d3-event-handling/).

The main differences are:

* a Flourish popup is initialized and assigned to the variable `popup`
* `handleMouseover` sets the popup position (using `.point`) and content (using `.html`) and calls `.draw` to make the popup visible
* `handleMouseout` hides the popup using `.hide`

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/MWKgJLX](https://codepen.io/createwithdata/pen/MWKgJLX) to view the example in CodePen

A popup appears when a circle is hovered:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-39.png)

One of the benefits of using a popup library like this one is that it handles cases where the popup might be cropped. For example if the first circle is hovered the popup automatically aligns to the right:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-40.png)

In the next practical you’ll add a popup to the Energy Explorer.