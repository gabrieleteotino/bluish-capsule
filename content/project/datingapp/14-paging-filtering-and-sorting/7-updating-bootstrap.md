---
title: "Updating Bootstrap"
date: 2018-07-26T12:10:22.702+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Install bootstrap 4

```shell
# Specify the version to force the update from 3 to 4
npm install bootstrap@4.1.1 -save
# Or update to the latest version
npm install bootstrap@latest -save
```

Open **angular.json** and remove all the added css in the *styles* section. This section is present in *build* and in *test*

```json
"styles": [
  "src/styles.css"
],
```

Open **styles.css** and add the styles

```css
@import '../node_modules/bootstrap/dist/css/bootstrap.min.css';
@import '../node_modules/bootswatch/dist/united/bootstrap.min.css';
```

Update *Bootswatch*

```shell
npm install bootswatch@latest --save
```

Fix some security stuff

```shell
npm audit fix
```
