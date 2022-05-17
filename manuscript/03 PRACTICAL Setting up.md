# Practical: Setting up

## What you’ll need

This section describes the tools you’ll need to follow this book and to build the energy explorer.

You’ll need three things:

* a **modern web browser** such as Chrome or Firefox
* a **code editor** such as [Atom](https://atom.io/), [VS Code](https://code.visualstudio.com/) or [Brackets](http://brackets.io/)
* a **web server** running on your computer

You probably already have a web browser. I recommend using Chrome or Firefox. You’re probably fine using other browsers so long as they’re modern and up to date.

If you’re a developer and already using a preferred code editor then please stick with that. If you don’t have a preferred code editor I recommend [Atom](https://atom.io/), [VS Code](https://code.visualstudio.com/) or [Brackets](http://brackets.io/). These are covered in the [Web Development Tools section](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/tools-set-up-etc/) in the Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book.

Finally you’ll need to have a web server running on your machine. This is so that the data can be loaded without running into browser security restrictions.

If you’re a web developer there’s a good chance you’re familiar with setting up a server using Node, Python or similar.

If not I recommend using Brackets as your code editor as this has a built-in server. For futher information read the [Web](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/tools-set-up-etc/) [](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/tools-set-up-etc/)[Development Tools section](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/tools-set-up-etc/) in the Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book.

## Get set up

This book includes a download containing code snapshots of [each step](https://learn.createwithdata.com/books/d3-start-to-finish/sections/overview-of-the-energy-explorer-build) of the Energy Explorer build.

The aim of this section is to:

* **download the code** (in the form of a zip file)
* **expand** the zip file
* **open the first code step** in your code editor
* **run a web server** to serve the first code step
* **view the page** in your web browser

If at any point these steps don’t make sense, I suggest going through the [Web Development Tools](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/tools-set-up-etc/) and [Example set-up](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/example-set-up/) sections in my [Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/) book.

### Download the code

Start by downloading the zip from [here](http://learn.createwithdata.com/download/d3-start-to-finish-code/).

Expand the zip and make sure you have a directory structure like:

```
d3-start-to-finish-code
├── step1
│   └── index.html
├── step10
│   ├── css
│   │   └── style.css
│   ├── data
│   │   └── data.csv
│   ├── index.html
│   └── js
│       ├── config.js
│       ├── layout.js
│       ├── lib
│       │   ├── d3.min.js
│       │   └── popup-v1.1.1.min.js
│       ├── main.js
│       └── update.js
...
```

Move the `d3-start-to-finish-code` directory somewhere convenient. Perhaps you already have a directory where you store your projects.

(I have a `development` directory within my home directory and have placed `d3-start-to-finish-code` there.)

You should aim for something like:

```
peter-home
├── development
│   ├── d3-start-to-finish-code
│   │   ├── step1
│   │   │   └── index.html
│   │   ├── step10
│   │   │   ├── css
│   │   │   │   └── style.css
│   │   │   ├── data
│   │   │   │   └── data.json
│   │   │   └── index.html
│   ...
├── documents
│   ...
├── music
│   ...
├── photos
│   ...
```

### Open step1 in code editor

Open your code editor and open `d3-start-to-finish-code/step1`.

In Atom, VS Code or Brackets you select ‘Open Folder…’ from the ‘File’ menu and select `d3-start-to-finish-code/step1`.

Within the code editor’s file browser, expand `step1`, click on `index.html` and you should see something like:

![](https://learn.createwithdata.com/wp-content/uploads/2020/10/image-1.png)

### Start web server

If you’re comfortable setting up a local web server (for example using Node or Python) please do so by navigating to `d3-start-to-finish` within a command line interface and starting the web server there.

Here’s an example of me starting `http-server` (a node web server) using the command line:

![](https://learn.createwithdata.com/wp-content/uploads/2020/10/http-server-1.gif)

Otherwise I recommend using Brackets as your code editor, opening `step1` as detailed above then click on the lightning symbol in the toolbar on the far right (or select Live Preview from the File menu.)

You can also read the [Web Development Tools](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/tools-set-up-etc/) section of my Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book for other options.

### View step1 in your browser

If you’re using Brackets a browser window should have opened when you clicked on Live Preview.

If you’re using a standalone web server navigate to the appropriate location in your web browser. Typically the URL will be similar to `localhost:8000`.

You should now be able to see a friendly greeting in your web browser:

![](https://learn.createwithdata.com/wp-content/uploads/2020/06/image-1.png)

### Edit the code

Just to check everything’s set up properly and to save you dealing with technical issues when you start building the energy explorer, try editing the greeting in `index.html` and saving.

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Energy mix by Country 2015</title>
</head>
<body>
<h1>Let's learn D3!</h1>
</body>
</html>
```

Refresh your browser and verify that the greeting has updated.

![](https://learn.createwithdata.com/wp-content/uploads/2020/06/image-3.png)

## Take a break

You’ve completed all the necessary set up steps and are now ready to start learning and building with D3!

The next section introduces D3 and gives a broad overview of what it is and what it can be used for.

You’ll then learn about using D3 to load data into the browser and will put that into action by loading the World Bank energy data into your application.