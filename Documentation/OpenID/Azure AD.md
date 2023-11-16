# OpenID Connect configuration using Azure AD

**The configuration described below is for Azure public cloud only. Endpoints for sovereign Azure clouds (Azure Government, China Azure Cloud and Azure Germany) are separate.**

## 

1. Sign in to the Azure portal, https://portal.azure.com/.

2.	Click on the Azure portal menu and select Microsoft Entra ID.


![01-Entra-ID](https://github.com/Esri/idp/assets/51384051/9e77482e-0f03-46a9-a3bc-272dd6f82fc8)


3.	Under Manage, select App registrations, then select New registration.


![02-New-App-Registration](https://github.com/Esri/idp/assets/51384051/bbf6a8c3-5e4a-4d1a-88b4-a94294f50141)


4.	Enter a user-facing display name for the app. Under Redirect URI, select Web as the platform, and then enter https://jwt.ms. Select Register.


![03-App-Details](https://github.com/Esri/idp/assets/51384051/1734018f-1655-4564-a5e1-3a6910bec10f)


5.	Under Manage, select Certificates & secrets.


![04-App-secret](https://github.com/Esri/idp/assets/51384051/431926d4-3957-4c8f-b0ce-554af830c78f)


6.	Select New client secret. Enter a description and expiration date and select Add.


![05-App-secret-details](https://github.com/Esri/idp/assets/51384051/6d1fb9b8-77c5-41bc-a1fe-0856f199c22e)


7. Select the copy icon next to the secret Value and save this in a text document as Registered client secret.

> [!WARNING]
> If you do not copy and save the Secret value at this moment, you will be unable to retrieve it once you navigate away from this page, and will need to create a new secret. You do not need to copy the Secret ID.
 

![06-App-secret-copy](https://github.com/Esri/idp/assets/51384051/32bdeb20-b50a-436b-bc3e-f78c5d4d0213)


8. Select Overview and copy the Application (client) ID value. Using any text\word editor of your choice, save this value, in a text document, as Registered client id.


![07-App-copy-Client-ID](https://github.com/Esri/idp/assets/51384051/96977d9d-071c-414d-96e8-a5336ab23b05)


9. Select Endpoints.


![08-App-Endpoints](https://github.com/Esri/idp/assets/51384051/23c39bb9-a5d1-4a28-a816-c591eb28d6cf)


10. Copy and open the OpenID Connect metadata document URL in a web browser. Find and save the following values from the metadata in the text document:

      - issuer 
      - authorization_endpoint 
      - token_endpoint
      - jwks_uri
      - end_session_endpoint


![09-App-well-known-config](https://github.com/Esri/idp/assets/51384051/cca7233e-71a8-45ea-930f-60ee840ac536)


11. Sign in to your ArcGIS web site and navigate to Settings > Security > Logins. Select New Open ID Connect Login.


![10-ArcGIS-Logins](https://github.com/Esri/idp/assets/51384051/40f9352c-9a55-4ba1-83bc-aed0130676db)


12. Fill in the parameters using the values saved in the text document and select Save.

      - For Let new members join, select Automatically. Joining via invitations is currently unsupported for Azure Entra ID.
      - For Provider scopes/permissions, enter the value: openid profile email.
      - Enable the option: Send access token in header.
      - For ArGIS username field\claim name, enter preferred_username.


![11-ArcGIS-OIDC-params](https://github.com/Esri/idp/assets/51384051/aa51e4ad-7e57-40a1-be0b-2fb0b7ec190e)


14. Select Configure login next to the OpenID connect login registration you created and scroll down to the end. Copy and save the ArcGIS Login Redirect URI value to the text document.

15. Navigate back to the App registration in the Azure portal. Under Manage, select Authentication, and then select Add URI. Paste the ArcGIS Login Redirect URI here and select Save.


![12-Azure-Login-URI](https://github.com/Esri/idp/assets/51384051/76c0ab74-04e3-4113-9a11-90a2e9171bdc)


17. Navigate to your ArcGIS web site and select the Microsoft Entra ID login created at Step 12. 


![14-Sign-in](https://github.com/Esri/idp/assets/51384051/d19aa968-126d-48f6-8c47-faf7183f3f71)


18. Enter your Azure Entra ID user credentials.


![15](https://github.com/Esri/idp/assets/51384051/30e98790-62d7-43c4-90a3-34cfef0ab05b)


![17](https://github.com/Esri/idp/assets/51384051/725de3fd-f80d-43a8-9234-cbce19f7601b)


19. Confirm that you are able to sign in, then select Sign Out to confirm a successful user log out.


![18](https://github.com/Esri/idp/assets/51384051/9858f840-f034-41d3-80f0-0650ba6bc03b)


![19](https://github.com/Esri/idp/assets/51384051/3ac117e2-d1de-4058-855c-328dc223f5f6)



