# Practical: Finishing Touches

In this final practical we add some finishing touches to Energy Explorer. The changes are cosmetic and take Energy Explorer from being functional, but not particularly pretty:

![Energy Explorer before finishing touches](1ff7f7ffaa0feb3ce962ffdbbb18919e.png)

to a more polished and professional looking data visualisation:

![The finished Energy Explorer](d62113848eae969ed48dc4bc51ccd8f1.png)

These are subtle changes but when added together they make a big difference.

## Overview

Open `step15`. The file structure is:

```text
step15
├── css
│   └── style.css
├── data
│   └── data.csv
├── index.html
└── js
    ├── config.js
    ├── layout.js
    ├── lib
    │   ├── d3.min.js
    │   ├── lodash.min.js
    │   └── popup-v1.1.1.min.js
    ├── main.js
    ├── menu.js
    ├── popup.js
    ├── store.js
    └── update.js
```

In this practical we:

1. Add and apply the Open Sans Google font.
2. Style the menu.
3. Add a header and footer.
4. Other CSS tweaks.

## Add and apply Open Sans font

Currently Energy Explorer is using the web browser’s **default font** for all text elements. This is usually a serif font such as Times or Times New Roman. In general sans-serif fonts (such as Helvetica) are recommended for data visualisation text because they tend to be clearer (they have less visual ‘noise’). Font support varies across platforms. For example, Helvetica is built into MacOS but not Windows. For this reason I tend to use Google Fonts as this ensures the same font is used regardless of operating system.

There’s a huge amount of resources and opinion surrounding font choice and unless you’re a trained designer it can be hard to know where to turn. I usually turn to Google Fonts and filter by Sans Serif and sort by most popular. Currently this returns Roboto, Open Sans and Lato as popular sans serif fonts. I usually try each of these and use my instinct to choose one. In the case of Energy Explorer I’ve chosen Open Sans. Feel free to choose a different font if you prefer.

In `index.html` we add the following line to the `<head>` element:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
markua-start-insert
    <link href="https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600&display=swap" rel="stylesheet">
markua-end-insert
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

If you see a continuation character `\` please ignore it (it's been added by the typesetting software). The entire `<link>` element should occupy a single line.

We then add a `font-family` declaration to the `body` rule in `css/style.css`:

{caption: "css/style.css", line-numbers: false}
```css
body {
    background-color: #FFFFF7;
markua-start-insert
    font-family: 'Open Sans', sans-serif;
markua-end-insert
    cursor: default;
}
```

This will result in Open Sans being used througout the visualisation. `sans-serif` is also added the the list of fonts as a fallback in case Open Sans doesn’t load.

Save `index.html` and `css/style.css` and load `step15` in the browser. The font has changed to Open Sans:

![Energy Explorer with Open Sans font](2075481779deb0b9da5e5c3953e88229.png)

## Style the menu

Before styling the menu we add the following headings to bring some order to `style.css`:

{caption: "css/style.css", line-numbers: false}
```css
/* Global */
body { ... }

#wrapper { ... }

/* Controls */
#controls { ... }

/* Menu */
.menu .items { ... }

.menu .item.selected, .menu .item:hover { ... }

/* Legend */
.legend circle { ... }

.legend text { ... }

/* Circles (menu and chart) */
circle.renewable { ... }

circle.oilgascoal { ... }

circle.hydroelectric { ... }

circle.nuclear { ... }

/* Chart */
.country { ... }

.country .label { ... }

/* Popup */
.popup-center { ... }

.flourish-popup { ... }
```

We'll now style the menu so that’ll it’ll look like:

{width: 66%}
![Styled menu](5fc7b78417f8b12db93b9c6aa0d4211f.png)

We need to add a circle to each indicator and add a 'Sort by' label. In `js/menu.js` we change how the menu items are created:

{caption: "js/menu.js"}
```js
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

markua-start-insert
function getCircle(id) {
    let svg = '<svg width="18" height="18"><circle class="'
        + id + '" cx="9" cy="9" r="8"></svg>';
    return svg;
}

function getHtml(d) {
    let circle = d.id === 'country' ? '' : getCircle(d.id);
    let label = '<div class="label">' + d.label + '</div>';
    return circle + label;
}
markua-end-insert

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
markua-start-delete
        .text(function(d) {
            return d.label;
        })
markua-end-delete
markua-start-insert
        .html(getHtml)
