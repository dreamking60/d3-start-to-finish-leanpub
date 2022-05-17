# Practical: Add a popup

In the next two practicals you’ll **add a popup** to the Energy Explorer using the Flourish popup component:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image.png)

When a country is hovered a popup will appear containing the country name together with the country’s energy mix.

In this practical you’ll add the popup that and populate it with the country name:

![](http://learn.createwithdata.com/wp-content/uploads/2020/07/image-42.png)

(You’ll add the energy mix data to the popup in the next practical.)

There are a few steps in this practical:

* **install** the Flourish popup component
* add a **new module** for managing the popup
* add mouseover and mouseout **event handlers** and attach them to the countries
* set `pointer-events` **CSS rule** for the popup and the circles
* **populate the popup** with the country name

In your code editor open the `step10` directory of the `d3-start-to-finish` code.

### Install the Flourish popup component

The `js/lib` directory in `step10` already contains the Flourish popup component. (It was downloaded from `https://cdn.flourish.rocks/popup-v1.full.min.js`.)

In `index.html` add `script` tags to load the popup component:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
...
<script src="js/lib/d3.min.js"></script>
<script src="js/lib/popup-v1.1.1.min.js"></script>
...
</body>
</html>
```

### Create module for popup code

Now create a new file within the `js` directory and name it `popup.js`. This will contain popup related code.

Include the new file `popup.js` in the application by adding a new `<script>` tag to `index.html`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
...
<script src="js/layout.js"></script>
<script src="js/popup.js"></script>
<script src="js/main.js"></script>
</body>
</html>
```

### Initialize the popup

In the new file `popup.js` initialize the popup:

{caption: "popup.js", line-numbers: false}
```
var popup = Popup();
```

### Create event handlers

You’ll now create **event handlers** for when the mouse moves over (and out of) a country.

Start by adding event listeners to each country group in `initializeGroup` (`update.js`). Use D3’s `.on` method which was covered in the [D3 Event Handling](https://learn.createwithdata.com/books/d3-start-to-finish/sections/d3-event-handling/) section:

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.classed('country', true)
.on('mouseover', handleMouseover)
.on('mouseout', handleMouseout);
g.append('circle')
.classed('renewable', true);
...
}
```

Now in `popup.js` add the `handleMouseover` event handler:

{caption: "popup.js", line-numbers: false}
```
var popup = Popup();
function handleMouseover(e, d) {
}
```

We saw in the [event handling section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/d3-event-handling/) that D3 passes the joined value into `handleMouseover` as the second parameter. In our case the joined value is an object from `layoutData` (see `update` in `main.js` for a reminder) and will have the following structure:

```
{
"x": 814.2857142857142,
"y": 264.2857142857143,
"renewableRadius": 17.79887636902959,
"oilGasCoalRadius": 19.488458122694055,
"hydroelectricRadius": 14.261837188805655,
"nuclearRadius": 0,
"labelText": "El Salvad...",
"labelOffset": 50
}
```

Start by setting the popup’s content using the `labelText` property:

{caption: "popup.js", line-numbers: false}
```
function handleMouseover(e, d) {
popup
.html(d.labelText);
}
```

We saw in the event handling section that D3 assigns the element that triggered the event to the `this` keyword. Let’s use this to position the popup:

{caption: "popup.js", line-numbers: false}
```
function handleMouseover(e, d) {
popup
.point(this)
.html(d.labelText);
}
```

Finally call `.draw` to draw the popup:

{caption: "popup.js", line-numbers: false}
```
function handleMouseover(e, d) {
popup
.point(this)
.html(d.labelText)
.draw();
}
```

The `mouseout` handler will hide the popup:

{caption: "popup.js", line-numbers: false}
```
var popup = Popup();
function handleMouseover(e, d) {...}
function handleMouseout() {
popup.hide();
}
```

### Set pointer-events

As explained in the previous section it’s important to switch off pointer events on the popup itself (to eliminate flickering).

In `css/style.css` add the following at the bottom:

{caption: "style.css", line-numbers: false}
```
.country .label {
text-anchor: middle;
}
.flourish-popup {
pointer-events: none;
}
```

By default SVG shapes with a `fill` of `none` do not trigger pointer events. This means that **only the renewable circle** **triggers events**.

You can make **all four circles** respond to pointer events by setting `pointer-events` to `all` on the `g` element containing the circles:

{caption: "style.css", line-numbers: false}
```
circle.nuclear {...}
.country {
pointer-events: all;
}
.country .label {
text-anchor: middle;
}
```

Save `index.html`, `popup.js`, `main.js` and `style.css` and refresh your browser (making sure it’s loading `step10`).

Hover over a country and you should see a simple popup appear:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-42.png)

In the next practical you’ll add the energy mix data to the popup.

## Practical: Populate the popup

In this practical you’ll **add the energy mix data** to the popup.

The end result will look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-43.png)

In your code editor open the `step10` directory of the `d3-start-to-finish` code. (This will already be open if you’ve just completed the previous practical.)

### Add energy mix data to popup

Recall that each `g` element in the chart is joined to an object that the layout function generates:

```
{
"x": 814.2857142857142,
"y": 264.2857142857143,
"renewableRadius": 17.79887636902959,
"oilGasCoalRadius": 19.488458122694055,
"hydroelectricRadius": 14.261837188805655,
"nuclearRadius": 0,
"labelText": "El Salvad...",
"labelOffset": 50
}
```

An object with this structure is passed into `handleMouseover` which is where the popup will be populated.

You need the name and energy mix for each country so add them to the `layout` function in `layout.js`:

{caption: "layout.js", line-numbers: false}
```
function layout() {
...
var layoutData = data.map(function(d, i) {
...
item.labelText = getTruncatedLabel(d.name);
item.labelOffset = maxRadius + labelHeight;
item.popupData = {
name: d.name,
renewable: d.renewable,
oilgascoal: d.oilgascoal,
hydroelectric: d.hydroelectric,
nuclear: d.nuclear
};
return item;
});
...
}
```

Each layout item will now look something like:

```
{
"x": 814.2857142857142,
"y": 264.2857142857143,
"renewableRadius": 17.79887636902959,
"oilGasCoalRadius": 19.488458122694055,
"hydroelectricRadius": 14.261837188805655,
"nuclearRadius": 0,
"labelText": "El Salvad...",
"labelOffset": 50,
"popupData": {
"name": "El Salvador",
"renewable": 35.2,
"oilgascoal": 42.2,
"hydroelectric": 22.6,
"nuclear": null
}
}
```

> It’s not strictly necessary to put the country name and indicator values inside a separate object but it does keep things tidier.

Now in `popup.js` add a new function named `popupTemplate` which takes the joined data (it’ll have a structure like the above object) and outputs an HTML string representing the popup content. This function calls another new function `getPopupEntry` which generates the HTML for a single indicator (such as Renewable) in the popup:

{caption: "popup.js", line-numbers: false}
```
var popup = Popup();
function getPopupEntry(d, type, label) {
if (d.popupData[type] !== null) {
return '<div>' + label + ': ' + d.popupData[type] + '%</div>';
}
return '';
}
function popupTemplate(d) {
var html = '';
html += '<h3>' + d.popupData.name + '</h3>';
html += getPopupEntry(d, 'renewable', 'Renewable');
html += getPopupEntry(d, 'oilgascoal', 'Oil, Gas & Coal');
html += getPopupEntry(d, 'hydroelectric', 'Hydroelectric');
html += getPopupEntry(d, 'nuclear', 'Nuclear');
return html;
}
function handleMouseover(e, d) {...}
function handleMouseout() {...}
```

`popupTemplate` creates a variable `html` which is initialized with an empty string.

The country name (`d.popupData.name`) is then enclosed within `<h3>` tags and appended to the `html` string.

`getPopupEntry` is then called for each energy type. This checks whether the energy type value (e.g. `d.popupData.renewable`) is non-null and if so returns an HTML string consisting of the energy type and it’s percentage value. (A `null` value indicates there was no data for that particular country and energy type.)

The output of `getPopupEntry` will look something like:

```
"<div>Renewable: 1.9%</div>"
```

Finally in `handleMouseover` replace `d.labelText` with a call to `popupTemplate`. Pass `d` into `popupTemplate`:

{caption: "popup.js", line-numbers: false}
```
function handleMouseover(e, d) {
popup
.point(this)
.html(popupTemplate(d))
.draw();
}
```

Now save `layout.js` and `popup.js` and hover over a country. You should see something like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-43.png)

The popup is positioned towards the bottom of the circle. This is because the Flourish popup positions the popup at the center of the country group. Each country group consists of four circles **and the text label** which is why the center is just below the circle center.

You’ll fix this in the next practical by adding another (hidden) element that determines the popup location.

## Practical: Position the popup

Currently the popup appears just below the circle center:

![](http://learn.createwithdata.com/wp-content/uploads/2020/07/image-43.png)

We’d like the popup to appear **towards the top** of the circle. You’ll do this by adding a hidden element, placing it where you’d like the popup to appear and pass this element into the popup’s `.position` method.

At the end of this practical the popup will appear just above the circle center:

![](http://learn.createwithdata.com/wp-content/uploads/2020/07/image-44.png)

You’ll:

* **add another circle** to each country that will determine the popup’s position
* **add a property** `popupOffset` to the layout function that specifies where the new circle will be positioned
* **position the new circle** according to the new layout property `popupOffset`
* use the new circle to **position the popup**

In your code editor open the `step10` directory of the `d3-start-to-finish` code. (This will already be open if you’ve just completed the previous practical.)

### Add another circle to each country group

In `initializeGroup` in `update.js` append a new circle and give it a class attribute of `popup-center`.

You also need to set its radius to a non-zero value. (This is to deal with a rendering optimization in Firefox.)

{caption: "update.js", line-numbers: false}
```
function initializeGroup(g) {
g.classed('country', true)
.on('mouseover', handleMouseover)
.on('mouseout', handleMouseout);
g.append('circle')
.classed('popup-center', true)
.attr('r', 1);
g.append('circle')
.classed('renewable', true);
...
}
```

In `style.css` set the new circle’s opacity to zero:

{caption: "style.css", line-numbers: false}
```
...
.popup-center {
opacity: 0;
}
.flourish-popup {
pointer-events: none;
}
```

### Add popupOffset property to the layout

In the `layout` function (`layout.js`) add a new property named `popupOffset` to `item`. Set it to half of `maxRadius` and make it negative so that the popup is positioned above (rather than below) the circle center:

{caption: "layout.js", line-numbers: false}
```
function layout(data) {
...
item.labelOffset = maxRadius + labelHeight;
item.popupOffset = -0.5 * maxRadius;
...
}
```

### Position the popup center circle

In `updateGroup` select the popup center circle and set its `cy` attribute to `d.popupOffset`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) {
var g = d3.select(this);
if(g.selectAll('*').empty()) initializeGroup(g);
g.attr('transform', 'translate(' + d.x + ',' + d.y + ')');
g.select('.popup-center')
.attr('cy', d.popupOffset);

...
}
```

### Use the popup center circle to position the popup

Finally in `handleMouseover` select the popup center circle and get its SVG element using the `.node` method. (See the [More selection functions](https://learn.createwithdata.com/books/d3-start-to-finish/sections/more-selection-functions/) section for more about the `.node` method.)

Use this element to set the popup position:

{caption: "popup.js", line-numbers: false}
```
function handleMouseover(e, d) {
var popupCenter = d3.select(this)
.select('.popup-center')
.node();
popup
.point(popupCenter)
.html(popupTemplate(d.datum))
.draw();
}
```

Now save `layout.js`, `main.js` and `popup.js` and refresh the browser.

The popup should now appear towards the top of the circles:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-44.png)

### Summing up

In this and the previous two practicals you added an information popup to the Energy Explorer. This allows users to get precise information for each country.

Although there was quite a lot of work to get the popup up and running, using an existing library has saved a lot of work. For instance, the popup library automatically positions the popup so that it doesn’t disappear off the edges of the screen.

In the next section you’ll learn how about state management.