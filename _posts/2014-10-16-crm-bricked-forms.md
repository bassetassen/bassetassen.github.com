---
layout: post
title: Bricking your CRM Forms, the ultimate guide
categories: [CRM]
tags: [CRM, Forms]
date: 2014-10-20 22:22:00 +0200
---
{% include JB/setup %}

<img src="{{ site.url }}/assets/images/bricked_crm_forms/loader.png" class="img-responsive img-right" alt="Loader" title="Bricked white windows, never ending loader" />

The other day I completely bricked a form in Dynamics CRM. The form would just have the command bar and loader up, but nothing happen. This was in a custom entity we use for some settings, and I had a text field which was a template text for SMS sending. This text also had a few template codes that could be used. So in an attempt to be helpful. I added the available template codes in the description for the field. This way I thought it was easy for the user generating this templates to see what template codes that is available in the tool tip.

{% raw %}
I had chosen a template format like {{contactname}}, and then I would replace this with the name of the contact when we were sending the SMS. The problem is that this is a pretty widely used format, and CRM uses JsRender, which uses this kind of format as well.
{% endraw %}

It also took some time to figure out what was wrong, because only Internet Explorer showed something in the console. And IE, was sadly in this case, not the first browser I tried. But when I did try IE, it gave me the error message below, and I immediately understood what had gone wrong.

```
{% raw %}
JsRender Error: Syntax error
Unmatched or missing tag: "{{/contactname}}" in template
{% endraw %}
```

<img src="{{ site.url }}/assets/images/bricked_crm_forms/field_metadata.png" class="img-responsive img-right" alt="Field metadata" title="Description on field, do not put double curly brackets here" />

So by removing these template codes from the description on the field and publishing again everything was working as expected. I tried using HTML escape characters for the curly braces, but they was only showed as they are typed in. So as far as I know, there is no way to show template codes as these ones in the tool tip.

There is no problem storing these kind of templates in the field itself, but do not put it in the description of the field. There is also no problem storing single curly brackets in the description field either, just stay away from the double.