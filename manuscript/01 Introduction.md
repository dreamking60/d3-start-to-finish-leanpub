# Introduction

Welcome!

First of all, I’d like to thank you for deciding to buy this book. The aim of this book is to teach you how to build a real-world interactive data visualization using D3 called **Energy Explorer**.

![](http://learn.createwithdata.com/wp-content/uploads/2020/08/image-29-1024x427.png)

This book uses a mix of **theory** and **practice**. Typically you’ll learn a particular aspect of D3 then you’ll put that theory into practice during the build of the data visualization.

The book is divided into several sections and each section contains **sections** and **practicals**.

A **section** covers theoretical material and usually contains examples. Some examples are hosted on CodePen, in which case you’re encouraged to explore the example by modifying the code.

A **practical** takes you through the steps required to make the Energy Explorer. Each practical is marked ‘Practical’, for example ‘Practical: What You’ll Need’.

## Prerequisites

To get the most out of D3 Start to Finish I recommend that you’ve enough experience to be able to build a front-end web application.

Specifically you need to be familiar with:

* HTML
* SVG
* CSS
* JavaScript

You should also be comfortable opening and editing code in a code editor (such as Atom, VS Code or Sublime Text) and setting up a local web server.

If you need to get up to speed on the above languages and tools I suggest completing my free [HTML, SVG, CSS & JavaScript for Data Visualisation](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/) book. If you understand everything in the HTML, SVG, CSS & JavaScript for Data Visualisation book then you should be ready for D3 Start to Finish.

## What you’ll learn

This book aims to teach you enough D3 to build a **custom data visualization** from the ground up.

Broadly speaking we’ll cover:

* **D3 selections and joins** (these are used to update HTML and SVG elements in a data-driven fashion)
* **D3 modules** (such as scale functions and transitions)
* **third party libraries** such as Lodash that are useful when building data visualizations
* general **web development patterns** that are helpful when building interactive data visualizations

#### D3 selections and joins

D3 has a powerful mechanism for adding, removing and updating HTML/SVG elements in a data-driven fashion known as selections and joins.

You’ll learn how to use **selections** to select and update HTML/SVG elements. You’ll also learn how to update the elements in a data-driven fashion using D3 **joins**.

For example, given an array of data, you’ll learn how to keep a group of SVG circles in sync with the data.

#### D3 modules

D3 has a large number of modules that help you build data visualizations. We’ll cover the following D3 modules (and concepts) in this book:

<table class=""><tbody><tr><td><strong>Scale functions</strong></td><td>Scale functions transform data values (e.g. percentages) into visual values (e.g. position, size or colours)</td></tr><tr><td><strong>Data requests</strong></td><td>Data requests let you load CSV and JSON files</td></tr><tr><td><strong>Event handling</strong></td><td>Event handling lets you respond to clicks and hovers from the user</td></tr><tr><td><strong>Transitions</strong></td><td>Transitions let you animate the position, size and color of HTML/SVG elements</td></tr></tbody></table>

#### Third party libraries

We’ll look at a couple of JavaScript libraries in this book, namely Flourish’s [popup component](https://github.com/kiln/flourish-popup) and [Lodash](https://lodash.com/).

Flourish’s popup library helps you add an information popup to a web page and Lodash provides functions for processing data (such as sorting and filtering).

![](http://learn.createwithdata.com/wp-content/uploads/2020/07/image-38.png)

Flourish’s information popup (appears when an item is hovered)

#### Web development patterns

We’ll also cover some general web development topics which are useful when building interactive data visualizations:

<table class=""><tbody><tr><td><strong>Modules</strong></td><td>You’ll learn to split your code up into separate modules rather than having all your code in a single file</td></tr><tr><td><strong>State management</strong></td><td>You’ll learn to implement a state management pattern so that user interaction can be handled in a clean and easy to understand manner</td></tr><tr><td><strong>Data manipulation</strong></td><td>You’ll learn how the lodash library can be used to process (sort, filter etc.) data</td></tr></tbody></table>

All of the above will be put into practice by building an interactive data visualization. We’ll look at what you’ll be building in the next section.

## What you’ll build

In this book you’ll build an interactive data visualization called **Energy Explorer** which shows the energy mix of 141 countries.

You can visit a live version at [https://d3-start-to-finish-energy-explorer.surge.sh/](https://d3-start-to-finish-energy-explorer.surge.sh/).

Each country is represented by 4 circles. Each circle shows the amount of **renewable**, **fossil fuel**, **hydroelectric** and **nuclear** energy produced by a country.

![](https://lh6.googleusercontent.com/y-Vcm_h4Akcpsdh2u5Q3IjO9zgBZ_2Mmyc2w0tOBGiNF9pf-nXCpujdlZXGwe3P8jzEfnAhgCwS7kJQ_h0wRu8a-HMBOV0cOoBS8C5lMUwu8obZTiOSlDbyxDBjGKe1lzF3YfFrDDAY)

The chart is interactive. When a country is hovered a popup appears showing detailed information.

![](http://learn.createwithdata.com/wp-content/uploads/2020/08/image-29-1024x427.png)

The countries can be sorted by name or one of the energy types. When the circles sort they animate into their new positions.

The following image shows the view when Renewable has been selected. The countries are sorted by the percentage of renewable energy in their energy mix:

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-31-1024x380.png)

The chart is styled to look clean, modern and fresh. Colors, fonts, line widths, spacing etc. have been carefully selected to make the chart attractive and easy to digest.

## HTML, SVG, CSS, JavaScript and D3

The energy explorer will be built using **HTML**, **SVG**, **CSS**, **JavaScript** and **D3**.

HTML, SVG, CSS and JavaScript are standard technologies used to build websites and web applications. Practically every website (or web application) you visit is built from these four technologies.

D3 is a JavaScript library that helps you build interactive data visualizations. It offers advanced functionality such as adding, removing and updating DOM elements, transforming data into shapes and animating between application states.

This book expects that you’re reasonably proficient in HTML, SVG, CSS and JavaScript. If you feel your knowledge of these technologies needs a boost I strongly recommend taking my free [HTML, SVG, CSS & JavaScript for Data Visualisation book](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/).