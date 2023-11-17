# OpenID Connect configuration using Azure Entra ID

### In this article

 * [Setup OpenID Connect logins for ArcGIS](#setup-openid-connect-logins-for-arcgis "Setup OpenID Connect logins for ArcGIS")
    * [Enable PKCE for OpenID Connect logins](#enable-pkce-for-openid-connect-logins "Enable PKCE")
 * [Troubleshoot OpenID Connect login issues](#troubleshoot-openid-connect-login-issues "Troubleshoot OpenID Connect login issues")

## Setup OpenID Connect logins for ArcGIS

   > [!NOTE]  
   > The configuration described below is for Azure public cloud only. Endpoints for sovereign Azure clouds (Azure Government, China Azure Cloud and Azure Germany) are separate.
1. Sign in to the Azure portal, https://portal.azure.com/.

2.	Click on the Azure portal menu and select Microsoft Entra ID.


      <img src="https://github.com/Esri/idp/assets/51384051/9e77482e-0f03-46a9-a3bc-272dd6f82fc8" width="720">


3.	Under Manage, select App registrations, then select New registration.


      <img src="https://github.com/Esri/idp/assets/51384051/bbf6a8c3-5e4a-4d1a-88b4-a94294f50141" width="720">


4.	Enter a user-facing display name for the app. Under Redirect URI, select Web as the platform, and then enter https://jwt.ms (This is an Azure utility can be used for troubleshooting). Select Register.

   > [!NOTE]  
   > For Supported account types, use the default option: Accounts in this organizational
   > directory only. ArcGIS currently only supports the registration of a single OIDC provider.

   <img src="https://github.com/Esri/idp/assets/51384051/1734018f-1655-4564-a5e1-3a6910bec10f" width="720">


5.	Under Manage, select Certificates & secrets.


      <img src="https://github.com/Esri/idp/assets/51384051/431926d4-3957-4c8f-b0ce-554af830c78f" width="720">


6.	Select New client secret. Enter a description and expiration date and select Add.


      <img src="https://github.com/Esri/idp/assets/51384051/6d1fb9b8-77c5-41bc-a1fe-0856f199c22e" width="720">


7. Select the copy icon next to the secret Value and save this in a text document as Registered client secret.
   
> [!WARNING]
> If you do not copy and save the Secret value at this moment, you will be unable to retrieve it once you
> navigate away from this page, and will need to create a new secret. You do not need to copy the Secret ID.
 
   <img src="https://github.com/Esri/idp/assets/51384051/32bdeb20-b50a-436b-bc3e-f78c5d4d0213" width="720">


8. Select Overview and copy the Application (client) ID value. Using any text\word editor of your choice, save this value, in a text document, as Registered client id.


      <img src="https://github.com/Esri/idp/assets/51384051/96977d9d-071c-414d-96e8-a5336ab23b05" width="720">


9. Select Endpoints.

      <img src="https://github.com/Esri/idp/assets/51384051/23c39bb9-a5d1-4a28-a816-c591eb28d6cf" width="720">


10. Copy and open the OpenID Connect metadata document URL in a web browser. Find and save the following values from the metadata in the text document:

      - issuer 
      - authorization_endpoint 
      - token_endpoint
      - jwks_uri
      - end_session_endpoint

      <img src="https://github.com/Esri/idp/assets/51384051/cca7233e-71a8-45ea-930f-60ee840ac536" width="720">


11. Sign in to your ArcGIS web site and navigate to Settings > Security > Logins. Select New Open ID Connect Login.


      <img src="https://github.com/Esri/idp/assets/51384051/40f9352c-9a55-4ba1-83bc-aed0130676db" width="720">


12. Fill in the parameters using the values saved in the text document and select Save.

      - For Let new members join, select Automatically. Joining via invitations is currently unsupported for Azure Entra ID.
      - For Provider scopes/permissions, enter the value: openid profile email.
      - Enable the option: Send access token in header.
      - For ArGIS username field\claim name, enter preferred_username. You may also choose to use any other id_token attribute of your choice, such as email.


         <img src="https://github.com/Esri/idp/assets/51384051/aa51e4ad-7e57-40a1-be0b-2fb0b7ec190e" width="720">


13. Select Configure login next to the OpenID connect login registration you created and scroll down to the end. Copy and save the ArcGIS Login Redirect URI value to the text document.

14. Navigate back to the App registration in the Azure portal. Under Manage, select Authentication, and then select Add URI. Paste the ArcGIS Login Redirect URI here and select Save.


      <img src="https://github.com/Esri/idp/assets/51384051/76c0ab74-04e3-4113-9a11-90a2e9171bdc" width="720">


15. Navigate to your ArcGIS web site and select the Microsoft Entra ID login created at Step 12. 


      <img src="https://github.com/Esri/idp/assets/51384051/d19aa968-126d-48f6-8c47-faf7183f3f71" width="720">


16. Enter your Azure Entra ID user credentials.


      <img src="https://github.com/Esri/idp/assets/51384051/30e98790-62d7-43c4-90a3-34cfef0ab05b" width="720">

      <img src="https://github.com/Esri/idp/assets/51384051/725de3fd-f80d-43a8-9234-cbce19f7601b" width="720">


17. Confirm that you are able to sign in, then select Sign Out to confirm a successful user log out.

      <img src="https://github.com/Esri/idp/assets/51384051/9858f840-f034-41d3-80f0-0650ba6bc03b" width="720">

      <img src="https://github.com/Esri/idp/assets/51384051/3ac117e2-d1de-4058-855c-328dc223f5f6" width="720">


### Enable PKCE for OpenID Connect logins

If you have a requirement to use PKCE, which is recommended when authenticating on native or mobile applications, you will need update your configuration in both Azure and ArcGIS:

#### Configure Azure Entra ID for PKCE
 - Sign in to the Azure portal, https://portal.azure.com/.
 - Click on the Azure portal menu and select Microsoft Entra ID.
 - Under Manage, select App registrations, then select All applications.
 - Select the app registration for your ArcGIS Service Provider.
 - Under Manage, select Authentication.
 - Under Platform Configurations, your ArcGIS Login Redirect URI will be listed under the platform type: Web. Copy your ArcGIS Login Redirect URI.
 - Delete the ArcGIS Login Redirect URI and select Save.
 - Select Add a platform, then select Single-page application. Paste your ArcGIS Login Redirect URI and select Configure.

#### Configure ArcGIS for PKCE
 - Sign in to your ArcGIS web site and navigate to Settings > Security > Logins.
 - Select Configure login, next to your OpenID connect login registration.
 - Enable the option: Use PKCE enhanced Authorization Code Flow and select Save.

> [!NOTE]  
> If you disable the *Use PKCE enhanced Authorization Code Flow* option in ArcGIS, you must switch your platform, for the Azure app registration, back to Web.

# Troubleshoot OpenID Connect login issues

### Verify the Azure app client secret

 - Sign in to your ArcGIS web site and navigate to Settings > Security > Logins.
 - Select Configure login, next to your OpenID connect login registration.
 - Under Registered client secret, select Show client secret.
 - In a new browser window or tab, sign in to the Azure portal, https://portal.azure.com/.
 - Click on the Azure portal menu and select Microsoft Entra ID.
 - Under Manage, select App registrations, then select All applications.
 - Select the app registration for your ArcGIS Service Provider.
 - Under Manage, select Certificates & secrets.
 - Verify that the ArcGIS client secret starts with the same characters as the client secret Value in Azure. The Azure Secret ID cannot be used as the ArcGIS client secret.

### Examine the Azure id_token

To examine the OpenID Connect id_token issued by Azure Entra ID, use the jwt.ms utility added when registering a new application (See Step 4 in [Setup OpenID Connect logins for ArcGIS](#setup-openid-connect-logins-for-arcgis "Setup OpenID Connect logins for ArcGIS")).

> [!NOTE]  
> If you registered your ArcGIS Redirect URI as a Single-page application, enable Access tokens and ID tokens for the app registration (Azure App registration > Authentication > Implicit grant and hybrid flows). 

1. Copy the URL below to a text editor of your choice:
```
   https://login.microsoftonline.com/{Your-Tenant-ID}/oauth2/v2.0/authorize?client_id={Your-app-client-id}&response_type=id_token&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid%20profile%20email&response_mode=fragment&state=12345&nonce={Nonce-value}
```

2. Update the following parts of the URL using the details of your Azure app registration:
    - {Your-Tenant-ID} : Replace with your Directory (tenant) ID.
    - {Your-app-client-id} : Replace with your Application (client) ID.
    - {Nonce-value} : Any number of your choice.

Example:
```
https://login.microsoftonline.com/c8aa7711-c203-4a29-9c49-60c4eab0f1bd/oauth2/v2.0/authorize?client_id=8dabf32f-e1d6-44ea-a5e5-1fcfefb33461&response_type=id_token&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid%20profile%20email&nonce=011235
```

3. Open the updated URL in your web browser and sign in using your Azure Entra ID when prompted. The jwt.ms utility will display the id_token.


      <img src="https://github.com/Esri/idp/assets/51384051/f111b159-4b46-40de-96e1-5371a68b0788" width="720">


4. Review the value of the iss claim and verify that it [matches](https://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation "OpenID Connect specification for id validation") the issuer id you configured ArcGIS with. If the Tenant IDs do not match, it means that the user attempting to sign in is from a different tenant than the one registered with ArcGIS. ArcGIS currently only supports registration of a single OpenID Connect provider.

   *Azure id_token iss claim*

      <img src="https://github.com/Esri/idp/assets/51384051/d1fd89d0-dfd2-4f07-8748-008c2e1b95e5" width="720">

   *ArcGIS Provider Issuer ID*

      <img src="https://github.com/Esri/idp/assets/51384051/a7159ce3-5bb4-479f-b013-c205038d6979" width="720">


5. Check that the id_token contains the following attributes:
    - email
    - name
    - oid
    - preferred_username
    - sub
