# Setting up Azure AD as an OpenID Connect (OIDC) Identity Provider for ArcGIS Enterprise 10.9.1
**This document describes steps to connect ArcGIS Enterprise to Azure AD via OIDC at Enterpries version 10.9.1. for other versions, YMMV.**

1. Log in to the Azure Portal and navigate to the _Azure Active Directory_ -\&gt; _App registrations_ blade
2. Create a new App Registration, providing a **Name** for the app, selecting whether to allow accounts to login from your directory, from multiple directories, or from personal accounts.
    1. Leave the Redirect URI portion blank and click **Register**
3. On the page for your App Registration, select **Add a certificate or secret** in the **Client Credentials** sectionat the right side
    1. create a **new Client Secret** , with a description like &quot;ArcGIS Enterprise OIDC secret&quot; and an expiration appropriate to your use case. Select **Add**
    2. Copy the resulting **Value** of the secret to use later
      1. eg. _i2X8Q~pkzRC5zxY8EQOHvMcyiyuvZ4bkF4w8AbGU_
4. Select **Overview** on the left to go back to the **Registered App** page
    1. Open the link for **Endpoints** at the top of the page, and copy the link for OpenID Connect metadata document: something like: [https://login.microsoftonline.com/91f5663a-841b-4dec-b19d-c0fb6f66b4bc/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/91f5663a-841b-4dec-b19d-c0fb6f66b4bc/v2.0/.well-known/openid-configuration)
5. Open this link in a separate tab.
6. In ArcGIS Enterprise, navigate to **Organization** \&gt; **Settings** \&gt; **Security** and select **New**** OpenID Connect login**
    1. Provide a **Login Button Label** for your login, for example _Azure AD_
    2. If you want to be able to allow any user from your directory to login, select **Automatically** , which may force you to set a default Role and User Type for users.
      1. If you do not want to add accounts automatically, **currently we are researching which attribute can be used to manually invite users.**** You would need to allow automatic creation to identify the attribute being sent by Azure AD, then turn off automatic creation.**
    3. For the remaining attributes, use the following values:
      1. **Registered client ID** : use the &quot;**Application (client) ID**&quot; value from the Registered App page in Azure AD.
      2. **Registered client secret** : use the Client Secret copied from the Azure AD page in an earlier step
      3. **Provider scopes/permissions:** enter **openid profile email**
      4. **Provider Issuer ID** : the &quot;issuer&quot; property from the OpenID metadata JSON (from the link copied above).
      5. **OAuth 2.0 authorization endpoint URL: **&quot;authorization\_endpoint&quot; value from the JSON configuration
        1. NOTE: **be careful to not duplicate https://** in this entry.
      6. **Token endpoint URL: **&quot;token\_endpoint&quot; value from the JSON configuration
      7. **JSON web key set (JWKS) URL: **&quot;jwks\_uri&quot; value from the JSON configuration
      8. **User profile endpoint URL (recommended): **&quot;userinfo\_endpoint&quot; value from the JSON configuration
      9. **Logout endpoint URL (optional): **&quot;end\_session\_endpoint&quot; value from the JSON configuration
      10. Send Access token in header: Set to True
    4. Click &quot;Save&quot; to complete adding the OIDC provider
7. Reopen the OIDC provider in Portal using **Configure Login**
    1. Copy the **Login Redirect URI** and **Logout Redirect URI** from the bottom of the Page for re-use
8. Back in Azure, on the **Registered App** page, navigate to the **Authentication** blade
    1. Select **Add a Platform** of type **Web**
    2. Provide the **redirect URI** that you copied in the previous step
    3. For the **Front-channel logout URL** , provide the logout URL from the previous step.
    4. Select and &quot;check on&quot; both **Access Tokens** and **ID Tokens**
    5. Select **Configure**
9. You should now be able to login to your ArcGIS Enterprise deployment using the Create Accounts Automatically setting.
