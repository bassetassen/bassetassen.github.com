---
layout: post
title: LINQ to Sql with schema name other than dbo
categories: [.Net, Sql]
tags: [LINQ, SQL, CRM]
date: 2011-09-04 22:03:00 +0200
---
{% include JB/setup %}

At my former employer we were working with an CRM system from Visma. Visma is an Norwegian software company creating CRM, ERP and a lot of other systems. In this CRM system you could have several clients/companies and you would choose what client you want to logon to at startup.

When a new client were created, it would create an database with the same name as the client and a user in the sql server. All the objects in the created database would also use the schema of the created login, not the dbo schema.

This is causing some problems when using Linq to sql, because when an dbml file is created it uses the schema from the tables that is selected in the model. So when we created a model for client1 in an application, this application would not run against client2. Because in the dbml file every table is saved with the schema for client1, and in client2 the schema name is client2.

Luckily an co-worker found a great blog post from Guy Barrette that describes a way to create an mapping file so we could use our application on all the clients we’d like.

[Guy’s blog post](http://weblogs.asp.net/guybarrette/linq-to-sql-dynamic-mapping)