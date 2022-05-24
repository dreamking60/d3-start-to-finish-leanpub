# Practical: Add Google font

At the moment the Energy Explorer is using the web browser’s **default font** for all text elements. This is usually a serif font such as Times or Times New Roman.

In general sans-serif fonts (such as Helvetica) are recommended for data visualisation text because they tend to be clearer (they have less visual ‘noise’).

Font support varies across platforms. For example, Helvetica is built into MacOS but not Windows. For this reason **I tend to use Google Fonts** as this ensures the same font is used regardless of operating system.

There’s a huge amount of resource and opinion surrounding font choice and unless you’re a trained designer it can be hard to know where to turn.

I usually turn to Google Fonts and [filter by Sans Serif and sort by most popular](https://fonts.google.com/?category=Sans+Serif&sort=popularity). Currently this returns Roboto, Open Sans and Lato as popular sans serif fonts. I usually try each of these and use my instinct to choose the right one.

In the case of the Energy Explorer I’ve chosen Open Sans. Feel free to choose a different font if you prefer.

In this practical you’ll:

* **install** Google Fonts’ Open Sans font
* **add CSS** so that the new font is used throughout the visualisation.

In your code editor open the `step15` directory of the `d3-start-to-finish` code.

### Install Open Sans

Open `index.html` and add the following line to the `<head>` element:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Energy mix by Country 2015</title>
<link href="https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="css/style.css">
</head>
<body>
...
</body>
</html>
```

This line was provided by Google Fonts. To get the line yourself:

* navigate to Google Fonts and select Open Sans
* select the Regular 400 and Semi-bold 600 sizes
* view your selected families (currently this is an icon at the top right of the screen, but this might change!)
* click on Embed and copy the `<link>` code

Save `index.html`.

### Apply Open Sans to all text

Now open `style.css` and in the `body` rule add a `font-family` declaration:

{caption: "style.css", line-numbers: false}
```
body {
background-color: #FFFFF7;
font-family: 'Open Sans', sans-serif;
cursor: default;
}
```

This will result in Open Sans being used througout the visualisation.

`sans-serif` is also added the the list of fonts as a fallback in case Open Sans doesn’t load.

Save `style.css` and refresh your browser (making sure it’s loading `step15`) and you should see that the font has changed to Open Sans:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-14-1024x261.png)

The country labels don’t look quite right – they feel a bit too big. This and other style will be tweaked in an upcoming practical.

In the next practical you’ll add some headings to `style.css` to help keep things clear and tidy.

## Practical: Organize the CSS file

This is a simple practical that adds some **headings** (using CSS comments) to `style.css` to help you and others understand and navigate the file.

You’ll add the following headings:

* Global (for rules that affect everything in the page)
* Controls (for the controls section that contains the menu and legend)
* Menu
* Legend
* Circles (for the circle styling)
* Chart (for chart styling)
* Popup (for popup styling)

The headings are in the form of CSS comments which are denoted by `/*` and `*/`.

In your code editor open the `step15` directory of the `d3-start-to-finish` code. (This’ll already be open if you’ve just completed the previous practical.)

Open `style.css` and add the following headings:

{caption: "style.css", line-numbers: false}
```
/* Global */
body { ... }
#wrapper { ... }
/* Controls */
#controls { ... }
/* Menu */
.menu .items { ... }
.menu .item { ... }
.menu .item.selected, .menu .item:hover { ... }
/* Legend */
.legend circle { ... }
.legend text { ... }
/* Circles */
circle.renewable { ... }
circle.oilgascoal { ... }
circle.hydroelectric { ... }
circle.nuclear { ... }
/* Chart */
.country { ... }
.country .label { ... }
/* Popup */
.flourish-popup { ... }
```

Now save `style.css`. Refresh your browser and check it works as before.

## Practical: Style the menu

In this practical you’ll style the menu so that’ll it’ll look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-15.png)

You’ll **add a circle to each indicator** (except for Country) that shows the style used for that indicator in the chart.

For example, in the visualisation the circle representing renewable energy is solid green. The same style will be used for the Renewable item in the menu.

You’ll also **add a ‘Sort by’ label** to the left of the menu and add some CSS to vertically align the menu items.

The steps are:

* **add a circle** to each menu item in
* **vertically align** the menu items
* **add a ‘Sort by’ label** to the menu
* other **CSS changes**

In your code editor open the `step15` directory of the `d3-start-to-finish` code. (This’ll already be open if you’ve just completed the previous practical.)

### Add a circle to menu items

Currently the menu labels are created in `updateMenu` in `menu.js`:

{caption: "menu.js", line-numbers: false}
```
function updateMenu() {
d3.select('#controls .menu .items')
.selectAll('.item')
.data(menuItems)
.join('div')
...
.text(function(d) {
return d.label;
})
.on('click', handleMenuClick);
}
```

D3’s `text` method is used to set the text content of each menu item.

Each menu item currently looks like:

```
<div class="item">Renewable</div>
```

We’d like to add an SVG circle to each item, so that each item will look like:

```
<div class="item">
<svg width="18" height="18">
<circle class="renewable" cx="9" cy="9" r="8" />
</svg>
<div class="label">Renewable</div>
</div>
```

Instead of each menu item being a single `div` element, it’s now a `div` element that contains an `svg` element which contains a circle and a `div` element for the label.

We’ll use D3’s `html` method to create the above structure. (The `html` method allows you to add a snippet of HTML to the elements of a selection.)

In `menu.js` change the `text` method in `updateMenu` to the `html` method:

{caption: "menu.js", line-numbers: false}
```
function updateMenu() {
d3.select('#controls .menu .items')
.selectAll('.item')
.data(menuItems)
.join('div')
...
.html(function(d) {
return d.label;
})
.on('click', handleMenuClick);
}
```

Move the function that’s passed into `html` into a standalone function named `getHtml`:

{caption: "menu.js", line-numbers: false}
```
function getHtml(d) {
return d.label;
}
function handleMenuClick(e, d) { ... }
function updateMenu() {
d3.select('#controls .menu .items')
.selectAll('.item')
.data(menuItems)
.join('div')
...
.html(getHtml)
.on('click', handleMenuClick);
}
```

Save `menu.js` and check that the menu still looks the same.

Next you’ll create another new function named `getCircle` which returns the following snippet of HTML:

```
  <svg width="18" height="18">
<circle class="renewable" cx="9" cy="9" r="8" />
</svg>
```

`getCircle`‘s first parameter will determine the class attribute. Here’s the function:

{caption: "menu.js", line-numbers: false}
```
let menuItems = [ ... ];
function getCircle(id) {
let svg = '<svg width="18" height="18"><circle class="' + id + '" cx="9" cy="9" r="8"></svg>';
return svg;
}
function getHtml(d) { ... }
```

This function returns a string containing an SVG element containing a `circle` element.

The circle is given a class attribute (such as `renewable`) corresponding to the indicator name. This will cause it to be styled in the same manner as the circles in the chart because of the rules for `circle.renewable`, `circle.oilgascoal` and so on in `style.css`.

{caption: "style.css", line-numbers: false}
```
/* Circle */
circle.renewable {
fill: #7FB069;
}
circle.oilgascoal {
fill: none;
stroke: #BCB5B2;
}
etc.
```

Now modify `getHtml` so that it returns a string containing the output of `getCircle` and a `div` element containing the label:

{caption: "menu.js", line-numbers: false}
```
function getCircle(id) {
let svg = '<svg width="18" height="18"><circle class="' + id + '" cx="9" cy="9" r="8"></svg>';
return svg;
}
function getHtml(d) {
let circle = d.id === 'country' ? '' : getCircle(d.id);
let html = circle + '<div class="label">' + d.label + '</div>';
return html;
}
function handleMenuClick(d) { ... }
```

The finished `menu.js` looks like:

{caption: "menu.js", line-numbers: false}
```
let menuItems = [
{
id: 'country',
label: 'Country'
},
{
id: 'renewable',
label: 'Renewable'
},
{
id: 'oilgascoal',
label: 'Oil, Gas & Coal'
},
{
id: 'hydroelectric',
label: 'Hydroelectric'
},
{
id: 'nuclear',
label: 'Nuclear'
}
];
function getCircle(id) {
let svg = '<svg width="18" height="18"><circle class="' + id + '" cx="9" cy="9" r="8"></svg>';
return svg;
}
function getHtml(d) {
let circle = d.id === 'country' ? '' : getCircle(d.id);
let html = circle + '<div class="label">' + d.label + '</div>';
return html;
}
function handleMenuClick(e, d) {
action('setSelectedIndicator', d.id);
}
function updateMenu() {
d3.select('#controls .menu .items')
.selectAll('.item')
.data(menuItems)
.join('div')
.classed('item', true)
.classed('selected', function(d) {
return state.selectedIndicator === d.id;
})
.html(getHtml)
.on('click', handleMenuClick);
}
```

`getHtml` gets called for each item in the `menuItems` array (see the top of `menu.js`).

If `id` is `'country'` (the first menu item) we use an empty string instead of a circle (because the first menu item doesn’t have an associated circle).

The output of `getHtml` will look something like (I’ve added new lines for clarity):

```
'<svg width="18" height="18">
<circle class="renewable" cx="9" cy="9" r="8" />
</svg>
<div class="label">Renewable</div>'
```

Save `menu.js` and refresh the browser. You should see:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-16.png)

### Align menu items

In `style.css` set the `display` property of the menu items to `flex`. This will make the circles sit side by side with the labels.

Also set `align-items` to `center` to vertically align the circles and labels:

{caption: "style.css", line-numbers: false}
```
 .menu .item {
padding: 0 1rem;
color: #333;
opacity: 0.5;
display: flex;
align-items: center;
}
```

Save `style.css` and refresh the browser. The menu should now look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-17.png)

Now add a rule for the menu item labels and add declarations for `font-size` and `padding-left`:

{caption: "style.css", line-numbers: false}
```
.menu .item { ... }
.menu .item .label {
font-size: 1.2rem;
padding-left: 0.2rem;
}
.menu .item.selected, .menu .item:hover { ... }
```

Save style.css and refresh the browser. The menu should now look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-18.png)

### Add ‘Sort by:’ label to menu

In `index.html` add a ‘Sort by:’ label to the menu:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
<div id="wrapper">
<div id="controls">
<div class="menu">
<div>Sort by:</div>
<div class="items"></div>
</div>
<div class="legend">
...
```

In `style.css` add a new rule for the menu and add a declaration for the `font-size` (this’ll determine the size of the new label):

{caption: "style.css", line-numbers: false}
```
...
/* Menu */
.menu {
font-size: 0.9rem;
}
.menu .items { ... }
...
```

And now add declarations to vertically align the new label and the menu items:

{caption: "style.css", line-numbers: false}
```
...
/* Menu */
.menu {
font-size: 0.9rem;
display: flex;
align-items: center;
}
.menu .items { ... }
...
```

Save `style.css` and refresh the browser. The menu should now look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-19.png)