markua-end-insert
        .on('click', handleMenuClick);
}
```

We use the `.html` method instead of `.text` in `updateMenu` to update the content of each menu item using HTML instead of a text string. We pass in function `getHtml` which returns an HTML string consisting of an SVG circle and the label. Each circle is given the same class as its counterpart in the chart, so that it adopts the existing circle styles in `css/style.css`. (The 'Country' item in the menu doesn't have a circle.)

Save `js/menu.js` and refresh the browser. The menu now looks like:

![Menu with circles](e18dea52cd410dd36828018692e680e7.png)

In `index.html` we add a `Sort by:` heading to the menu:

{caption: "index.html"}
```html
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
markua-start-insert
          <div>Sort by:</div>
markua-end-insert
          <div class="items"></div>
        </div>
        <div class="legend">
          ...
        </div>
      </div>
      <div id="chart-wrapper">
        ...
      </div>
    </div>

    <script src="js/lib/d3.min.js"></script>
    ...
  </body>
</html>
```

We now make the following changes to the menu CSS:

{caption: "css/style.css"}
```css
/* Menu */
markua-start-insert
.menu {
    font-size: 0.9rem;
    display: flex;
    align-items: center;
}
markua-end-insert

.menu .items {
    display: flex;
}

.menu .item {
    padding: 0 1rem;
    color: #333;
markua-start-delete
    opacity: 0.5;
markua-end-delete
markua-start-insert
    display: flex;
    align-items: center;
markua-end-insert
}

markua-start-insert
.menu .item .label {
    font-size: 1.2rem;
    padding-left: 0.2rem;
    opacity: 0.5;
    transition: opacity 0.3s;
}
markua-end-insert

