---
title: "Hugo Configure a Site Part 3"
date: 2018-02-26T11:41:09+01:00
subtitle: ""
tags: ["hugo", "atom", "github"]
categories: ["development"]
draft: false
---

# Custom domain
## Github custom domain
[Offical instructions](https://help.github.com/articles/adding-or-removing-a-custom-domain-for-your-github-pages-site/)

Go to the repository containing the site and move to the *Settings* panel. [gabrieleteotino settings](https://github.com/gabrieleteotino/gabrieleteotino.github.io/settings)

{{< figure src="/img/github_settings_tab.png" title="Github settings tab" >}}

Scroll down to *GitHub Pages* section find *Custom Domain* and insert or modify the domain "www.teosoft.it" and click *Save*

{{< figure src="/img/github_settings_custom_domain.png" title="Github custom domain" >}}

## DNS configuration
Open the control panel of the site registar.
Open the DNS section.
Change the **www** **CNAME** to *gabrieleteotino.github.io*

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

## SSL and CDN on Cloudflare
Signup to [Cloudflare](https://www.cloudflare.com/a/sign-up-n).

Add www.teosoft.it, select the free plan and follow the instructions.

Change the nameservers from *ns10.ovh.net*, *dns10.ovh.net* to *brett.ns.cloudflare.com*, *kiki.ns.cloudflare.com*.

In the control panale go to Domains -> teosoft.it, select **DNS servers** click **Modify DNS servers** and change the nameservers.

Note: changes submitted at 13:05 and Cloudflare detected the changes at 17:07.

While we wait it is a good time to launch a performance test with [webpagetest](https://www.webpagetest.org) before Cloudflare start to serve our site.

Load Time| First Byte| Start Render| Speed Index
---|---|---|---
2.780s| 0.299s| 1.400s| 1405

After a few hours (at 17:07) the DNS are pointed to Clouflare servers.

Change in config.toml (restoring https)
```toml
baseURL = "https://www.teosoft.it/"
```
Rebuild and publish the site.

Let's check webpagetest again.

Load Time| First Byte| Start Render| Speed Index
---|---|---|---
2.542s| 0.396s| 1.200s| 1385

A small consistent improvement and now we have https.
