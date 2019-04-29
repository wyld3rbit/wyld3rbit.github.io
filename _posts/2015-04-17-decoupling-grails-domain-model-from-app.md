---
author: arka_p
published: true
layout: post
title: Decoupling the Grails (2.x+) domain model
date: 2015-04-17 12:39:15
categories: grails grailsplugin dependency-resolution-strategy
---

Recently, an interesting request landed on my desk: to find ways to be able to change the domain model in a new Grails app to deal with downstream modifications to the use cases being served without having to re-wire the whole thing per Grails conventions.

Problem set decomposition: Given a domain model, be able to edit and inject changes to said model with minimal impact on rest of the Grails app.

Potential solutions:

1. Do nothing clever: Simply version each iteration of the app according to the version of domain model it uses.
2. Do something a bit cleverer: Leverage OOP design fundamentals of interfaces and abstractions in the model so as to encompass all possible types that the app might utilize in the future.
3. Do something badass: Use two-way binding and meta-class programming to possibly change the model at runtime. Scary thought. Possibly the ramblings of a fool. Or...something particularly awesome.
