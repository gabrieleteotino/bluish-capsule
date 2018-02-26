---
title: "Configure a site using Hugo - part 1"
date: 2018-02-21T18:15:33+01:00
subtitle: "Creating the bluish capsule using Hugo, Atom and Github"
tags: ["hugo", "atom", "github"]
categories: ["development"]
draft: false
---

# Using Hugo for this website
A step by step description on how the bluish capsule was born.

## Installing Hugo
Detailed instructions are available at the [official Hugo site](http://gohugo.io/getting-started/installing/)

Download the deb package from [Hugo Releases](https://github.com/gohugoio/hugo/releases)

```shell
sudo dpkg -i hugo_0.36.1_Linux-64bit.deb
```

Test if everithing works
```shell
hugo version
```

## Atom Plugins

Nice autocomplete for hugo shortodes [language-hugo](https://atom.io/packages/language-hugo)

For example typing youtube and hitting tab the following snippet will be generated:
```
\{\{< youtube id class="" >\}\}
```

Some key combination for common Hugo activities [atom-hugo](https://atom.io/packages/atom-hugo)

For example CTRL+SUPER+N will create a new content item.


## Initialize Hugo website
Hugo will create a new site with the basic site structure.
Then we initialize git and add a theme as a [git submodule](https://git-scm.com/docs/git-submodule).

```shell
hugo new site bluish-capsule
cd bluish-capsule
git init
git submodule add https://github.com/halogenica/beautifulhugo.git themes/beautifulhugo
```

Other themes are available at [Hugo themes](https://themes.gohugo.io/)

## Configure the beautifulhugo theme
Each theme has a sligtly different configuration.
Inside the theme folder there is a theme.toml and inside the folder exampleSite there is another theme.toml with some advanced configuration.

Here is the configuration needed for the first run.
```toml
baseURL = "https://gabrieleteotino.github.io/"
languageCode = "en-us"
title = "Bluish capsule"
theme = "beautifulhugo"

metaDataFormat = "yaml"

[params]
  subtitle = "Some inspirational quote here"
  logo = "img/avatar-icon.png"
  favicon = "img/favicon.ico"
  dateFormat = "January 2, 2006"
  commit = false
  rss = true
  comments = false
  readingTime = true
  useHLJS = true
#  gcse = "012345678901234567890:abcdefghijk" # Get your code from google.com/cse. Make sure to go to "Look and Feel" and change Layout to "Full Width" and Theme to "Classic"

[author]
  name = "Gabriele Teotino"
  website = "http://www.teosoft.it"
  github = "gabrieleteotino"
  stackoverflow = "users/2085504/deramko"
```

## Add a post and test the site
Add a new post using the command line.
```shell
hugo new post/hello-world.md
```

Start the development server. The **-D** tells Hugo to pubblish draft pages.
```shell
hugo server -D
```
Now open the borwser and got the [home page](http://localhost:1313) and the [Hello World](http://localhost:1313/posts/hello-world/).

Write some content inside hello-world.md.
The live reload should immediatly show the new text.

Change the *front matter* draft to false, making the file ready for publishing.

## Github repository
Be sure to have your private keys configured for Github. See [here](2018-02-23-SSH-keys-for-github) for the configuration.

The following steps are taken from [whipperstacker instructions] (https://github.com/whipperstacker/blog/blob/master/content/post/deploying-a-hugo-site-to-github-pages.md)

Create a new repository in Github named bluish-capsule. Add a description, select public and don't create any readme, gitignore and license.

Stop Hugo if it is running, in the terminal goto the root folder of the site.
Confirm it is already initialize with
```shell
git status
```

Add the new remote origin
```shell
git remote add origin git@github.com:gabrieleteotino/bluish-capsule.git
```

Create a new repository for Github Pages named __gabrieleteotino.github.io__, add a description. This time check __Initialize this repository with a README__ leaving no gitignore and no license.

__Optional__ Delete the ```public/``` directory with ```rm -r public/```.

Add the "pages" repo as a submodlule
```shell
git submodule add git@github.com:gabrieleteotino/gabrieleteotino.github.io.git public
```

It is time to stage all the changes and commit to the local git.
```shell
git add .
git commit -m "Initial setup"
git push -u origin master
```

## Create content
Create the content in public folder
```shell
hugo -t "beautifulhugo"
cd public/
git add .
git commit -m "Generate site"
git push origin master
```
