---
layout: post
title: .Net Client profile annoyance, and how to get rid of it
categories: [.Net, Visual Studio]
tags: [C#, Client Profile, Visual Studio]
date: 2011-11-13 14:20:00 +0200
---
{% include JB/setup %}

In Visual Studio 2010 some project types(see link to msdn pages at the bottom of this post for full list) defaults to .Net Framework 4 Client Profile. In many cases that is a problem, actually in my line of work it is always a problem. The problem for me is that I reference libraries that target the full .Net framework, not the subset framework .Net client profile.

So when I add a reference to structuremap or any other library which targets the full .Net framework, everything looks good until the project is being build. Then I get an error: `The type or namespace name 'StructureMap' could not be found (are you missing a using directive or an assembly reference?)`.

The first few times I got this error message I wasted quite some time on troubleshooting. Now it is just annoying and I immediately change the target framework when I get this error message. You can change the target framework in the properties of your project. Just right-click project and choose properties.

On the new project screen in Visual Studio, there is a dropdown menu where it is possible to choose the target framework. But this list only show the full frameworks. Unfortunately there is no setting in Visual Studio to set projects to use the full framework either. But there is an solution, change the project template.

The project templates for C# in an default installation can be found `\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE\ProjectTemplates\CSharp`. In this example I will change the Console application. So from the CSharp directory I browse my way to `Windows\1033\`. Here are all the project templates that is available for windows applications in C#.

I copy the ConsoleApplication.zip file to some other location, so I got a backup of the project template.

Back in the project templates folder, I unzip the ConsoleApplication.zip and then open consoleapplication.csproj in some text editor. In this file I remove line 14 to 16. See the lines I remove below.

```xml
$if$ ($targetframeworkversion$ >= 4.0)
   <TargetFrameworkProfile>Client</TargetFrameworkProfile>
$endif$
```

Then I save the file and zip the files back to a zip file I call ConsoleApplication.zip. The project template is now done, but there is still one small thing left to do.

Since Visual Studio cache the project templates, a new project of type console application would now still be using the .Net 4 Client Profile framework. So we will have to update the cached folder. The cached folder is located `\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE\ProjectTemplatesCache\CSharp\Windows\1033` if you wondered.
To update the cached folder I launch the Visual Studio Command Prompt and run the command `devenv /installvstemplates`. This command runs for some seconds. When it is finished without any errors you can launch Visual Studio and create a console application project. Now it uses the full .Net 4 Framework, not the .Net 4 Client Profile.

http://msdn.microsoft.com/en-us/library/cc656912.aspx