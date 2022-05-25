# Practical: Add a menu

In this practical we add a **menu** to Energy Explorer that lets the user select which indicator to sort the countries by:

{width: 75%}
![Energy Explorer menu](2c402e21e967d37bbca528118e1a2661.png)

The menu consists of 5 items: 'Country', 'Renewable', 'Oil, Gas & Coal', 'Hydroelectric' and 'Nuclear'. In this practical we add a property to the state object that stores which indicator has been selected. When a menu item is clicked we update the state accordingly. We'll implement the sorting in a later practical.

## Overview

A new module named `menu.js` will be added which contains an array of menu items:

```js
[
  {
    id: 'country',
    label: 'Country'
  },
  {
    id: 'renewable',
    label: 'Renewable'
  },
  ...
]
```

We use D3 to join the menu item array to `div` elements. The resulting HTML will look something like:

```html
<div class="menu">
  <div class="items">
    <div class="item">Country</div>
    <div class="item">Renewable</div>
    <div class="item">Oil, Gas & Coal</div>
    <div class="item">Hydroelectric</div>
    <div class="item">Nuclear</div>
  </div>
</div>
```

When a menu item is clicked a new state property `selectedIndicator` is updated and the chart and menu are redrawn.

Open step11. The file structure is:

```text
step11
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
    │   └── popup-v1.1.1.min.js
    ├── main.js
    ├── popup.js
    ├── store.js
    └── update.js
```

`js/store.js` was added in the previous practical.

In this practical we:

1. Add a container for the menu in `index.html`.
2. Add a new property `selectedIndicator` to the state object.
3. Add a new module `js/menu.js`.
4. Add code to construct and manage the menu.
5. Add basic styling to the menu.
6. Modify the update function so that it also updates the menu.


## Add a container for the menu

In `index.html` we make the following changes:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
    <link rel="stylesheet" href="css/style.css">
  </head>

  <body>
    <div id="wrapper">
markua-start-insert
      <div id="controls">
        <div class="menu">
          <div class="items"></div>
        </div>
      </div>
      <div id="chart-wrapper">
markua-end-insert
        <svg width="1200" height="1200">
          <g id="chart"></g>
        </svg>
markua-start-insert
      </div>
markua-end-insert
    </div>

    <script src="js/lib/d3.min.js"></script>
    <script src="js/lib/popup-v1.1.1.min.js"></script>

    <script src="js/config.js"></script>
    <script src="js/store.js"></script>
    <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
    <script src="js/popup.js"></script>
    <script src="js/main.js"></script>
  </body>
</html>
```

We add two new `<div>` elements to the main wrapper `<div id="wrapper">`. The first (with `id="controls"`) acts as a container for Energy Explorer's controls (including the menu). The second `<div>` (with `id="chart-wrapper"`) acts as a container for the chart's `<svg>` element.

## Add selectedIndicator state property

In `js/store.js` add a new property `selectedIndicator` which indicates which energy indicator the user has selected. We also add a new action type `setSelectedIndicator` which updates `state.selectedIndicator`.

{caption: "js/store.js"}
```js
let state = {
markua-start-insert
    selectedIndicator: 'country'
markua-end-insert
};

function action(type, param) {
    switch(type) {
markua-start-insert
        case 'setSelectedIndicator':
            state.selectedIndicator = param;
            break;
markua-end-insert
    }

    update();
}
```

## Add a new module for the menu

Create a new file within the `js` directory and name it `menu.js`. Your directory structure should now look like:

```text
step11
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
    │   └── popup-v1.1.1.min.js
    ├── main.js
markua-start-insert
    ├── menu.js
markua-end-insert
    ├── popup.js
    ├── store.js
    └── update.js
```

In `index.html` link to the new file:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
  ...
  </head>

  <body>
    ...

    <script src="js/lib/d3.min.js"></script>
    <script src="js/lib/popup-v1.1.1.min.js"></script>

    <script src="js/config.js"></script>
    <script src="js/store.js"></script>
    <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
    <script src="js/popup.js"></script>
markua-start-insert
    <script src="js/menu.js"></script>
markua-end-insert
    <script src="js/main.js"></script>
  </body>
</html>
```