### Other CSS tweaks

Currently each menu item has an opacity of 0.5 and if it’s selected its opacity is changed to 1:

{caption: "style.css", line-numbers: false}
```
.menu .item {
padding: 0 1rem;
color: #333;
opacity: 0.5;
display: flex;
align-items: center;
}
.menu .item .label {
font-size: 1.2rem;
padding-left: 0.2rem;
opacity: 0.5;
}
.menu .item.selected, .menu .item:hover {
opacity: 1;
}
```

However we’d like all the **menu item circles** to have an opacity of 1 so that the menu circle style always matches the circles in the visualisation.

You’ll:

* modify the CSS so that the menu **labels** (but not the menu item circles) have an opacity 0.5
* modify the selectors for highlighting the menu items so that they apply just to the menu labels (and not the whole menu item)

Start by moving the `opacity` declaration from `.menu .item` to `.menu .item .label`:

{caption: "style.css", line-numbers: false}
```
.menu .item {
padding: 0 1rem;
color: #333;
display: flex;
align-items: center;
}
.menu .item .label {
font-size: 1.2rem;
padding-left: 0.2rem;
opacity: 0.5;
}
```

Now modify the selectors of selected and hovered menu items so that the label’s opacity is set to 1:

{caption: "style.css", line-numbers: false}
```
.menu .item .label { ... }
.menu .item.selected .label, .menu .item:hover .label {
opacity: 1;
}
```

