# Practical: Add a menu

In this practical you’ll add the **menu** that appears at the top of the energy explorer:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-14.png)

The menu consists of 5 items:

* Country
* Renewable
* Oil, Gas & Coal
* Hydroelectric
* Nuclear

When an item is clicked the circles will be sorted in **alphabetical order** (if Country is selected) or in **descending order of the selected indicator**. (The sorting will be implemented in an upcoming practical.)

You’ll only add basic styling to the menu in this practical, so it’ll look like:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-15.png)

The steps are:

* add a **container** for the menu in `index.html`
* add a **new module** named `menu.js`
* add code to **create and manage** the menu
* **style** the menu
* **modify** the `update` function in `update.js` so that it updates the menu

The menu itself will be created using D3. It’s not the only way of creating a menu (using a library such as React or Vue springs to mind) but to avoid introducing another library it’ll be created using D3.

If you’ve just completed the previous practical you’ll have `step11` already open. Otherwise in your code editor open the `step11` directory of the `d3-start-to-finish` code and make sure you’ve completed the previous practical.

### Overview of how the menu works

A new module named `menu.js` will be added which contains an array of menu items and two functions `handleMenuClick` and `updateMenu`.

The array of menu items looks something like:

```
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

`updateMenu` is an update function which uses D3 to join the menu item array to `div` elements. The resulting HTML will look something like:

```
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

When a menu item is clicked, a new event handler `handleMenuClick` is called. `handleMenuClick` calls `action` (which you added in the previous practical) passing in `'setSelectedIndicator'` as the action type and the menu item id (e.g. `'country'`) as the action parameter. For example:

```
action('setSelectedIndicator', 'country');
```

The `action` function in `store.js` updates the state object and calls `update` (in `update.js`). You’ll modify `update` so that it calls `updateMenu` **and** `updateChart` (which updates the chart).

`updateMenu` also sets a class attribute of `selected` on the selected menu item. You’ll set some CSS rules such that the selected menu item is highlighted.

### Add menu container

In `index.html` wrap the `svg` element in a new `div` element. Give the `div` element a class attribute of `chart-wrapper`:

{caption: "index.html", line-numbers: false}
```
    ...
<div id="wrapper">
<div id="chart-wrapper">
<svg width="1200" height="1200">
<g id="chart"></g>
</svg>
</div>
</div>
...
```

Now add a section to contain the menu and legend. This’ll come just before the chart wrapper and have an id of `controls`:

{caption: "index.html", line-numbers: false}
```
    ...
<div id="wrapper">
<div id="controls">
</div>
<div id="chart-wrapper">
<svg width="1200" height="1200">
<g id="chart"></g>
</svg>
</div>
</div>
...
```

Now add a `div` element for the menu and another `div` element for the menu items:

{caption: "index.html", line-numbers: false}
```
    ...
<div id="wrapper">
<div id="controls">
<div class="menu">
<div class="items"></div>
</div>
</div>
<div id="chart-wrapper">
<svg width="1200" height="1200">
<g id="chart"></g>
</svg>
</div>
</div>
...
```

Now save `index.html`.

### Add a new module for the menu

Create a new file within the `js` directory and name it `menu.js`.

Your directory structure should now look like:

```
step11
├── css
│   └── style.css
├── data
│   └── data.json
├── index.html
└── js
├── config.js
├── layout.js
├── lib
│   ├── d3.min.js
│   └── popup-v1.1.1.min.js
├── main.js
├── menu.js
├── popup.js
├── store.js
└── update.js
```

