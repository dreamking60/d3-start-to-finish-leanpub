# Practical: Setting Up

## What you need

This section covers the tools you need to build Energy Explorer. You need three things:

* A **modern web browser** such as Chrome or Firefox.
* A **code editor** such as [Brackets](http://brackets.io/) or [VS Code](https://code.visualstudio.com/).
* A **web server** running on your computer.

You probably already have a web browser. I recommend using Chrome or Firefox. You’re probably fine using other browsers so long as they’re modern and up to date.

If you’re a developer and already using a preferred code editor then please stick with that. If you don’t have a preferred code editor I recommend [Brackets](http://brackets.io/) or [VS Code](https://code.visualstudio.com/). These are covered in Appendix A.

Finally you need to have a web server running on your machine. This is so that the data can be loaded without running into browser security restrictions. If you’re a web developer there’s a good chance you’re familiar with setting up a server using Node, Python or similar. If not I recommend using Brackets as your code editor as this has a built-in server. For futher information refer to Appendix A and Appendix B.

## Get set up

The aim of this section is to:

* **download the code** (in the form of a zip file)
* **expand** the zip file
* **open the code** in your code editor
* **run a web server** to serve the code
* **view the page** in your web browser

(If at any point you get stuck, refer to Appendix A and Appendix B for further information.)

### Download the code

Start by downloading the code from [here](https://cwd-resources.netlify.app/d3starttofinish/2-6-22-3jrisb/d3-start-to-finish-code.zip) (it's in the form of a zip file).

Expand the zip and make sure you have a directory structure like:

```text
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

Move the `d3-start-to-finish-code` directory somewhere convenient. Perhaps you already have a directory where you store your projects. (I have a `development` directory within my home directory and have placed `d3-start-to-finish-code` there.) You should aim for something like:

```text
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

Open your text editor and open `d3-start-to-finish-code/step1`. In VS Code or Brackets you select ‘Open Folder…’ from the ‘File’ menu and select `d3-start-to-finish-code/step1`. Within the code editor’s file browser, expand `step1`, click on `index.html` and you should see something like:

![step1/index.html in the code download](7a9df67ce015631879cb1fb317e83bb2.png)

### Start web server

If you’re comfortable setting up a local web server (for example using Node or Python) please do so by navigating to `d3-start-to-finish-code` within a command line interface and starting the web server there. Here’s a screenshot of my command line after starting `http-server` (a node web server):

![Starting node's http-server](91362466cb9b83dc8295bd5c9a85e6fc.png)

Otherwise I recommend using Brackets as your code editor and using it's Live Preview (see next section), or using VS Code's Live Server extension (see next section).

### View step1 in your browser

If you're using a Node or Python server (or similar), navigate to the URL specified by the server and open `step1`. The URL should be something like `localhost:8000/step1`.

If you're using Brackets, select `step1/index.html` in the sidebar and click the lightning symbol (or select Live Preview from the File menu). If you're using VS Code's Live Server extension, select the `step1` directory in the sidebar and click the 'Go Live' button (usually it's in the footer menu). See Appendix A and Appendix B for more information.

You should now see a friendly greeting in your web browser:

{width: 66%}
![step1 of the code download (viewed using Brackets' server)](a87e7b386cc4b34b0c09031b6bf552ba.png)

### Edit the code

Just to check everything’s set up properly try editing the greeting in `index.html` and saving.

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
  <head>
  <meta charset="utf-8">
  <title>Energy mix by Country 2015</title>
</head>
<body>
markua-start-insert
  <h1>Let's learn D3!</h1>
markua-end-insert
</body>
</html>
```

Refresh your browser and verify that the greeting has updated.

{width: 66%}
![Greeting after editing index.html](3eba1689c4bcd951d085c7688962c678.png)

## Summary

You’ve completed all the necessary set up steps and are ready to start learning and building with D3! The next chapter introduces D3 and gives a broad overview of what it is and what it can be used for.