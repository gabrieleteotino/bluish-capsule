---
title: "Bootswatch"
date: 2018-06-17T19:21:16+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

The official [site](https://bootswatch.com/) and [themes for 3.3.7](https://bootswatch.com/3/).

## Installation

Stop *ng serve* if it is running.

We are still using the old version of Bootstrap, so specify the correct version for the themes

```shell
npm install bootswatch@3.3.7 --save
```

## Configuration

The course choose **uunited** but I like **flatly**.

Open **angular.json** and add the css after the bootstrap one.

```json
"styles": [
  "node_modules/bootstrap/dist/css/bootstrap.min.css",
  "node_modules/bootswatch/flatly/bootstrap.min.css",
  "node_modules/font-awesome/css/font-awesome.min.css",
  "node_modules/alertifyjs/build/css/alertify.min.css",
  "node_modules/alertifyjs/build/css/themes/bootstrap.min.css",
  "src/styles.css"
],
```

Restart *ng serve*

## Navbar color

Open **nav.component.html** and switch from the *inverse* color to the *default*.

```html
<nav class="navbar navbar-default">
```

Check the site for the new theme.
