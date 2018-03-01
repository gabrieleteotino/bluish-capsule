---
title: "Migrate Wordpress to Hugo"
date: 2018-03-01T16:59:51+01:00
subtitle: "Migration of old sites and articles to Hugo"
tags: ["hugo", "wordpress"]
categories: ["webdev"]
draft: true
---

For the migration process (WordPress to Hugo Exporter)[https://github.com/SchumacherFM/wordpress-to-hugo-exporter] is a plugin that will export everything from WordPress to Hugo.

<!--more--->

# recover wordpress site
```sql
UPDATE wp_options SET option_value = replace(option_value, 'http://www.oldurl', 'http://www.newurl') WHERE option_name = 'home' OR option_name = 'siteurl';

UPDATE wp_posts SET guid = replace(guid, 'http://www.oldurl','http://www.newurl');

UPDATE wp_posts SET post_content = replace(post_content, 'http://www.oldurl', 'http://www.newurl');

UPDATE wp_postmeta SET meta_value = replace(meta_value,'http://www.oldurl','http://www.newurl');
```
