---
layout: post
title: Building C# 6 with TeamCity
categories: [CI, TeamCity]
tags: [Visual Studio 2015, TeamCity, C#]
date: 2015-08-13 18:24:00 +0200
---
{% include JB/setup %}

After we upgraded to Visual Studio 2015 and started to use some of the new language features in C#6, we got some trouble at work with some of our CI builds. We use TeamCity as build server and we had recently updated to 9.1.1. The builds was using the Visual Studio sln build configuration and was set to Visual Studio 2013.

The error was unexpected character '$'. So it's was easy to understand that it was the new string interpolation that was causing out build to break.

<img src="{{ site.url }}/assets/images/building_csharp6_with_teamcity/tc-csharp6-error.png" class="img-responsive img-right" alt="error CS1056: Unexpected character '$'" title="Error message with wrong build configuration" />

The first thing we did was to change the configuration to use Visual Studio 2015, but that only made the build incompatible with all of our build agents.

We then installed the build tools for 2015 and restarted the server. Then the build had compatible agents, and the build worked again. Here is a link to the <a href="http://www.microsoft.com/en-in/download/details.aspx?id=48159">Microsoft Build Tools 2015</a>

This was for an internal library that now was working again, but there still was a web project that had problems. This time the build was complaining about some missing dll's.

 ```
   C:\...bla.csproj(1045, 3): error MSB4019: The imported project
   "C:\Program Files (x86)\MSBuild\Microsoft\VisualStudio\v14.0\WebApplications\Microsoft.WebApplication.targets"
   was not found. Confirm that the path in the <Import> declaration is correct, and that the file exists on disk.
 ```

Now we only copied over the files from a computer with Visual Studio 2015 installed and this build was working again. We actually copied over the whole v14.0 folder.
