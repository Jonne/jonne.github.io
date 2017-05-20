---
layout: post
title:  "10 best practises you should embrace when doing Sitecore development"
description: These are the things we learned the hard way in the past couple of years of doing Sitecore development
##date: 2016-05-15 19:21
author: jonnekats
comments: true
categories: [Sitecore]
---
Two and a half years a go we started doing our first Sitecore project at Aviva Solutions. Not only that,
but because of a business opportunity we even started developing our own product on top of Sitecore! 
Ofcourse, this was dive in the deep and we had to learn some Sitecore lessons the hard way. Maybe you 
are doing or going to do your first Sitecore project: applying these best pactises will save you some
headaches! If you are allready an experienced Sitecore developer: hopefully you are allready practisiong
these, in the worst case it will be a nice reminder!

## Automate your development environment
Sitecore environments can get quite complex. An average Sitecore installation needs Sitecore, MongoDB & 
SQL Server. If you are using multiple front-ends in production, you probably also need SOLR. We are doing
e-commerce and in that case you also need Commerce Server. It will take a lot of time for new devs on
your team to install all these when they want to get started. Also, if you need to upgrade to a new version
all the devs in the team need to perform this upgrade as well and these kind of upgrades can take a long
time... So, make sure you really invest in an automated development environment. 

## Use IDs to reference templates and fields

## Don't use renderings directly from your views

## Create site specific base templates

## Don't use Axes.Child...

## Embrace pipelines

## Only serialize templates / layouts

## Modularize 




