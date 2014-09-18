---
layout: post
title: Hiding menu when printing in Dynamics CRM
categories: [CRM, JavaScript]
tags: [Print, CRM]
date: 2014-09-18 22:45:00 +0200
---
{% include JB/setup %}

Recently I had a client that needed to print CRM pages that contained some web resources. The standard print functionality in Dynamics CRM only supports printing static content, so some web resources used on the page being printed was omitted. The user wanted to use the browsers print capabilities and it all worked great except that the Dynamics CRM header was included on the top.

<img src="{{ site.url }}/assets/images/hiding_menu_in_print/print-heading.png" class="img-responsive" alt="Print preview showing CRM menu" title="CRM menu at the top of the page when printing in browser" />

As you can see from the picture above, the menu from CRM on the top of the page taking up some much needed space from other content.

The solution in our case was to do a little unsupported change by injecting some CSS in the page with JavaScript. You will only see this change when printing because of the media in the CSS is set to print.

{% highlight javascript %}
$('head').append('<style>@media print { #crmMasthead, #crmTopBar {display: none !important;} #crmContentPanel {top: 0 !important;}}</style>');
{% endhighlight %}

As you can see below, the menu is now gone and we get some extra space on each page when printing.

<img src="{{ site.url }}/assets/images/hiding_menu_in_print/print-heading-removed.png" class="img-responsive" alt="Print preview showing page without CRM menu" title="CRM menu at the top of the page is now gone when printing in browser" />