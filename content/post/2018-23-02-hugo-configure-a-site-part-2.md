---
title: "Configure a site using Hugo - part 2"
date: 2018-02-23T14:35:04+01:00
subtitle: "Creating the bluish capsule using Hugo, Atom and Github"
tags: ["hugo", "atom", "github"]
categories: ["development"]
draft: true
---

# Structure

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
draft: true
---
```

## Theme customization
### pygment
```toml
metaDataFormat = "yaml"
pygmentsStyle = "trac"
pygmentsUseClasses = true
pygmentsCodeFences = true
pygmentsCodefencesGuessSyntax = true
```

### Add some buttons to the top banner

```toml
[[menu.main]]
  name = "Blog"
  url = ""
  weight = 1

[[menu.main]]
  name = "About"
  url = "page/about/"
  weight = 3

[[menu.main]]
  identifier = "samples"
  name = "Samples"
  weight = 2

[[menu.main]]
  parent = "samples"
  name = "Big Image Sample"
  url = "post/2017-03-07-bigimg-sample"
  weight = 1

[[menu.main]]
  name = "Tags"
  url = "tags"
  weight = 3
```

-fix logo
## Add some content
