---
title: "Meccanici_architecture"
date: 2018-05-02T15:48:59+02:00
subtitle: "The project structure for the Meccanici project"
author: Gabriele Teotino
tags: ["c#", "netcore"]
categories: ["dev"]
draft: true
---

The Meccanici project is an evolving project. The structure exposed here is for the first milestone and doesn't include a lot of future components.

Meccanici is the server backend. It exposes a web api for the clients to consume.

The solution consist of multiple projects. The main reason for the project split is to try to force a loose coupling between the objects.

*Meccanici.WebApi*: the main entry point for the application. All the public client endpoints are implemented here.
*Meccanici.Domain*: domain models and aggregates
*Meccanici.Repository*: generic interface for data access
*Meccanici.Repository.Marten*: concrete implementation for Postgresql using Marten
*Meccanici.Service*: services and sagas

The architecture can also be split using the microservice architecture or, in other words, by Domain Aggregate.
Meccanici.ApiGateway
Meccanici.Core
Meccanici.User
  WebApi
  Service
  Repository
  Repository.Marten
  Model
Meccanici.Veichle
Meccanici.Saga
