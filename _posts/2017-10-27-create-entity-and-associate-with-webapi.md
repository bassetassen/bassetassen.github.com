---
layout: post
title: Create an entity using the WebApi with lookup values
categories: [Dynamics 365]
tags: [Dynamics365, WebApi]
date: 2017-10-27 15:45:00 +0200
---
{% include JB/setup %}

When creating a new entity with the WebApi for Dynamics 365 the documentation looks pretty clear. It also looks straight forward when associating the new entity with an existing one. See [here](https://msdn.microsoft.com/en-us/library/gg328090.aspx#Anchor_3) for a reference.

This example from the previous link creates an account and set the primary contact.

```json
{
"name":"Sample Account",
"primarycontactid@odata.bind":"/contacts(e13072b3-81ba-e711-8108-5065f38ba391)"
}
```

When looking at the code one would think that it is the logicalname for the attribute that is in use here and we only need to append the @odata.bind part. But as soon we start to associate custom entities we will see that it is not the logicalname of the attribute but rather the associatednavigationproperty.

Here is the result querying the newly created account with prefer header set to odata.include-annotations="*"

```json
{
    "@odata.context": "https://someorg.api.crm4.dynamics.com/api/data/v8.2/$metadata#accounts(_primarycontactid_value)/$entity",
    "@odata.etag": "W/\"1434863\"",
    "_primarycontactid_value@OData.Community.Display.V1.FormattedValue": "Sebastian Holager",
    "_primarycontactid_value@Microsoft.Dynamics.CRM.associatednavigationproperty": "primarycontactid",
    "_primarycontactid_value@Microsoft.Dynamics.CRM.lookuplogicalname": "contact",
    "_primarycontactid_value": "e13072b3-81ba-e711-8108-5065f38ba391",
    "accountid": "72bc7a32-82ba-e711-8108-5065f38ba391"
}
```

When querying with this prefer header we get to see the name of the associatednavigationproperty. In this example it's not possible to conclude if it is the logicalname or the associatednavigationproperty that is used.

When setting a lookup on a custom attribute the associatednavigationproperty is set to the same as the schemaname, this is confusing because this is not the case on the standard lookup attributes as we saw on the primarycontactid example above. Schemaname for primarycontactid is PrimaryContactId.

Here is a example on a custom entity, notice the case-sensitivity. Here the associatednavigationproperty is equal to the schemaname of the attribute.
```json
{
	"new_Testentitet@odata.bind": "/new_testentitets(0FB7A14C-8DBA-E711-810A-5065F38BD3C1)"
}
```

Now let's look at a custom activity, then things really starts to be interesting.

In this example I will create a custom activity. Here is the body of the POST.

```json
{
    "regardingobjectid_incident@odata.bind": "/incidents(5CD45010-6752-E711-80FA-5065F38BA391)",
    "crmntime_Case_crmntime_TimeEntry@odata.bind": "/incidents(5CD45010-6752-E711-80FA-5065F38BA391)",
    "crmntime_HourTypeMain_crmntime_TimeEntry@odata.bind": "/crmntime_hourtypes(2b645ea3-7352-e711-80fa-5065f38ba391)"
}
```

We associate the same incident in the custom crmntime_case attribute and the standard regardingobjectid attribute of an activity. We also associate an crmntime_hourtype that is a custom attribute referencing a custom entity.

This is the result when querying the newly created recored.

```json
{
    "_regardingobjectid_value@OData.Community.Display.V1.FormattedValue": "Test",
    "_regardingobjectid_value@Microsoft.Dynamics.CRM.associatednavigationproperty": "regardingobjectid_incident_crmntime_timeentry",
    "_regardingobjectid_value@Microsoft.Dynamics.CRM.lookuplogicalname": "incident",
    "_regardingobjectid_value": "5cd45010-6752-e711-80fa-5065f38ba391",
    "_crmntime_case_value@OData.Community.Display.V1.FormattedValue": "Test",
    "_crmntime_case_value@Microsoft.Dynamics.CRM.associatednavigationproperty": "crmntime_Case_crmntime_TimeEntry",
    "_crmntime_case_value@Microsoft.Dynamics.CRM.lookuplogicalname": "incident",
    "_crmntime_case_value": "5cd45010-6752-e711-80fa-5065f38ba391",
    "_crmntime_hourtypemain_value@OData.Community.Display.V1.FormattedValue": "Test type",
    "_crmntime_hourtypemain_value@Microsoft.Dynamics.CRM.associatednavigationproperty": "crmntime_HourTypeMain_crmntime_TimeEntry",
    "_crmntime_hourtypemain_value@Microsoft.Dynamics.CRM.lookuplogicalname": "crmntime_hourtype",
    "_crmntime_hourtypemain_value": "2b645ea3-7352-e711-80fa-5065f38ba391"
}

```

When looking at this result we can see that regardingobjectid_incident is not the associatednavigationproperty, but using regardingobjectid_incident_crmntime_timeentry@odata.bind also work.

Notice that the associatednavigationproperty is case-sensitive and on custom relationships on custom activities it looks like it take the form of *attribute schema name*_*entity schema name*. In this example for the hourtypemain lookup the attribute schema name is crmntime_HourTypeMain and this lookup attribute exist on enity with schema name crmntime_TimeEntry so the associatednavigationproperty becomes crmntime_HourTypeMain_crmntime_TimeEntry. This is different from other custom lookup attributes on custom entities where the form of associatednavigationproperty is equal to schemaname of the attribute.

I hope these examples can help you out when exploring the Web Api and associating entities when creating and updating entities.