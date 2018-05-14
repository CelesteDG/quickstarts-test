---
title: Azure AD v2 JS SPA Quickstart | Microsoft Docs
description: How JavaScript SPA applications can call an API that require access tokens by Azure Active Directory v2 endpoint
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity�
ms.date: 05/09/2018
ms.author: andret

---

# TEST TEST CHROMELESS --- Sign-in users and acquire an access token from a JavaScript application

This quickstart contains a code sample that demonstrates how a JavaScript Single Page Application (SPA) can sign in personal, work and school accounts, get an access token, and call the Microsoft Graph API.

![How the sample app generated by this guide works](media/active-directory-javascriptspa/javascriptspa-intro.png)


## Step 1: Download your web server or project

- [Download the Visual Studio project](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
- [Download the core project files - for a local web server, such as Python](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip)

> [!div class="sxs-lookup" renderon="portal"]
> ## Step 2: Configure your application registration
> 1. Select **Authentication** tab and add a reply URL correspond to the application's URL based on your web server
> 2. Make sure **Implict flow** is **checked**
> 3. Click *Save*

> [!div renderon="portal" id="makechanges"]

::: zone render="docs"

> ## Step 2: Register your application (DOCS ONLY)
> 
> 1. To register an application, go to the [Azure AD - Application Registration](https://apps.dev.microsoft.com/portal/register-app)
> 2. Enter a name for your application and click **Create**
> 3. Select **Authentication** tab and add  *http://localhost:30662/*
> 4. Make sure **Implict flow** is **checked**
> 5. Click *Save*

::: zone-end

::: zone render="chromeless"
> ## Step 2: Register your application (IBIZA ONLY)
> 
> 3. Select **Authentication** tab and add  *http://localhost:30662/*
> 4. Make sure **Implict flow** is **checked**
> 5. Click *Save*

::: zone-end

> [!div renderon="docs"]
>> [!NOTE]
>> If you use Visual Studio, set the Redirect URL to *http://localhost:30662/* as it is configured in the code sample's project. If you use Python or any other web server, feel free to set any port as long as the redirect URL for your Web Server and in the application registration information both match. For Phython, to set redirect URL to *http://localhost:30662/*, run the following command line:
>> ```bash
>> python -m http.server 30662
>> ```

## Step 3: Configure your JavaScript SPA

1. Still in the Azure Portal, copy the value for **Application ID** to the clipboard
2.	Edit `msalconfig.js` and replace <code>Enter_the_Application_Id_here</code> with the Application ID your application:

    ```javascript
    var msalconfig = {
        clientID: "Enter_the_Application_Id_here",
        redirectUri: location.origin
    };
    ```

## More Information

### *msal.js*
MSAL is the library used to sign in users and request tokens used to access an API protected by Microsoft Azure Active Directory. The Quickstart's *index.html* contains reference to the library:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>
````

### MSAL initialization

The quickstart code also shows how you how you initialize the library:

```javascript
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});
```

> |Where  |  |
> |---------|---------|
> |ClientId     |Application Id from the application registered in the Azure Portal|
> |loginCallBack | Name of a callback method called after the authentication|
> |redirectUri     |URL where users are sent after authentication against Azure AD v2 Endpoint|

### Sign-in users

Below how you sign in users:

```javascript
userAgentApplication.loginRedirect(graphAPIScopes);
```

> |Where  |  |
> |---------|---------|
> |graphAPIScopes     | (Optional) Contains scopes being requested at login time (i.e. `{ "user.read" }` for Microsoft Graph or `{ "api://<Application ID>/access_as_user" }` for custom Web APIs)|

> [!TIP]
> Alternativelly you may want to use `loginPopup` method to display a popup window for sign-in the user.

### Requesting tokens

Msal has two methods to used acquire tokens `acquireTokenRedirect` (or `acquireTokenPopup`) and `acquireTokenSilent`:

#### Getting a user token silently

The `acquireTokenSilent` method handles token acquisitions and renewal without any user interaction. After `loginRedirect` (or `loginPopup`) is executed for the first time, `acquireTokenSilent` is the method commonly used to obtain tokens used to access protected resources for subsequent calls - as calls to request or renew tokens are made silently.

```javascript
// Try to acquire the token used to query Graph API silently first:
userAgentApplication.acquireTokenSilent(graphAPIScopes)
```

> |Where  |  |
> |---------|---------|
> |graphAPIScopes     | (Optional) Contains scopes being requested at login time (i.e. `{ "user.read" }` for Microsoft Graph or `{ "api://<Application ID>/access_as_user" }` for custom Web APIs)|

#### Getting a user token interactively

 There are situations however that you need to force users interact with Azure Active Directory v2 endpoint � some examples include:
- Users may need to reenter their credentials because the password has expired
- Your application is requesting access to a resource that the user needs to consent to
- Two factor authentication is required

Calling the *acquireTokenRedirect(scope)* result in redirecting users to the Azure Active Directory v2 endpoint (or *acquireTokenPopup(scope)* result on a popup window) where users need to interact with by either confirming their credentials, giving the consent to the required resource, or completing the two factor authentication.

```javascript
userAgentApplication.acquireTokenRedirect(graphAPIScopes);
```

## What is next

Try out the JavaScript tutorials for a complete step-by-step guide on building applications and building new features, including a full explanation of this Quickstart, and other tutorials, like call Microsoft Graph API, sign-out:

### Learn the steps to create the application for this Quickstart

> [!div class="nextstepaction"]
> [Call Graph API tutorial](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

<!--
### Learn other scenarios
> [!div class="nextstepaction"]
> [Sign-Out tutorial](..\tutorials\active-directory-javascriptspa-sign-out.md)
> [Call Graph API tutorial](..\tutorials\active-directory-javascriptspa-call-graph-api.md)
-->

[!INCLUDE [Help and support](../../../../includes/active-directory-develop-help-support-include.md)]