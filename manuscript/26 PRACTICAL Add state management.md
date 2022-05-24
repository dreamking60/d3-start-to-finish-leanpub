# Practical: Add state management

In this section you’ll add state management to the Energy Explorer. As described in the previous section you’ll add a **state object** (that represents the current state) and an **action function** (for managing state changes).

The state object will consist of a single property `selectedIndicator` which specifies the indicator to sort the circles by.

In the next practical you’ll add a menu that will set `selectedIndicator`:

![](http://learn.createwithdata.com/wp-content/uploads/2020/08/image-32.png)

`selectedIndicator` can be set to one of five values:

* `'country'`
* `'renewable'`
* `'oilgascoal'`
* `'hydroelectric'`
* `'nuclear'`

If it’s set to `'country'` the circles will be sorted **alphabetically** by country name. Otherwise the circles will be sorted according to the **chosen indicator**.

The circle sorting will be added in an upcoming practical. This practical just adds the state management code.

The steps are:

* add a **new module** named `store.js`
* add an **object** named `state` for storing application state
* add a **function** `action` that updates the `state` object and calls `update`
* add the `data` array to `state`

In your code editor open the `step11` directory of the `d3-start-to-finish` code.

### Add a new module

Create a new file within the `js` directory and name it `store.js`. (It’s named `store.js` because store is the name commonly used in the Flux pattern.)

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
├── popup.js
├── store.js
└── update.js
```

Include the new file `store.js` in the application by adding a new `<script>` tag to `index.html`:

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
<script src="js/main.js"></script>
</body>
</html>
```

Now save `store.js` and `index.html`.

### Add a state object

Open `store.js` and add a state object that contains a single property `selectedIndicator` and initialize it to `'country'`:

{caption: "store.js", line-numbers: false}
```
let state = {
selectedIndicator: 'country'
};
```

### Add an action function

Now add an action function which handles a single action type `setSelectedIndicator`:

{caption: "store.js", line-numbers: false}
```
let state = {
selectedIndicator: 'country'
};
function action(type, param) {
switch(type) {
case 'setSelectedIndicator':
state.selectedIndicator = param;
break;
}
update();
}
```

### Add data to state

The data array (`data`) can also be thought of as state. (This makes sense because the `data` array describes what the Energy Explorer needs to display.)

Therefore let’s move `data` to the `state` object. Start by removing its declaration and initialisation from `main.js`:

{caption: "main.js", line-numbers: false}
```
// Remove: let data;
function dataIsReady(csv) {
// Remove: data = csv;
update();
}
...
```

In `store.js` add a `data` property to the `state` object and add a new action `setData` to the `action` function:

{caption: "store.js", line-numbers: false}
```
let state = {
data: null,
selectedIndicator: 'country'
};
function action(type, param) {
switch(type) {
case 'setData':
state.data = param;
break;
case 'setSelectedIndicator':
state.selectedIndicator = param;
break;
}
update();
}
```

Finally call `action` in `dataIsReady` (`main.js`). Also remove the `update()` call (because it’ll get called by `action`):

{caption: "main.js", line-numbers: false}
```
function dataIsReady(csv) {
action('setData', csv);
// Remove: update();
}
```

You’ll also need to change `data` to `state.data` in the `update` function (in `update.js`):

{caption: "update.js", line-numbers: false}
```
function update() {
let layoutData = layout(state.data);
d3.select('#chart')
.selectAll('g')
.data(layoutData)
.join('g')
.each(updateGroup);
}
```

Save `update.js`, `store.js` and `main.js`, refresh your browser (making sure it’s pointing at `step11`) and check Energy Explorer is still working.

### Summing up

In this practical you added a new module `store.js` which contains the `state` object and an action function.

In the next practical you’ll add the menu that lets the user select an indicator to sort the circles by. When a menu item is clicked the `action` function will be called and this will update the state accordingly.