Save `style.css` and refresh the browser. Now the menu circles should always have an opacity of 1 regardeless of whether they’re selected.

Furthermore the labels should be gray unless hovered or selected, in which case they’ll be dark gray.

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-20.png)

### Summing up

The menu is now complete. You added circles to the menu items indicating which energy type each circle style represents.

There was quite a lot of detailed CSS to add. This is fairly typical when adding finishing touches to a data visualisation. It’s often a significant portion of the work and sometimes requires a lot of experimentation and iteration.

You’re nearly there. The remaining steps are to add a header and footer and a few final bits of CSS styling.

## Practical: Add header and footer

In this practical you’ll add a **header** and **footer** to the Energy Explorer.

The header will contain a main title **Energy mix by country (2015)**:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-21-1024x209.png)

and the footer will contain information on the data source and attribution information:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-22-1024x186.png)

The steps are:

* **add a header** containing the main title
* **align the header** to the right
* **add a footer** containing information on the data source and an attribution
* **align the footer** items

### Add the header

Open `index.html` and add a `div` element and give it an `id` attribute with value `header`. Add the title ‘Energy mix by country (2015)’ to the new `div`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
<div id="wrapper">
<div id="header">Energy mix by country (2015)</div>
<div id="controls">
<div class="menu">
...
```

Save `index.html`.

### Align header

In `style.css` add a new block for the header:

{caption: "style.css", line-numbers: false}
```
/* Global */
...
#wrapper { ... }
/* Header */
#header {
}
/* Controls */
#controls { ... }
...
```

Set the header’s `font-size` to `1.5rem`. Close up the letter spacing a bit by setting `letter-spacing` to `-0.03rem`:

{caption: "style.css", line-numbers: false}
```
/* Header */
#header {
font-size: 1.5rem;
letter-spacing: -0.03rem;
}
```

Finally add some padding around the header and align the text to the right:

{caption: "style.css", line-numbers: false}
```
/* Header */
#header {
font-size: 1.5rem;
letter-spacing: -0.03rem;
padding: 1rem;
text-align: right;
}
```

Save `style.css` and refresh the browser. There should now be a header at the top of the energy explorer:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-23-1024x212.png)

### Add footer

Return to `index.html` and add a `div` below the chart wrapper (`div#chart-wrapper`). Give the new `div` an `id` of `footer`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
<div id="wrapper">
<div id="header">Energy mix by country (2015)</div>
<div id="controls">
...
</div>
<div id="chart-wrapper">
...
</div>
<div id="footer">
</div>
</div>
...
</body>
</html>
```

Add two `divs` to the footer. The first one will contain a link to the data source and second one attribution information:

{caption: "index.html", line-numbers: false}
```
...
<div id="footer">
<div>Data: <a href="https://datacatalog.worldbank.org/dataset/world-development-indicators">World Bank</a></div>
<div><a href="https://learn.createwithdata.com">D3 Start to Finish</a></div>
</div>
...
```

### Align footer

In `style.css`, just above the popup block, add a block for the footer:

{caption: "style.css", line-numbers: false}
```
/* Chart */
...
/* Footer */
#footer {
}
/* Popup */
...
```

Set the footer’s `display` to `flex` so that the items are arranged horizontally. Set `justify-content` to `space-between` so that the items fill the width of the container (similarly to what was done with the controls section):

{caption: "style.css", line-numbers: false}
```
/* Footer */
#footer {
display: flex;
justify-content: space-between;
}
```

Finally add some padding to the footer:

{caption: "style.css", line-numbers: false}
```
/* Footer */
#footer {
padding: 1rem 1rem 2rem 1rem;
display: flex;
justify-content: space-between;
}
```

Save `style.css` and refresh the browser. There should now be a footer at the bottom of the page:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-24-1024x220.png)

### Summing up

In this practical you added a header and a footer to the Energy Explorer.

You’re very nearly finished now. In the next practical you’ll add the final touches to the Energy Explorer.

## Practical: Final touches

You’re nearly finished! This practical adds a few finishing touches to the Energy Explorer.

The text style throughout the visualisation will be tweaked, hover effects will be added to links (such as the footer data source) and the popup content will be styled.

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-29-1024x427.png)

The steps are:

* **set the global text color** to a dark gray (instead of black)
* set **margin** and **padding** sizes
* add **hover over effects**
* set the **circle stroke style**
* style the **country labels**
* style the **popup content**

Once all of these changes are made the Energy Explorer will be finished!

### Set global text color

Up until now we haven’t set the default text color, so unless a color has been set on specific elements (such as menu items) they will appear black.

Let’s use a very dark gray as the default text color. In `style.css` set the `color` on the `body` element to `#333`:

