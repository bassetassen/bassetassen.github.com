---
layout: post
title: Web Resource Utility and CRM with Office 365
categories: [CRM]
tags: [CRM, Office 365]
date: 2013-07-17 00:00:00 +0200
---
{% include JB/setup %}

If you wish to use the Web Resource Utility that comes with CRM 2011 SDK on an CRM Online organization from Office 365, you will not be able to connect to the discovery service.

When you type in your credentials and server address you will get the error message shown below.

<img src="{{ site.url }}/assets/images/webresourceutility_crm365/error_logging_in.png" class="img-responsive" alt="Error message" title="Error message in webresource utility" />

This is because the Web Resource Utility uses

```
https://dev.[address_typed_in]/XRMServices/2011/Discovery.svc
```

That is correct for CRM Online as the discovery service, but not when you have CRM from Office 365. Office 365 instances of CRM uses disco insted of dev in the URL. So a correct URL for a CRM Online from Office 365 is

```
https://disco.[address_typed_in]/XRMServices/2011/Discovery.svc
```

For more info on the differnet discovery services see [MSDN](http://msdn.microsoft.com/en-us/library/gg328127.aspx)

Luckily there is an easy fix to the problem. In the method GetServerConfiguraton in the class ConsolelessServerConnection we have to change the DiscoveryUri.

Open the file and simply change the line

{% highlight csharp %}
config.DiscoveryUri = new Uri(String.Format("https://dev.{0}/XRMServices/2011/Discovery.svc", config.ServerAddress));
{% endhighlight %}

to

{% highlight csharp %}
config.DiscoveryUri = new Uri(String.Format("https://{0}/XRMServices/2011/Discovery.svc", config.ServerAddress));
{% endhighlight %}

After this change you will have to specify dev/disco part to use the tool, but it will work for both “regular” CRM Online and CRM Online from Office 365.

<img src="{{ site.url }}/assets/images/webresourceutility_crm365/connection.png" class="img-responsive" alt="Connection window" title="This is how you need to type in server address after the change in webresource utility" />
