---
title: "2018 02 26 Hugo Configure a Site Part 3"
date: 2018-02-26T11:41:09+01:00
subtitle: ""
tags: ["hugo", "atom", "github"]
categories: ["development"]
draft: true
---

# Custom domain
## Github custom domain
[Offical instructions](https://help.github.com/articles/adding-or-removing-a-custom-domain-for-your-github-pages-site/)

Go to the repository containing the site and move to the *Settings* panel.
[gabrieleteotino settings](https://github.com/gabrieleteotino/gabrieleteotino.github.io/settings)

**TODO image of setting panel**

Scroll down to *Custom Domain* and insert or modify the domain "www.teosoft.it" and click *Save*

**TODO image of custom domain**

## Tophost DNS configuration
Open the Tophost [control panel](https://cp.tophost.it/)
Open the DNS section.
Change the **www** **CNAME** from *w-10.th.seeweb.it* to *gabrieleteotino.github.io*

Wait for the dns info to propagate.

To check the status of the DNS
```shell
dig www.teosoft.it +nostats +nocomments +nocmd
```

If dig is not istalled
```shell
sudo apt install dnsutils
```

## Hugo configuration
Change in config.toml
```toml
baseURL = "https://www.teosoft.it/"
```

# Google Analytics
Open [Google Analytics]() and find your id.

Change in config.toml in the global parameter section, just below the pygment.
```toml
googleAnalytics = "UA-24994854-1"
```

# gcse google custom search
## Some content
-fix logo
