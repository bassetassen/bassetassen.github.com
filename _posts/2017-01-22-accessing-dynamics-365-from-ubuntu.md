---
layout: post
title: Accessing Dynamics 365 from Ubuntu
categories: [Linux, Ubuntu, Dynamics 365]
tags: [Dynamics 365, CRM]
date: 2017-01-22 14:00:00 +0200
---
{% include JB/setup %}

From home when using Dynamics 365 on a Ubuntu machine I'm quite regularly get the mobile forms when accessing Dynamics 365. So today I wanted to investigate this some more.

I start with Firefox and go to http://portal.office.com and log in. When I click on the Dynamics 365 tile, I get an error on the new app page.
<img src="{{ site.url }}/assets/images/dynamics_ubuntu/firefox-portal-error.png" class="img-responsive img-right" alt="Error message when using Firefox" title="Error message when using Firefox" />

When I close this dialog there is a blank page with portal menu on the top. Refreshing the page og pressing the sync button does not help.

I then try to go directly to my instance of Dynamics 365 with the url {organization}.crm4.dynamics.com. Then we are logged inn to the mobile site.

So no luck with Firefox, let's try Chrome.

When logging in to the portal with Chrome and clicking on the Dynamics 365 tile there is no error and I can see all my apps. Clicking on one of them I get to my organization.

If I type in the address to the organization as I tried with Firefox, I get the mobile site.

So to summarize I have to use Chrome and log in through portal.office.com or home.dynamics.com to avoid getting the mobile site of Dynamics 365 on a Ubuntu machine.
