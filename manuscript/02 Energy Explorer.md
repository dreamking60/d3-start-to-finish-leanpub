# Energy Explorer

This chapter gives a technical overview of Energy Explorer and an overview of the build steps.

## Technical overview

This section gives a broad overview of the energy explorer from a technical perspective. There’s no need to understand this section fully, it’s just here to give you a flavour of what you’ll be building.

The energy explorer is built using HTML, SVG, CSS and JavaScript. It uses a few JavaScript libraries namely D3, Lodash and Flourish’s popup library.

The data originates from the [World Bank’s World Development Indicators](http://datatopics.worldbank.org/world-development-indicators/) database and has been transformed into a comma separated value (CSV) file using a Node script. The CSV file looks like:

{caption: 'data.csv'}
```text
id,name,oilgascoal,nuclear,hydroelectric,renewable
AGO,Angola,46.8,,53.2,0.0
ALB,Albania,0.0,,100.0,0.0
ARE,United Arab Emirates,99.8,,0.0,0.2
ARG,Argentina,66.9,,26.2,1.9
ARM,Armenia,35.9,,28.3,0.1
...
```

This CSV file is loaded into Energy Explorer using D3. We use D3 selections and joins to create a group of four circles that represent each country. A text label is also created for each country.

A mouse over event handler is added to each country. This triggers a popup containing additional information.

At the top of the visualisation is a menu which allows the user to choose how the countries are sorted.

![Energy Explorer's sort menu](a823c0dc944cbc3bfcc58b36ce54d99a.png)

The menu is created using D3. When a menu item is clicked the data is sorted and D3’s transitions are used to animate the countries into their new positions.

The application is split into a few modules. One for the main application and further modules for computing the circle positions, for adding a popup and for adding a menu.

The final file structure looks like:

```text
css
└── style.css
data
└── data.csv
index.html
js
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

This is a typical web application structure. It’s a static application, meaning there’s no server-side code. All files are loaded into the browser when the visualisation first loads.

(In order to stay focused, this book doesn’t use any build tools such as Webpack.)

The application consists of a single HTML file (`index.html`). This contains a handful of HTML elements that act as containers for the chart.

The main JavaScript file is  `main.js` and this loads the data from `data/data.csv`. It transforms the data into position and radius information using a function in `layout.js`. The chart is drawn in `update.js`.

`menu.js` creates the sort menu and `popup.js` manages the popup. `store.js` helps with state management.

D3, Lodash and Flourish’s popup library live in the `lib` directory.

## Overview of the Energy Explorer build

The build of the Energy Explorer is divided into **fifteen steps**. Each step focuses on a single aspect. For example, loading the data or adding transitions.

The code download includes the **before** and **after** code for each of the fifteen steps. Therefore if you don’t complete a particular step, you can still work on the next one.

Typically in each practical session you’ll **load** the step’s code into your editor, **edit** the code and **view** the result in your web browser. The steps are:

| **Step** | **Description** |
| --- | --- |
| 1   | Getting started |
| 2   | Load the data |
| 3   | Draw the data (you’ll represent each country with a circle) |
| 4   | Size the circles |
| 5   | Add layout module |
| 6   | Arrange the circles in a grid |
| 7   | Add country name labels (using a nested join) |
| 8   | Add extra circles (for representing each energy type) |
| 9   | Basic styling |
| 10  | Add a popup |
| 11  | Add state management and a menu |
| 12  | Add functionality to sort the countries |
| 13  | Add transitions |
| 14  | Add a legend |
| 15  | Finishing touches |