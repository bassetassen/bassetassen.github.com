---
layout: post
title: Building C# 7 with TeamCity
categories: [CI, TeamCity]
tags: [Visual Studio 2017, TeamCity, C#]
date: 2017-10-19 14:30:00 +0200
---
{% include JB/setup %}

We have started using some new language features from C# 7 lately and then some builds on Team City started failing. Luckily I remembered my post from the time we started using C# 6 features. You can find that post here: {{ site.baseurl }}{% post_url 2015-08-13-building-csharp6-with-teamcity %}

```
error CS1003: Syntax error, ',' expected
```

The builds were using the Visual Studio sln build configuration and was set to Visual Studio 2015. I changed this to Visual Studio 2017, but then there was no compatible agent. I then installed the Build Tools for Visual Stuido 2017 and restarted the agents, now everything is working as expected.

You find the Build Tools for Visual Studio 2017 <a href="https://www.visualstudio.com/downloads/#build-tools-for-visual-studio-2017">here</a>. Scroll all the way to the bottom and you find the download link or just search for Build tools.