Include the new file `menu.js` in the application by adding a new `<script>` tag to `index.html`:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
...
<body>
...
<script src="js/lib/d3.min.js"></script>
<script src="js/config.js"></script>
<script src="js/store.js"></script>
<script src="js/layout.js"></script>
<script src="js/update.js"></script>
<script src="js/popup.js"></script>
<script src="js/menu.js"></script>
<script src="js/main.js"></script>
</body>
</html>
```

Now save `menu.js` and `index.html`.

### Add menu code

The `menu.js` module will contain code to:

* **create** and **update** the menu items using a D3 join
* **handle** menu item clicks

You’ll use D3 to create the menu. (This is a good example of where D3 can be used to create standard HTML elements.)

An array specifying the menu items will be created and this will be joined to `div` elements.

The selected menu item will have a class attribute of `selected` (so that it can be styled appropriately). An event handler `handleMenuClick` will be attached to each menu item.

Start by adding an array to `menu.js` that specifies the menu items:

{caption: "menu.js", line-numbers: false}
```
var menuItems = [
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
```

Each menu item is represented by an object that has two properties `id` and `label`. The `id` property uniquely identifies each menu item and is used when setting `state.selectedIndicator`.

Now add a new function named `updateMenu` which joins the `menuItems` array to `div` elements:

{caption: "menu.js", line-numbers: false}
```
var menuItems = [...];
function updateMenu() {
d3.select('#controls .menu .items')
.selectAll('.item')
.data(menuItems)
.join('div')
.classed('item', true)
.text(function(d) {
return d.label;
});
}
```

> Notice that the join code for the menu is fundamentally the same as what you learned in the [D3 joins section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/how-to-join/).

Now attach an event handler named `handleMenuClick` to each menu item (you’ll define `handleMenuClick` later):

{caption: "menu.js", line-numbers: false}
```
function updateMenu() {
d3.select('#controls .menu .items')
.selectAll('.item')
.data(menuItems)
.join('div')
.classed('item', true)
.text(function(d) {
return d.label;
})
.on('click', handleMenuClick);
}
```

When a menu item is clicked, `handleMenuClick` is called and the joined value is passed in as the first parameter. (See the [Event Handling](https://learn.createwithdata.com/books/d3-start-to-finish/sections/d3-event-handling) section for a reminder.)

The joined value will be an element from `menuItems` so will look similar to:

```
{
id: 'renewable',
label: 'Renewable'
}
```

Now add the `handleMenuClick` function. `handleMenuClick`‘s second parameter `d` is a single menu item object that contains two properties `id` and `label` (see above).

`handleMenuClick` calls `action` with action type `'setSelectedIndicator'`. Pass `d.id` (which is the id of the clicked menu item) as the action parameter:

{caption: "menu.js", line-numbers: false}
```
var menuItems = [...];
function handleMenuClick(e, d) {
action('setSelectedIndicator', d.id);
}
function updateMenu() {...}
```

Finally add a call to the `.classed` method that sets a class attribute of `selected` if the menu item’s id matches `state.selectedIndicator`:

{caption: "menu.js", line-numbers: false}
```
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

Now save `menu.js`.

### Add style

In `css/style.css` add the following CSS to style the menu:

{caption: "style.css", line-numbers: false}
```
#wrapper { ... }
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
...
```

The `display: flex;` declaration causes the items to be displayed in a horizontal row.

Each item is given some horizontal padding, a color of `#333` and an opacity of 0.5.

If a menu item has a class attribute `selected` or it’s hovered, its opacity is set to 1.

Finally add the following declaration to the `body` rule which ensures the cursor keeps its default shape (an arrow) even when its hovering over text:

{caption: "style.css", line-numbers: false}
```
body {
background-color: #FFFFF7;
cursor: default;
}
```

Save `style.css` and refresh the browser.

### Modify update function

Finally you’ll modify the `update` function in `update.js` so that it updates both the chart and the menu.

Start by renaming `update` to `updateChart`:

{caption: "update.js", line-numbers: false}
```
function updateGroup(d, i) { ... }
function updateChart() {
var layoutData = layout(state.data);
d3.select('#chart')
.selectAll('g')
.data(layoutData)
.join('g')
.each(updateGroup);
}
```

Then add a new function named `update` which calls `updateChart` and `updateMenu`:

{caption: "update.js", line-numbers: false}
```
function updateChart() { ... }
function update() {
updateChart();
updateMenu();
}
```

Now save `update.js`.

Refresh the browser (making sure it’s loading `step11`) and you should see the menu at the top of the window. When you click an item it should highlight in a darker gray:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-16.png)

The chart itself won’t change when a menu item is clicked – that’ll be added in an upcoming practical.

The complete code for this practical is in `step11-complete`.

### Summing up

In this practical you added the sort menu to the energy explorer.

When a menu item is clicked the `handleMenuClick` event handler (in `menu.js`) is called. This event handler calls `action` and passes in an action type of `setSelectedIndicator` and an action parameter of the menu item’s id (e.g. `'renewable'`).

The `action` function updates `state.selectedIndicator` and calls `update` which causes the **chart and menu** to be redrawn.

Currently only the menu responds to `state.selectedIndicator` changing. However in an upcoming practical you’ll add code so that the circles are sorted according to the selected indicator.

Another thing to note is that the menu was created using D3. This shows that D3 is actually a general purpose library that can create user interfaces, as well as data visualizations.

For simple interfaces such as this menu, D3 is fine to use. However I’d recommend a library such as React or Vue for more complicated interfaces.