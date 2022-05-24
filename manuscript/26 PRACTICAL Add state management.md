# Practical: Add state management

In this section you’ll add state management to Energy Explorer in a similar manner to described in the State Management chapter. We’ll add a **state object** (that represents the current state) and an **action function** (for managing state changes).

## Overview

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
    └── update.js
```

In this practical we:

1. Add a new module `js/store.js`.
2. Add a new object `state` for storing state and a new function `action` that manages state changes.

### Add a new module

Create a new file within the `js` directory and name it `store.js`. (It’s named `store.js` because store is the name commonly used in the Flux pattern.)

Your directory structure should now look like:

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
markua-start-insert
    ├── store.js
markua-end-insert
    └── update.js
```

In `index.html` we link to the new file:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">

    ...
    <script src="js/lib/d3.min.js"></script>
    <script src="js/lib/popup-v1.1.1.min.js"></script>

    <script src="js/config.js"></script>
markua-start-insert
    <script src="js/store.js"></script>
markua-end-insert
    <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
    <script src="js/popup.js"></script>
    <script src="js/main.js"></script>
  </body>
</html>
```

Now save `store.js` and `index.html`.

### Add an empty state object and action function

In `js/store.js` we add a new empty state object:

{caption: "js/store.js", line-numbers: false}
```js
let state = {
};

function action(type, param) {
    switch(type) {
    }

    update();
}
```

We haven't added any properties to the state object (nor any associated actions). We'll do this in the next practical.

Save `index.html` and `store.js`, refresh your browser (making sure it’s loading `step11`) and check Energy Explorer is still working.