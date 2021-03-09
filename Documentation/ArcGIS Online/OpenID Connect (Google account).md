# OpenID Connect configuration using Google account

To configure OpenID Connect using a regular Google account, complete the following steps:

1. Go to https://console.developers.google.com and sign in using your Google account.

2. Click Credentials > Create Project. Specify the project name and click Create.

3. Click OAuth consent screen and select external.

4. Specify the application name and add the following scopes if not already added: openid, email, and profile. Click Save.

![Image-OAuth consent screen](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-OAuth%20consent%20screen.png)

5. Click Credentials > Create Credentials > OAuth client ID.

![Image-Create Credentials](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-Create%20Credentials.png)

6. Specify the app name. Click Create.

![Image-Create OAuth client ID](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-Create%20OAuth%20client%20ID.png)

7. Using the client ID and secret generated in the previous step, configure OpenID Connect in your organization as shown.
   - This endpoint provides the URLs necessary for configuration: https://accounts.google.com/.well-known/openid-configuration
  
![Image-Edit OpenID Connect login](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-Edit%20OpenID%20Connect%20login.png)

8. Clicking Save generates a login redirect URI. Copy it from your organization in the Settings > Security > Login section. 

![Image-OpenID Connect login](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-OpenID%20Connect%20login.png)

9. Set the redirect URI in the Google app. Click the Credentials tab and click the edit button for the app you created.

![Image-OAuth 2_0 Client IDs](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-OAuth%202_0%20Client%20IDs.png)

10. Add the redirect URI and specify the generated login redirect URI. Click Save.

![Image-Redirect URIs](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-Redirect%20URIs.png)

11. Go to your organization’s URL, for example, https://myorg.maps.arcgis.com, and click sign in. The option to sign in using OpenID Connect is displayed.

![Image-OpenID sign in](https://github.com/ArcGIS/idp/blob/main/Documentation/ArcGIS%20Online/Images/OpenID%20Connect%20(Google%20account)/Image-OpenID%20sign%20in.png)

12. Clicking the OpenID Sign in button prompts you to sign in to ArcGIS using your Google account using OpenID Connect.

**Notes:**

1.	When precreating users from SAML or OpenID, you need to provide the ID or sub or NameID that uniquely identifies the user on the provider side. This is the main property that binds the user from IDP to esri's system. For Gmail, it is not the first part before @gmail.com. It will be some other unique ID on Google side for your user.

2.	Precreating users with cloud providers like Google can be tricky because this information is not readily available for the general users of Google in the UI unless you make certain API calls to Google. Google has an API explorer, and you can try the steps below if you want to find out the user ID corresponding to your Gmail address: 
    - Go to https://developers.google.com/people/api/rest/v1/people/get and sign in.
    - On the right side, in the ‘Try this API’ section, in the resourceName field, type people/me.
    - For the personFields field, type names.
    - Click Execute. The ID will be in the metadata.source JSON object.
    
3.	For ArcGIS Enterprise providers that are OpenID based, the IDP administrator needs to provide instructions on how to fetch the unique user ID based on the underlying IDP that they use.
