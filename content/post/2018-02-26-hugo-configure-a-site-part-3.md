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
baseURL = "http://www.teosoft.it/"
```

Note: we will change to https in the future.

## Google Analytics
Open [Google Analytics](https://analytics.google.com) and find your id.

Change in config.toml in the global parameter section, just below the pygment.
```toml
googleAnalytics = "UA-24994854-1"
```

## Google custom search engine
Create a new custom search using [Google Custom Search](https://cse.google.com/).
Add as **Site to search** *www.teosoft.it/** and click *Create*.
From the setup page get the *Search engine ID*

Add the ID to the following line in config.toml, in the params section.
```toml
gcse = "005185449088889757369:5o44h-2knna"
```

In the Custom Search select **Look and feel** and switch to *Full width*, save.
In **Themes** select *classic*

## SSL on Cloudflare
Signup to [Cloudflare](https://www.cloudflare.com/a/sign-up-n)
Add www.teosoft.it
Select the free plan and follow the instructions.

When we reach the following screen

**TODO image**

Change the nameservers from *ns2.th.seeweb.it*, *ns1.th.seeweb.it* to *brett.ns.cloudflare.com*, *kiki.ns.cloudflare.com*.
Damn, tophost does not allow me to change the nameservers.

My next article will be on the new domain registar and new hosting.
