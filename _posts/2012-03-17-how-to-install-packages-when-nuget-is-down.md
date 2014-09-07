---
layout: post
title: How to install packages when NuGet is down
categories: [Visual Studio, Tools]
tags: [NuGet, MyGet, Visual Studio]
date: 2012-03-17 23:33:00 +0200
---
{% include JB/setup %}

Sometime during the 9th of March NuGet.org went down. This turned out to last for a while and a lot of people on twitter told their stories how builds were breaking and how they did not get to download packages. For me, I actually noticed how often I use NuGet and how annoying it is not having a backup plan.

If you wonder why NuGet was down, you can read about it on their [blog](http://blog.nuget.org/20120314/the-nuget-gallery-outage-on-march-9.html)

Thru twitter I soon realized that I could add my NuGet cache as package feed, this was a great way to use all the packages I usually add to my projects. Unfortunately I had not used the package I needed on that computer before, so I was not able to install the package. But nevertheless I will let you know how you add your NuGet cache as a package feed.

<img src="{{ site.url }}/assets/images/nuget_down/package_manager_console_settings_button.png" class="img-responsive img-right" alt="Package manager" title="The package manager window" />

It is a pretty easy job to add new feeds to your NuGet package manager console. When you are in the package manager console, click on the “Package Manager Settings” button, marked with red circle in this picture. There is also possible to go on visual studio tools menu and options. Here you find a “Package Manager” node witch is the same place this button goes to or you can go on the tools menu – Library Package Manager – Package Manager Settings.

On the Package Manager Settings page, go to the Package Sources node. Her you see the feeds available to use thru NuGet. Now I am adding a new feed called Local NuGet Cache, and in the source I use C:\Users\<LoggedOnUser>\AppData\Local\NuGet\Cache which points to my NuGet cache.

<img src="{{ site.url }}/assets/images/nuget_down/package_manager_settings_feeds.png" class="img-responsive" alt="Package manager settings" title="The package manager settings window" />

Now I have this Local NuGet Cache that I created as a option in my Package Manager Console and can use it just as I would use the official NuGet feed.

<img src="{{ site.url }}/assets/images/nuget_down/package_manager_console_list_feeds.png" class="img-responsive img-right" alt="Package manager feeds" title="Package manager feeds" />

So my backup strategy now is that I put my Local NuGet Cache and my public MyGet feed as alternative feeds in Visual Studio. So if NuGet will go down again for any reason I have some chances to get my most used packages still. If it is a package I have used on that particular computer before, it will be in my local NuGet cache. If it is not a package I have used before, hopefully I have added it to my MyGet feed so it is still available to me.

If you wonder what www.myget.org is, it is a site where you can create your own NuGet feed with packages from NuGet or any other package you might have. It is easy to use, works great and it is free if you choose the public or community feed, private feeds are not free.