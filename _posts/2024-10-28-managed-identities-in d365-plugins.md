---
layout: post
title: Using Managed Identity in Plugins
categories: [CRM, Dynamics 365, Azure, Security]
tags: [Dynamics 365, Security]
date: 2024-10-30 23:40:00 +0200
---
{% include JB/setup %}

When the 2024 Wave 1 was annonced the article about managed identities in Dataverse plugins really got me excited. We have been using managed identites in Azure services for a long time and the possablility to also be able to use that in plugins is very exciting stuff. The less we need to rely on credentials the better.

The preview for this [feature](https://www.microsoft.com/en-us/power-platform/blog/power-apps/announcing-public-preview-of-power-platform-managed-identity-support-for-dataverse-plug-ins/) dropped in August and I have finally had the time to try out and see how it works. I must admit it did not felt very mature to set this up yet, but then again, it is just in preview :D

I followed the documentation from Microsoft learn here: https://learn.microsoft.com/en-us/power-platform/admin/set-up-managed-identity

In my example I will create a plugin using the managed identity to get a secret from Azure Key Vault and just print it out in the trace log so show that it has actually got the secret.

### Identity
I started with creating a user-assigned managed identity (you can also use app registrations). Then under the federated credential I created a new one and added a issuer URL and an Subject identifier as the documentation shows.

In the documentation it shows that the format for the Issuer URL is: `https://[environment ID prefix].[environment ID suffix].enviornment.api.powerplatform.com/sts`

The way I found the environment id was going over to https://admin.powerplatform.microsoft.com/environments and clicking in to my environment, here you can see the ID:
<img src="{{ site.url }}/assets/images/managed_identities_dataverse_plugins/admin_powerplatform_environment.png" class="img-responsive" alt="Environment id in admin powerplatform" title="Environment id in admin powerplatform" />

The documentation says that the environment id should be split up in a prefix and a suffix where the suffix is the last two charathers and the prefix is everything except the last two. In the documentation you can see that the guid is stripped for dashes.

So for my environment with environment id 07d0e6ff-3293-eefd-9130-345f9adc035a, the environment prefix is `07d0e6ff3293eefd9130345f9adc03` and the suffix is `5a`.

For the Subject identifier the format is: `component:pluginassembly,thumbprint:<<Thumbprint>>,environment:<<EnvironmentId>>`. The thumbprint you get from the certificate you will sign the plugin assembly with.

So with my setup this is the Issuer URL and Subject identifier.

<img src="{{ site.url }}/assets/images/managed_identities_dataverse_plugins/fed_credential.png" class="img-responsive" alt="Federated Credential" title="Federated Credential" />


Issuer URL: `https://07d0e6ff3293eefd9130345f9adc03.5a.environment.api.powerplatform.com/sts`

Subject identifier: `component:pluginassembly,thumbprint:4349791EF561BF999AD43606FF91DD0D98615B6D,environment:07d0e6ff-3293-eefd-9130-345f9adc035a`

One thing I did wrong here when setting this up was not including the dashes in the environment id in Subject identifier and I then got an error in plugin traces when testing this out. The good thing was that the error captured in the trace wrote out the correct Subject identifier for the plugin.


### Azure Role Assignemnt
In Azure Portal I have given my Managed Identity the Key Vault Secrets User role on my Key Vault.

<img src="{{ site.url }}/assets/images/managed_identities_dataverse_plugins/identity_azure_role_assignment.png" class="img-responsive" alt="Azure Role Assignment" title="Azure Role Assignment" />


### Plugin
For the plugin code it is worth looking at the IManagedIdentityService and the use of AcquireToken with the scope for Key Vault, here I get the token that I use to get the secret from Key Vault. Other then that I just write out the content of the response from Key Vault.

```cs
public void Execute(IServiceProvider serviceProvider)
{
    var tracingService = (ITracingService)serviceProvider.GetService(typeof(ITracingService));
    tracingService.Trace("In execute method.");

    tracingService.Trace("Getting IManagedIdentityService");
    var managedIdentityService = (IManagedIdentityService)serviceProvider.GetService(typeof(IManagedIdentityService));
    tracingService.Trace("Getting token");
    var token = managedIdentityService.AcquireToken(new[] { "https://vault.azure.net/.default" });

    tracingService.Trace($"Getting secret");
    var secret = GetSecretFromKeyVault(token).GetAwaiter().GetResult();
    tracingService.Trace($"Secret: {secret}");
}

private async Task<string> GetSecretFromKeyVault(string accessToken)
{
    using (var httpClient = new HttpClient())
    {
        httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", accessToken);

        var response = await httpClient.GetAsync($"{_keyVaultUrl}secrets/{_secretName}?api-version=7.0");

        if (response.IsSuccessStatusCode)
        {
            var responseContent = await response.Content.ReadAsStringAsync();
            return responseContent;
        }
        else
        {
            throw new Exception($"Failed to retrieve secret from Key Vault. Status Code: {response.StatusCode}");
        }
    }
}
```

After the plugin is compiled you need to sign it for it to work. In my example I just use a self-signed certificate I created on my machine (it was the thumbprint from this certificate that I use in Subject identifier), but this is not an recommended approach for other then testing. I have my self-signed certificate registered as a code signing certificate so I then just use this command to sign it:

```command
signtool sign /fd SHA256 /a MIPlugin.dll
```

If you don't sign the plugin and you test with it you will get an error message like this

```
Plugin assembly must be signed with a valid certificate to use Managed Identity
```

If you need to create a self-signed certificate look at the powershell command `New-SelfSignedCertificate`.

When the plugin is compiled and signed you can register as you always do with pluginregistration tool.

### Register managed identity in Dataverse
Now you have to do a final step to register this in Dataverse and here is where it feels very preview. You have to do a POST request to the managedidentities endpoint in Dataverse API with the following body:
```json
 {
    "applicationid":"<<appId>>",
    "managedidentityid":"<<anyGuid>>",
    "credentialsource":2,
    "subjectscope":1,
    "tenantid":"<<tenantId>>"
 }
```
Since the managedidentityid property takes any id I just re-used the application id, here is a picture of my request from Postman:

<img src="{{ site.url }}/assets/images/managed_identities_dataverse_plugins/postman_post_managedidentity.png" class="img-responsive" alt="Postman POST managed identity" title="Postman POST managed identity" />

Then we have to do a PATCH request to relate the managed identity record we just created with the plugin assembly. The hardest part here was finding the correct id to the plugin assembly. I used this query in the API to find the ID:

```
https://org6b0754c2.crm19.dynamics.com/api/data/v9.0/pluginassemblies?$filter=name eq 'MIPlugin'&$select=pluginassemblyid
```

With the following result:

```json
{"@odata.context":"https://org6b0754c2.crm19.dynamics.com/api/data/v9.0/$metadata#pluginassemblies(pluginassemblyid)","value":[{"@odata.etag":"W/\"3351749\"","pluginassemblyid":"229afa6b-6fbe-4252-9fff-cb5c2114bc14"}]}
```

<img src="{{ site.url }}/assets/images/managed_identities_dataverse_plugins/postman_patch_plugin_assembly.png" class="img-responsive" alt="Postman PATCH plugin assembly" title="Postman PATCH plugin assembly" />


### Trying it out
Now I am ready to test the plugin. I have created a secret in my Key Vault and I have turned on tracing in my environment. My plugin triggers on creation of account so I create a new one and save it and wait a few seconds for my trace log record to appear. And as you can see from the picture below we got the secret from our Key Vault :satisfied:

<img src="{{ site.url }}/assets/images/managed_identities_dataverse_plugins/plugin_trace.png" class="img-responsive" alt="Trace show secret retrieved from Key Vault" title="Trace show secret retrieved from Key Vault" />