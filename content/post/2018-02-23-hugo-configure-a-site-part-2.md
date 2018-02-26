---
title: "Configure a site using Hugo - part 2"
date: 2018-02-23T14:35:04+01:00
subtitle: "Creating the bluish capsule using Hugo, Atom and Github"
tags: ["hugo", "atom", "github"]
categories: ["development"]
draft: true
---

# Structure
We are going to create the following folders one for each area of the site.

- ```/content/page``` - static pages like About and so on
- ```/content/post``` - a section with articles and turorials
- ```/content/project``` - will contain a subfolder for each project
- ```/content/review``` - review of products bough online from amazon, aliexpress, ...
- ```/content/note``` - small snippets of stuff like links to a nice library or an interesting article

Create an index page for each section of the site.
```shell
hugo new post/_index.md
hugo new project/_index.md
hugo new review/_index.md
hugo new note/_index.md
```

In the *page* section we will not create an index file but just the pages.
```shell
hugo new page/about.md
```

Insert a brief description of each section in the index pages.
Change the draft status to false.

## Archetipes
Open archetypes/default.md
Insert subtitle, tags and categories.
```
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
subtitle: ""
tags: []
categories: []
draft: false
---

## Theme customization

### Pygment

Add to ```config.toml``` just below the global parameters

```toml
metaDataFormat = "yaml"
pygmentsStyle = "trac"
pygmentsUseClasses = true
pygmentsCodeFences = true
pygmentsCodefencesGuessSyntax = true
```

### Add navigation to the top banner

In ```config.toml``` after the _[author]_ section

```toml
[[menu.main]]
  name = "Blog"
  url = "/post"
  weight = 1

[[menu.main]]
  name = "Notes"
  url = "/note"
  weight = 2

[[menu.main]]
  identifier = "projects"
  name = "Projects"
  weight = 3
[[menu.main]]
  parent = "projects"
  name = "Projects home"
  url = "project"
  weight = 1
[[menu.main]]
  parent = "projects"
  name = "Meccanici"
  url = "project/meccanici"
  weight = 2

[[menu.main]]
  name = "Reviews"
  url = "/review"
  weight = 4
```