{caption: "style.css", line-numbers: false}
```
/* Global */
body {
background-color: #FFFFF7;
font-family: 'Open Sans', sans-serif;
color: #333;
cursor: default;
}
...
```

### Set margin and padding sizes

Add a little bit of left and right padding to the controls section (`#controls`) so that the menu and legend sit more inside the visualisation:

{caption: "style.css", line-numbers: false}
```
/* Controls */
#controls {
padding: 0 1rem;
display: flex;
justify-content: space-between;
align-items: center;
}
```

(The padding value of `0 1rem` is a abbreviated way to set the top and bottom padding to zero and the left and right padding to `1rem`.)

Also set the `margin` on the `body` element to zero. This is something I always do so that there’s no additional space around the visualisation:

{caption: "style.css", line-numbers: false}
```
/* Global */
body {
background-color: #FFFFF7;
font-family: 'Open Sans', sans-serif;
color: #333;
margin: 0;
cursor: default;
}
```

### Add hover effects

Currently the Energy Explorer only has a couple of effects when items are hovered over:

* when a menu item is hovered it becomes darker
* when a country is hovered the popup appears

Hover effects are relatively straightforward to add and they are an effective way to add some final polish to a visualisation.

Let’s start by adding a hover effect to the links (represented by `a` elements) in the footer.

