# OpenID Connect configuration using Azure Entra ID

### In this article

[Setup OpenID Connect logins for ArcGIS](#setup-openid-connect-logins-for-arcgis "Setup OpenID Connect logins for ArcGIS")

[Troubleshoot OpenID Connect login issues](#troubleshoot-openid-connect-login-issues "Troubleshoot OpenID Connect login issues")

## Setup OpenID Connect logins for ArcGIS

> [!NOTE]  
> The configuration described below is for Azure public cloud only. Endpoints for sovereign Azure clouds (Azure Government, China Azure Cloud and Azure Germany) are separate.
1. Sign in to the Azure portal, https://portal.azure.com/.

2.	Click on the Azure portal menu and select Microsoft Entra ID.


![Entra-ID](https://github.com/Esri/idp/assets/51384051/9e77482e-0f03-46a9-a3bc-272dd6f82fc8)


3.	Under Manage, select App registrations, then select New registration.


![New-App-Registration](https://github.com/Esri/idp/assets/51384051/bbf6a8c3-5e4a-4d1a-88b4-a94294f50141)


4.	Enter a user-facing display name for the app. Under Redirect URI, select Web as the platform, and then enter https://jwt.ms. Select Register.


![App-Details](https://github.com/Esri/idp/assets/51384051/1734018f-1655-4564-a5e1-3a6910bec10f)


5.	Under Manage, select Certificates & secrets.


![App-secret](https://github.com/Esri/idp/assets/51384051/431926d4-3957-4c8f-b0ce-554af830c78f)


6.	Select New client secret. Enter a description and expiration date and select Add.


![App-secret-details](https://github.com/Esri/idp/assets/51384051/6d1fb9b8-77c5-41bc-a1fe-0856f199c22e)


7. Select the copy icon next to the secret Value and save this in a text document as Registered client secret.

> [!WARNING]
> If you do not copy and save the Secret value at this moment, you will be unable to retrieve it once you navigate away from this page, and will need to create a new secret. You do not need to copy the Secret ID.
 

![App-secret-copy](https://github.com/Esri/idp/assets/51384051/32bdeb20-b50a-436b-bc3e-f78c5d4d0213)


8. Select Overview and copy the Application (client) ID value. Using any text\word editor of your choice, save this value, in a text document, as Registered client id.


![App-copy-Client-ID](https://github.com/Esri/idp/assets/51384051/96977d9d-071c-414d-96e8-a5336ab23b05)


9. Select Endpoints.


![App-Endpoints](https://github.com/Esri/idp/assets/51384051/23c39bb9-a5d1-4a28-a816-c591eb28d6cf)


10. Copy and open the OpenID Connect metadata document URL in a web browser. Find and save the following values from the metadata in the text document:

      - issuer 
      - authorization_endpoint 
      - token_endpoint
      - jwks_uri
      - end_session_endpoint


![App-well-known-config](https://github.com/Esri/idp/assets/51384051/cca7233e-71a8-45ea-930f-60ee840ac536)


11. Sign in to your ArcGIS web site and navigate to Settings > Security > Logins. Select New Open ID Connect Login.


![ArcGIS-Logins](https://github.com/Esri/idp/assets/51384051/40f9352c-9a55-4ba1-83bc-aed0130676db)


12. Fill in the parameters using the values saved in the text document and select Save.

      - For Let new members join, select Automatically. Joining via invitations is currently unsupported for Azure Entra ID.
      - For Provider scopes/permissions, enter the value: openid profile email.
      - Enable the option: Send access token in header.
      - For ArGIS username field\claim name, enter preferred_username.


![ArcGIS-OIDC-params](https://github.com/Esri/idp/assets/51384051/aa51e4ad-7e57-40a1-be0b-2fb0b7ec190e)


14. Select Configure login next to the OpenID connect login registration you created and scroll down to the end. Copy and save the ArcGIS Login Redirect URI value to the text document.

15. Navigate back to the App registration in the Azure portal. Under Manage, select Authentication, and then select Add URI. Paste the ArcGIS Login Redirect URI here and select Save.


![Azure-Login-URI](https://github.com/Esri/idp/assets/51384051/76c0ab74-04e3-4113-9a11-90a2e9171bdc)


17. Navigate to your ArcGIS web site and select the Microsoft Entra ID login created at Step 12. 


![Sign-in](https://github.com/Esri/idp/assets/51384051/d19aa968-126d-48f6-8c47-faf7183f3f71)


18. Enter your Azure Entra ID user credentials.


![Azure-sign-in](https://github.com/Esri/idp/assets/51384051/30e98790-62d7-43c4-90a3-34cfef0ab05b)


![Azure-sign-in](https://github.com/Esri/idp/assets/51384051/725de3fd-f80d-43a8-9234-cbce19f7601b)


19. Confirm that you are able to sign in, then select Sign Out to confirm a successful user log out.


![ArcGIS-website](https://github.com/Esri/idp/assets/51384051/9858f840-f034-41d3-80f0-0650ba6bc03b)


![Logout](https://github.com/Esri/idp/assets/51384051/3ac117e2-d1de-4058-855c-328dc223f5f6)


# Troubleshoot OpenID Connect login issues

### Examine the Azure id_token

To examine the OpenID Connect id_token issued by Azure Entra ID, use the jwt.ms utility added when registering a new application (See Step 4 in [Setup OpenID Connect logins for ArcGIS](#setup-openid-connect-logins-for-arcgis "Setup OpenID Connect logins for ArcGIS")).

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

![JWT-token](https://github.com/Esri/idp/assets/51384051/f111b159-4b46-40de-96e1-5371a68b0788)


4. Review the value of the iss claim and verify that it [matches](https://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation "OpenID Connect specification for id validation") the issuer id you configured ArcGIS with. If the Tenant IDs do not match, it means that the user attempting to sign in is from a different tenant than the one registered with ArcGIS. ArcGIS currently only supports registration of a single OpenID Connect provider.

*Azure id_token iss claim*

![id-token-iss](https://github.com/Esri/idp/assets/51384051/d1fd89d0-dfd2-4f07-8748-008c2e1b95e5)

*ArcGIS Provider Issuer ID*

![ArcGIS-Issuer-id](https://github.com/Esri/idp/assets/51384051/a7159ce3-5bb4-479f-b013-c205038d6979)

5. Check that the id_token contains the following attributes:
    - email
    - name
    - oid
    - preferred_username
    - sub



