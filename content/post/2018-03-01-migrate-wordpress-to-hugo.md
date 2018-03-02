---
title: "Migrate Wordpress to Hugo"
date: 2018-03-01T16:59:51+01:00
subtitle: "Migration of old sites and articles to Hugo"
tags: ["hugo", "wordpress"]
categories: ["webdev"]
draft: true
---

[Jekyll Exporter](https://wordpress.org/plugins/jekyll-exporter/) ([github page](https://github.com/benbalter/wordpress-to-jekyll-exporter)) is a plugin that will export everything from WordPress to Jekyll.

There is also [WordPress to Hugo Exporter](https://github.com/SchumacherFM/wordpress-to-hugo-exporter) but I choose the one for jekyll because the plugin is already available in the WordPress plugins.

A good amount of work is still needed to fix the results so I didn't bother.

<!--more--->

## Change wordpress site URL

The old site url is not available. I created a new CNAME in the DNS.

To change the settings int the wordpress database the following commands must be run.

```sql
UPDATE wp_options SET option_value = replace(option_value, 'http://www.oldurl', 'http://www.newurl') WHERE option_name = 'home' OR option_name = 'siteurl';

UPDATE wp_posts SET guid = replace(guid, 'http://www.oldurl','http://www.newurl');

UPDATE wp_posts SET post_content = replace(post_content, 'http://www.oldurl', 'http://www.newurl');

UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://www.oldurl','http://www.newurl');
```

## Jekyll Exporter
From the WordPress administration go to **Plugins** -> **Add new**, search *Jekyll Exporter* by Ben Balter and install it.
Activate it in **Plugins** -> **Installed plugins**
In **Tools**-> **Export to Jekyll** the plugin will create and download a zip file with all your content.

Extract the zip and start to fix all the things: reorganizing images and converting all the unrecognized plugins code.