First set the color of all `a` elements:

{caption: "style.css", line-numbers: false}
```
/* Global */
body { ... }
#wrapper { ... }
a {
color: #777;
}
/* Header */
...
```

Next add a rule to set the `opacity` of `a` elements to 0.5 if they are hovered:

{caption: "style.css", line-numbers: false}
```
/* Global */
body { ... }
#wrapper { ... }
a {
color: #777;
}
a:hover {
opacity: 0.5
}
/* Header */
...
```

And then add a CSS transition so that the opacity gradually changes:

{caption: "style.css", line-numbers: false}
```
a {
color: #777;
transition: opacity 0.3s;
}
```

The `transition` property lets you define how quickly a CSS property animates when it changes.

Add a similar effect to the menu items:

{caption: "style.css", line-numbers: false}
```
/* Menu */
...
.menu .item .label {
font-size: 1.2rem;
padding-left: 0.2rem;
opacity: 0.5;
transition: opacity 0.3s;
}
```

### Set circle stroke style

This is a very subtle change. The hydroelectric and nuclear circles use a dashed stroke style:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-25.png)

I think it’s a tiny bit too thin, so let’s make their strokes a tiny bit thicker:

{caption: "style.css", line-numbers: false}
```
circle.hydroelectric {
fill: none;
stroke: #5EB1BF;
stroke-width: 1.25;
stroke-dasharray: 3,1;
}
circle.nuclear {
fill: none;
stroke: #F6AE2D;
stroke-width: 1.25;
stroke-dasharray: 4,2;
}
```

### Style country labels

Currently the country labels are quite dominant so let’s make them a bit smaller and a bit lighter in color.

Let’s also set their font weight to semi bold (which equates to a weight of 600). This adds a bit of typology contrast (if there is such a thing!) to the visualisation:

{caption: "style.css", line-numbers: false}
```
/* Chart */
.country { ... }
.country .label {
text-anchor: middle;
font-size: 0.75rem;
fill: #999;
font-weight: 600;
}
...
```

Save `style.css` and refresh your browser. The country labels should look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-26.png)

Let’s also add a hover effect to the countries:

{caption: "style.css", line-numbers: false}
```
/* Chart */
.country { ... }
.country .label {
text-anchor: middle;
font-size: 0.75rem;
fill: #999;
font-weight: 600;
transition: fill 0.3s;
}
.country:hover .label {
fill: #333;
}
```

The label color changes to `#333` if the country group is hovered over. The country group consists of the four circles and the label, so if any of the four circles or the label are hovered, the label will change color.

You also added a CSS `transition` to the country label `fill` so that it changes color gradually.

Save `style.css` and refresh the browser. When you hover over a country, the label should gradually darken:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-27.png)

### Style popup content

The final step is to style the content of the popup.

You’ll:

* set the text color to a dark gray
* make the text smaller
* add a margin
* center the heading

The Flourish popup adds a few class attributes to the popup which you can use to style particular elements in the popup.

The actual content of the popup (the heading and the list of values) has a class attribute of `flourish-popup-content`.

Start by setting the `font-size` and `color` of the popup content:

{caption: "style.css", line-numbers: false}
```
/* Popup */
.flourish-popup { ... }
.flourish-popup-content {
font-size: 0.8rem;
color: #333;
}
```

The heading (which contains the country name) is inside an `h3` tag which you added in the [practical that added the popup](https://learn.createwithdata.com/books/d3-start-to-finish/sections/add-a-popup/).

Center the heading (using `text-align`) and add top and bottom margins:

{caption: "style.css", line-numbers: false}
```
/* Popup */
.flourish-popup { ... }
.flourish-popup-content { ... }
.flourish-popup-content h3 {
text-align: center;
margin: 0.25rem 0;
}
```

Save `style.css` and refresh the browser. Hover over a country and popup should now look like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-28.png)

### Summing up

In this practical you made several changes to the CSS.

The text colors were adjusted in order to improve the tonal balance of the visualisation. Hover over effects were added, including adding a CSS transition. The transition softens the hover over effect and brings a degree of sophistication to the visualisation.

Finally the popup content was styled to match the rest of the visualisation.

You’re now all done! It’s been a long journey and hopefully you were able to follow most of the process.

In the next section we’ll recap the book and discuss further steps you might like to take.