Now save `menu.js` and `index.html`.


## Add code to construct and manage the menu

In `menu.js` we add code to create and update the menu items using a D3 join. We also add code to handle menu item clicks:

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
        .text(function(d) {
            return d.label;
        })
        .on('click', handleMenuClick);
}
```

`menuItems` is an array containing objects for each menu item. Each object consists of an id and label. `updateMenu` uses D3 to join `menuItems` to `<div>` elements. You might be surprised to see D3 used in this way - D3 can be used to join arrays to HTML elements, not just SVG elements! This code is standard join code, as covered in the Data Joins chapter. We also register an event handler `handleMenuClick` for click events.

When a menu item is clicked, `handleMenuClick` is called. This calls `action` which updates `state.selectedIndicator` and calls `update`. We'll modify `update` later on to call `updateMenu`. When `updateMenu` is called, it'll add a class attribute `selected` to the selected menu item. This'll allow us to style the selected menu item accordingly.

## Basic styling of the menu

We add some CSS rules to `css/style.css` for styling the menu:

{caption: "css/style.css"}
```css
body {
    background-color: #FFFFF7;
markua-start-insert
    cursor: default;
markua-end-insert
}

#wrapper {
    width: 1200px;
    margin: 0 auto;
}

markua-start-insert
.menu .items {
    display: flex;
}

.menu .item {
    padding: 0 1rem;
    color: #333;
    opacity: 0.5;
}

.menu .item.selected, .menu .item:hover {
    opacity: 1;
}
markua-end-insert

circle.renewable {
    fill: #7FB069;
}

...
```

We set `cursor` to `default` on the `body` element so that the cursor pointer always displays as an arrow. (Without this, the text select pointer is displayed when menu items are hovered.)

The menu item container (selected by `.menu .items`) is given a `display` of `flex` which arranges the menu items in a row. (You can find out more about Flexbox in the [Fundamentals of HTML, SVG, CSS & JavaScript for Data Visualisation](fundamentalsbook) book.) Each menu item is given some horizontal padding and given an opacity of 0.5. If a menu item is hovered or selected it's given an opacity of 1.


## Add menu to update function

Finally we need to make sure the menu is redrawn each time the state changes. In `js/update.js` we create two new functions `updateChart` and `updateMenu`, both of which are called from a rewritten `update` function. `updateChart` is a new function that replaces the old `update` function:

{caption: "js/update.js"}
```js
function initialiseGroup(g) {
    ...
}

function updateGroup(d, i) {
    ...
}

markua-start-insert
function updateChart() {
    let layoutData = layout(data);

    d3.select('#chart')
        .selectAll('g')
        .data(layoutData)
        .join('g')
        .each(updateGroup);
}

function update() {
    updateChart();
    updateMenu();
}
markua-end-insert
```

Whenever `update` is called, the chart and menu are updated. For example, when the `setSelectedIndicator` action is invoked the chart and menu are both updated.

Now make sure `index.html`, `css/style.css`, `js/store.js`, `js/update.js` and `js/menu.js` are saved and view Step 11 in your browser. There should now be a menu at the top. When you click an item it should highlight in a darker gray:

{width: 75%}
![Energy Explorer menu with Oil, Gas & Coal selected](a1a6b29666e04b7ebefd9784489c67d8.png)

The chart itself won’t change when a menu item is clicked – that’ll be added in an upcoming practical.

### Summing up

In this practical you added a menu to Energy Explorer. When a menu item is clicked the `handleMenuClick` event handler (in `js/menu.js`) is called. This event handler calls `action` and passes in an action type of `setSelectedIndicator` and an action parameter of the menu item’s id (e.g. `'renewable'`). The `action` function updates `state.selectedIndicator` and calls `update` which causes the chart and menu to be redrawn.

Currently only the menu responds to `state.selectedIndicator` changing. However in an upcoming practical you’ll add code so that the circles are sorted according to the selected indicator. Another thing to note is that the menu was created using D3. This shows that D3 is actually a general purpose library that can create simple user interfaces, as well as data visualisations.

For simple interfaces such as this menu, D3 is fine to use. However I’d recommend a library such as React or Vue for more complicated interfaces.