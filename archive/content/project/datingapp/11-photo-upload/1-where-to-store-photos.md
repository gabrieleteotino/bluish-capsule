---
title: "Where to store photos"
date: 2018-07-07T17:14:45+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular", "cloudinary"]
categories: ["dev"]
draft: false
---

<!--more-->

## DB

Photos can be stored inside the DB as BLOB objects.

This approach is not the best in terms of speed and it will use a very costly resource, the DB space, for a job that is not really appropriate.

It can be really simple to use the database, all the pieces are already in place and integrating with the security is already done.

## Filesystem

Using a filesystem is a nice approach because the filesystem is made to store those kind of objects. It can be a bit costly ineffective to use the webserver space to store large amount of information, but there are workarounds.

Implementation can be tricky but is still simple.

## Cloud service

A webservice dedicate to media storage is the most effective in terms of cost, scalability and performance. It is the most complex of the methods because we have to integrate our authentication and do a lot more work.

## And the winner is

The webservice! A very good choice, don't be lazy and plan ahead for growth and traffic.

The provider is *Cloudinary*.

The workflow is:

1. Client uploads Photo to API with JWT
2. Server uploads photo to Cloudinary with secret key
3. Cloudinary stores photo, sends response
4. API saves photo url an PublicId in the DB
5. API generates sql ID
6. API returns 201 Created response with location header
