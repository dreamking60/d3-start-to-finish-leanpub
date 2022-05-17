# Energy Explorer

## Technical overview

This section gives a broad overview of the energy explorer from a technical perspective. There’s no need to understand this section fully, it’s just here to give you a flavour of what you’ll be building.

The energy explorer is built using HTML, SVG, CSS and JavaScript. It uses a few JavaScript libraries namely D3, Lodash and Flourish’s popup library.

The data originates from the [World Bank’s World Development Indicators](http://datatopics.worldbank.org/world-development-indicators/) database and has been transformed into a comma separated value (CSV) file using a Node script. The CSV file looks like:

```
id,name,oilgascoal,nuclear,hydroelectric,renewable
AGO,Angola,46.8,,53.2,0.0
ALB,Albania,0.0,,100.0,0.0
ARE,United Arab Emirates,99.8,,0.0,0.2
ARG,Argentina,66.9,,26.2,1.9
ARM,Armenia,35.9,,28.3,0.1
...
```

The CSV file is loaded into the application using D3. We then use D3 selections and joins to create a group of four circles that represent each country. A text label is also created for each country.

A mouse over event handler is added to each country. This triggers a popup containing additional information.

At the top of the visualisation is a menu which allows the user to choose how the countries are sorted.

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-32.png)

The menu is also created using D3. When a menu item is clicked the data is sorted and D3’s transitions are used to animate the countries into their new positions.

The application is split into a few modules. One for the main application and further modules for computing the circle positions, for adding a popup and for adding a menu.

The final file structure looks like:

```
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

The main JavaScript file is named `main.js` and this loads the data from `data/data.json`. It then transforms the data into position and radius information using a layout function found in `layout.js`. The chart is then drawn in `update.js`.

The `menu.js` file creates the sort menu and `popup.js` manages the popup. `store.js` helps with state management.

D3, Lodash and Flourish’s popup library are found in the `lib` directory.

## Overview of the Energy Explorer build

The build of the Energy Explorer has been divided into **fifteen steps**.

Typically each step will focus on a single aspect, for example, loading the data or adding transitions.

This book includes the **before** and **after** code for each of the fifteen steps. Therefore if you don’t complete a particular step, you can still work on the next one.

Typically in each practical session you’ll **load** the step’s code into your editor, **edit** the code and **view** the result in your web browser.

The steps are:

<table class=""><tbody><tr><td><strong>Step</strong></td><td><strong>Description</strong></td></tr><tr><td>1</td><td>Getting started</td></tr><tr><td>2</td><td>Load the data</td></tr><tr><td>3</td><td>Draw the data (you’ll represent each country with a circle)</td></tr><tr><td>4</td><td>Size the circles</td></tr><tr><td>5</td><td>Add layout module</td></tr><tr><td>6</td><td>Arrange the circles in a grid</td></tr><tr><td>7</td><td>Add country name labels (using a nested join)</td></tr><tr><td>8</td><td>Add extra circles (for representing each energy type)</td></tr><tr><td>9</td><td>Basic styling</td></tr><tr><td>10</td><td>Add a popup</td></tr><tr><td>11</td><td>Add state management and a menu</td></tr><tr><td>12</td><td>Add functionality to sort the countries</td></tr><tr><td>13</td><td>Add transitions</td></tr><tr><td>14</td><td>Add a legend</td></tr><tr><td>15</td><td>Finishing touches</td></tr></tbody></table>