# Flourish popup library

One of the most useful features you can add to a data visualisation is an **information popup**. This is a box that displays information about a selected item.

For example the final version of the Energy Explorer has a popup that shows the energy mix as numbers:

{width: 33%}
![Information popup in Energy Explorer](ef65fec750293006f48c40ea8d47b67f.png)

It’s possible to implement your own popup using D3 but there are libraries that do this for you.

A good popup library is Flourish’s [popup component](https://github.com/kiln/flourish-popup). This is a popup that’s used in many of Flourish’s templates. (Just so you know, at the time of writing this I’m working at Flourish.)

## Installing the popup

The Flourish popup can be installed by downloading it from [https://cdn.flourish.rocks/popup-v1.1.js](https://cdn.flourish.rocks/popup-v1.1.js), moving it to your JavaScript directory in your project and including it using `<script>` tags. You’ll follow this approach in the next practical.

See the [popup’s documentation](https://github.com/kiln/flourish-popup) for other (more modern) ways of installing it.

## Initializing the popup

Once the popup component has been installed it can be initialized using:

```js
let popup = Popup();
```

### Popup methods

The Flourish popup’s most interesting methods are: `.point`, `.html`, `.draw` and `.hide`.

The `.point` method sets the popup’s location. You can pass in either:

* `x` and `y` co-ordinates or
* an HTML/SVG element

I personally find it easier to **pass in an HTML/SVG element** because this saves you having to figure out `x` and `y` (which can sometimes get tricky).

The `.html` method sets the content of the popup. You pass in a string containing HTML code. The `.draw` method draws (or redraws) the popup. The `.hide` method hides the popup.

### Popup styling

In general you need to set `pointer-events` to `none` on the popup, otherwise the **popup might flicker** when the mouse pointer is close to the popup. The popup has a class attribute named `flourish-popup` so you can apply the rule using:

```css
.flourish-popup {
  pointer-events: none;
}
```

You can style the content of the popup using the `flourish-popup-content` class attribute:

```css
.flourish-popup-content {
  font-family: sans-serif;
  color: #555;
}
```

## Example

Let’s add the popup to the circles example from the D3 Event Handling chapter. The HTML looks like:

```html
<svg width="400" height="200">
  <g class="circles" transform="translate(50, 100)">
  </g>
</svg>
```

and the JavaScript is changed to:

```js
let myData = [10, 40, 30];

markua-start-insert
let popup = Popup();
markua-end-insert

function handleMouseover(e, d) {
markua-start-insert
  popup.point(this)
    .html('The value is ' + d)
    .draw();
markua-end-insert
}

function handleMouseout() {
markua-start-insert
  popup.hide();
markua-end-insert
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

The code is mostly the same as the code in the D3 Event Handling chapter. The main differences are:

* a Flourish popup is initialized and assigned to the variable `popup`
* `handleMouseover` sets the popup position by passing `this` into `.point`. (`this` is the HTML/SVG element that triggered the event.)
* the content is set using `.html` and `.draw` is called to make the popup visible
* `handleMouseout` hides the popup using `.hide`

Navigate to [https://codepen.io/createwithdata/pen/MWKgJLX](https://codepen.io/createwithdata/pen/MWKgJLX) to view this example in CodePen.

A popup appears when a circle is hovered:

{width: 33%}
![Flourish popup activated when a circle is hovered](073d2f7a10bc201591eb75a1b2266024.png)

One of the benefits of using the Flourish popup library is it handles cases where the popup might be cropped. For example if the first circle is hovered the popup automatically aligns to the right:

{width: 33%}
![Flourish popup when circle near edge of container is hovered](34c611fac3287b6c103f7b6c10bd26c4.png)

In the next practical you’ll add a popup to Energy Explorer.