markua-start-delete
.menu .item.selected, .menu .item:hover {
markua-end-delete
markua-start-insert
.menu .item.selected .label, .menu .item:hover .label {
    opacity: 1;
}
markua-end-insert

.legend circle {
    stroke: #aaa;
    fill: none;
}

...
```

We've made the following changes:

- Changed menu font size to `0.9rem`.
- Set the menu's `display` to `flex` so that the 'Sort by:' label and menu items occupy the same row.
- Set the menu's `align-items` to `center` so that the label and menu are vertically aligned.
- Set each menu item's `display` to `flex` so that the circle and label occupy the same row.
- Set each menu item's `align-items` to `center` so that the each menu item's circle and label are vertically aligned.
- Set the font-size of each menu item to `1.2rem`.
- Give the menu labels an opacity of 0.5 (if not selected) or 1 (if selected).
- Add a subtle CSS transition to the label opacity.

Now the menu looks like:

{width: 66%}
![The styled menu](e8d0f2ce7b5ecb2c550ab52f6bb39357.png)

When an item is hovered, the label changes opacity but the circles always have an opacity of 1 (so that they look the same as the circles in the chart).

The menu is now complete. We added circles to the menu items indicating which energy type each circle style represents. There was quite a lot of detailed CSS to add. This is fairly typical when adding finishing touches to a data visualisation. It’s often a significant portion of the work and sometimes requires a lot of experimentation and iteration.

## Add header and footer

The header will contain a main title **Energy mix by country (2015)**:

![Main title](39f265fc7602bcfa057c4476ab9de31d.png)

and the footer will contain information on the data source and attribution information:

![Footer](307e652aedbc3da74282724210a03ff2.png)

In `index.html` we make the following changes:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    ...
  </head>

  <body>
    <div id="wrapper">
markua-start-insert
  	   <div id="header">Energy mix by country (2015)</div>
markua-end-insert
      <div id="controls">
        ...
      </div>
      <div id="chart-wrapper">
        ...
      </div>
markua-start-insert
      <div id="footer">
        <div>Data: <a href="https://datacatalog.worldbank.org/dataset/world-development-indicators">World Bank</a></div>
        <div><a href="https://learn.createwithdata.com">D3 Start to Finish</a></div>
      </div>
markua-end-insert
    </div>

    <script src="js/lib/d3.min.js"></script>
    ...
  </body>
</html>
```

We've added a `<div>` element for the header just before the controls. We've also added a `<div>` element for the footer.

We now add CSS for the header and footer:

{caption: "css/style.css"}
```css
/* Global */
...

markua-start-insert
/* Header */
#header {
    font-size: 1.5rem;
    letter-spacing: -0.03rem;
    padding: 1rem;
    text-align: right;
}
markua-end-insert

/* Controls */
...

/* Menu */
...

/* Legend */
...

/* Circles (menu and chart) */
...

/* Chart */
...

markua-start-insert
/* Footer */
#footer {
    padding: 1rem 1rem 2rem 1rem;
    display: flex;
    justify-content: space-between;
}
markua-end-insert

/* Popup */
...
```

The header’s `font-size` is set to `1.5rem`. We close up the letter spacing a bit by setting `letter-spacing` to `-0.03rem`. We also added some padding around the header and aligned the text to the right.

The footer’s `display` is set to `flex` so that the items are arranged horizontally. We set `justify-content` to `space-between` so that the items fill the width of the container (similarly to what was done with the controls section). We also add some padding to the footer.

Save `index.html` and `style.css` and refresh the browser. The header now looks like:

![Styled header](15d01fd993c9c61db197960042625f50.png)

and the footer looks like:

![Finished footer](025a785d55ad888a97a8758f91fedbb1.png)

## Other CSS tweaks

We’re nearly finished! We've a few final CSS tweaks to add a final layer of polish to Energy Explorer.

In `css/style.css` we make the following changes:

{caption: "css/style.css"}
```css
/* Global */
body {
    background-color: #FFFFF7;
    font-family: 'Open Sans', sans-serif;
markua-start-insert
    color: #333;
    margin: 0;
markua-end-insert
    cursor: default;
}

#wrapper {
    width: 1200px;
    margin: 0 auto;
}

markua-start-insert
a {
    color: #777;
    transition: opacity 0.3s;
}

a:hover {
    opacity: 0.5
}
markua-end-insert

/* Header */
...


/* Controls */
#controls {
markua-start-insert
    padding: 0 1rem;
markua-end-insert
    display: flex;
    justify-content: space-between;
    align-items: center;
}


/* Menu */
...

/* Legend */
...

/* Circles (menu and chart) */
circle.renewable {
    fill: #7FB069;
}

circle.oilgascoal {
    fill: none;
    stroke: #BCB5B2;
}

circle.hydroelectric {
    fill: none;
    stroke: #5EB1BF;
markua-start-insert
    stroke-width: 1.25;
markua-end-insert
    stroke-dasharray: 3,1;
}

circle.nuclear {
    fill: none;
    stroke: #F6AE2D;
markua-start-insert
    stroke-width: 1.25;
markua-end-insert
    stroke-dasharray: 4,2;
}


/* Chart */
.country {
    pointer-events: all;
}

.country .label {
    text-anchor: middle;
markua-start-insert
    font-size: 0.75rem;
    fill: #999;
    font-weight: 600;
    transition: fill 0.3s;
markua-end-insert
}

markua-start-insert
.country:hover .label {
    fill: #333;
}
markua-end-insert


/* Footer */
...


/* Popup */
.popup-center {
    opacity: 0;
}

.flourish-popup {
    pointer-events: none;
}

markua-start-insert
.flourish-popup-content {
    font-size: 0.8rem;
    color: #333;
}

.flourish-popup-content h3 {
    text-align: center;
    margin: 0.25rem 0;
}
markua-end-insert
```

The following changes were made:

* the global text colour has been set to a dark gray (instead of black)
* the `body` element's `margin` has been set to 0 (to remove the default margins around the whole page)
* the links in the footer are coloured grey and a subtle hover over effect added
* the controls container padding has been tweaked to bring the menu and legend in a bit
* the stroke width of hydroelectric and nuclear circles has been increased slightly
* the country labels in the main chart have been styled, including a hover over effect
* set the font size, colour, alignment and margin in the popup

Save `css/style.css` and the Energy Explorer is now complete! Refresh the browser and it looks like:

![The finished Energy Explorer](3c4f8737f5d8dbaa5da515246f1fe5a1.png)

The popup has also been styled and looks like:

{width: 25%}
![Styled popup](b90aec9888fc41f7638ded45f54f5fff.png)

## Summary

In this practical we made plenty of cosmetic changes to Energy Explorer, including: adding Open Sans font, adding a header and footer, styling the menu (including adding a circle to each item) and many other subtle styling changes. Each change is quite a small one, but they add up to make a big difference. Prior to the changes, Energy Explorer was functional but not particularly polished, but now it looks finished and professional.
