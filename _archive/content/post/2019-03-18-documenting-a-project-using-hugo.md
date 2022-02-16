---
title: "Documenting a project using hugo"
date: 2019-03-18T12:55:52+01:00
subtitle: "Use hugo and hugo-theme-learn to document a project with markdown"
author: Gabriele Teotino
tags: ["hugo", "markdown", "theme"]
categories: ["development"]
draft: false
---

With the static generator [Hugo](https://gohugo.io/) and the nice theme [hugo-theme-learn](https://github.com/matcornic/hugo-theme-learn) we can easily build a skeleton for a documentation site.

<!--more-->

## Installation

Download and install **Hugo**, the official [guide](https://gohugo.io/getting-started/installing/) is a good starting point. There are also a few articles: _Configure a site using Hugo_ [part1]({{< relref "/post/2018-02-21-hugo-configure-a-site-part-1.md" >}}), [part2]({{< relref "/post/2018-02-23-hugo-configure-a-site-part-2.md" >}}) and [part3]({{< relref "/post/2018-02-26-hugo-configure-a-site-part-3.md" >}})

Create a new site and clone the theme

```shell
hugo new site CCWeb
cd CCWeb
cd themes
git clone https://github.com/matcornic/hugo-theme-learn.git
```

Open the site configuration *config.toml*
```shell
baseURL = "http://example.org/"
languageCode = "it-it"
title = "CCWeb"
theme = "hugo-theme-learn"

# For search functionality
[outputs]
home = [ "HTML", "RSS", "JSON"]
```

Browse the site skeleton

```shell
hugo server -D
```

## Chapters and content

Create a new chapter using the theme provided archetype

```shell
hugo new --kind chapter architettura/_index.md
```

Open the created file **content/architettura/_index.md** and change the chapter name, title and description.
Change also the **pre** attribute in the page metadata.

Add content pages to the chapter

```shell
hugo new architettura/tecnologie.md
# alternative
hugo new architettura/tecnologie/_index.md
```

## Style

### Logo

Create a new folder **CCWeb/static/images** and paste the logo image there.

Override the theme default by creating a new file **CCWeb/layouts/partials/logo.html**

```html
<a id="logo">
  <img src="/images/logo.png" alt="CCWeb logo">
</a>
```

### Favicon

Drop a 32x32 **favicon.png** into **CCWeb/static/images** and the job is complete.

To customize the favicon behaviour override the theme default by creating a new file **CCWeb/layouts/partials/favicon.html**
