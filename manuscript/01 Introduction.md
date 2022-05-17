# Introduction

Welcome! First of all, I’d like to thank you for buying this book. The aim is to teach you how to build a real-world interactive data visualisation using D3 called **Energy Explorer** and for you to learn about D3 along the way.

![Energy Explorer](efcd4cbf87a2c746360581deb9c38f1b.png)

This book uses a mix of **theory** and **practice**. You’ll learn an aspect of D3 then put it into practice during the build of Energy Explorer. The book is divided into several chapters. Some contain theoretical information and others contain practical steps to build Energy Explorer. Each practical chapter has the name 'Practical' in its title, for example ‘Practical: What You’ll Need’.

## Prerequisites

To get the most out of this book I recommend that you’re experienced enough to build a simple front-end web application.

Specifically you need to be familiar with:

* HTML
* SVG
* CSS
* JavaScript

You should also be comfortable opening and editing code in a code editor (such as VS Code, Atom or Brackets) and setting up a local web server.

If you need to get up to speed on the above languages and tools I suggest reading my [HTML, SVG, CSS & JavaScript for Data Visualisation](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/) book. If you understand everything in that book you'll be ready for this one.

## What you’ll learn

This book aims to teach you enough D3 to build a **custom data visualisation** from the ground up. Broadly speaking we’ll cover:

* **D3 selections and joins** (these are used to update HTML and SVG elements in a data-driven fashion).
* **D3 modules** (such as scale functions and transitions).
* **Third party libraries** such as Lodash that are useful when building data visualisations.
* General **web development patterns** that are helpful when building interactive data visualisations.

### D3 selections and joins

D3 has a powerful mechanism for adding, removing and updating HTML/SVG elements in a data-driven fashion known as selections and joins. You’ll learn how to use **selections** to select and update HTML/SVG elements. You’ll also learn how to update the elements in a data-driven fashion using D3 **joins**. For example, given an array of data, you’ll learn how to keep a group of SVG circles synchronised with the data.

### D3 modules

D3 has a large number of modules that help you build data visualisations. We cover the following D3 modules (and concepts) in this book:

| Name | Description |
| --- | --- |
| **Scale functions** | Scale functions transform data values (e.g. percentages) into visual values (e.g. position, size or colours) |
| **Data requests** | Data requests let you load CSV and JSON files |
| **Event handling** | Event handling lets you respond to clicks and hovers from the user |
| **Transitions** | Transitions let you animate the position, size and color of HTML/SVG elements |

### Third party libraries

We look at a couple of JavaScript libraries in this book: Flourish’s [popup component](https://github.com/kiln/flourish-popup) and [Lodash](https://lodash.com/).

Flourish’s popup library helps you add an information popup to a web page and Lodash provides functions for processing data (such as sorting and filtering).

{width: 25%}
![Flourish’s information popup (appears when an item is hovered)](63892c2867de54d6b5c62a8aa13ae64b.png)


### Web development patterns

We also cover web development techniques that are useful when building interactive data visualisations:

| Name | Description |
| --- | --- |
| **Modules** | Split your code up into separate modules rather than having all your code in a single file. |
| **State management** | Implement a state management pattern so that user interaction can be handled in a clean and easy to understand manner. |
| **Data manipulation** | Use the lodash library to process (sort, filter etc.) data. |

The above techniques will be used during the build of Energy Explorer.

## What you’ll build

In this book we’ll build an interactive data visualisation called **Energy Explorer** which shows the energy mix of 141 countries. You can visit a live version at [https://d3-start-to-finish-energy-explorer.surge.sh/](https://d3-start-to-finish-energy-explorer.surge.sh/).

Each country is represented by 4 circles and each circle shows the amount of **renewable**, **fossil fuel**, **hydroelectric** and **nuclear** energy produced by a country.

![Energy Explorer](1550e6d5d7d6eac7e9535976ed8cef10.png)

The chart is interactive. When a country is hovered a popup appears showing detailed information.

![Information popup when a country is hovered](46264c633143dfe564920d04128200f6.png)

The countries can be sorted by name or one of the energy types. When the circles sort they animate into their new positions. The following image shows the view when Renewable has been selected. The countries are sorted by the percentage of renewable energy in their energy mix:

![Countries sorted by renewable energy](31da3d4cde47ee1d75afd37a837576ed.png)

The visualisation is styled to look clean, modern and fresh. Colors, fonts, line widths, spacing etc. have been carefully selected to make the chart attractive and easy to digest.

Energy Explorer will be built using **HTML**, **SVG**, **CSS**, **JavaScript** and **D3**. HTML, SVG, CSS and JavaScript are standard technologies used to build websites and web applications. Practically every website (or web application) you visit is built from these four technologies. D3 is a JavaScript library that helps you build interactive data visualisations. It offers advanced functionality such as adding, removing and updating DOM elements, transforming data into shapes and animating between application states.

This book requires that you’re reasonably proficient in HTML, SVG, CSS and JavaScript. If you think your knowledge of these technologies needs a boost I  recommend reading my [HTML, SVG, CSS & JavaScript for Data Visualisation](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/) book.