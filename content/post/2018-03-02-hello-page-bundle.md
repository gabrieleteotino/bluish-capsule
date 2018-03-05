---
title: "Hello Page Bundle"
date: 2018-03-02T13:13:49+01:00
subtitle: ""
tags: ["hugo"]
categories: ["webdev"]
draft: true
---

## Hello Page Bundle

The [Hugo documentation](https://gohugo.io/content-management/organization/#page-bundles) have a nice example on how to structure images and other resources in a page bundle.

<!--more-->

The basic idea is to store all the content for a page in a folder where the main content is inside *index.md*.

A sample structure for this page.
```
└── content
    └── post
        └── 2018-03-02-hello-page-bundle
            ├── index.md   // <- https://example.com/post/018-03-02-hello-page-bundle/
            └── hello_my_name_is.jpg  // <- https://example.com/post/018-03-02-hello-page-bundle/hello_my_name_is.jpg
```

It is also possible to leave the main content page outside the bundle. This is the actual structure used.

```
└── content
    └── post
        ├── 2018-03-02-hello-page-bundle.md   // <- https://example.com/post/018-03-02-hello-page-bundle/
        └── 2018-03-02-hello-page-bundle
            └── hello_my_name_is.jpg  // <- https://example.com/post/018-03-02-hello-page-bundle/hello_my_name_is.jpg
```

---
And finally the bundled image.

{{< figure src="hello_my_name_is.jpg" >}}
