# OpenID Connect configuration using AWS Cognito

1. Sign in to the AWS Management Console.

2. Create a new user pool
   - In the AWS Management Console's search bar enter "Cognito" and click on the Cognito service.
   - Click "Create user pool".
   - Under "Application type" pick "Traditional web application".
   - Enter a name for your application.
   - Pick your desired sign-in identifiers and required attributes for sign-up.
   - Leave the return URL empty for now and click "Create user directory".

3. Navigate to the User Management > Users tab and create a new user.

4. Navigate to Applications > App clients and pick the client you just created.
   - From the "App client information" section, copy the Client ID and Client secret values and save them to a text file. 

    <img src="https://github.com/user-attachments/assets/64b333fe-e7b0-4b34-b611-04faec29a4aa" width="720">

5. Navigate to the user pool's Overview page.
   - Compose and save to a text file, the URL for the OIDC configuration. The format is typically: <PRE>https://cognito-idp.[region].amazonaws.com/[userPoolId]/.well-known/openid-configuration</PRE>
   - Alternatively, you can copy the "Token signing key URL" and replace the last part "jwks.json" with "openid-configuration".

    <img src="https://github.com/user-attachments/assets/b0a72b46-264b-4710-bac1-16208942cad6" width="720">

6. Navigate to the openid configuration URL.   

    <img src="https://github.com/user-attachments/assets/56dbd0d5-7b7b-426d-bb18-6ff2c952d9ad" width="720">

7. Save the values of the following attributes to your text file
   - issuer
   - authorization_endpoint
   - token_endpoint
   - jwks_uri
   - end_session_endpoint 

8. Login to ArcGIS and navigate to Organization > Settings > Security > Logins.
   - Click "New Open ID Connect login"
   - Enter a name for the Login button label.
   - Set "Let new members join" to "Automatically".
   - For client id and secret, use the values you saved in your text file at Step 4.
   - For Provider scopes/permissions, enter: openid email profile
   - Using the values saved to your text file at Step 7, enter the values for each ArcGIS parameter. You can use the table below as a guide.

      | well-known OIDC attribute | ArcGIS parameter | Sample value |
      |--|--|--| 
      | issuer | Provider issuer ID | https://cognito-idp.us-east-2.amazonaws.com/us-east-2_GmDKBCIBx |
      | authorization_endpoint | OAuth 2.0 authorization endpoint URL | https://us-east-2gmdkbcibx.auth.us-east-2.amazoncognito.com/oauth2/authorize | 
      | token_endpoint | Token endpoint URL | https://us-east-2gmdkbcibx.auth.us-east-2.amazoncognito.com/oauth2/token |
      | jwks_uri | JSON web key set URL | https://cognito-idp.us-east-2.amazonaws.com/us-east-2_GmDKBCIBx/.well-known/jwks.json |
      | end_session_endpoint | Logout endpoint URL | https://us-east-2gmdkbcibx.auth.us-east-2.amazoncognito.com/logout |

> [!CAUTION]
> Please verify that all configuration parameters are accurate. If any parameter is incorrect, the login attempt will fail with the error message
> "Did not receive required parameter from the provider" or "Did not receive 'user profile' parameter from the provider

9. Click Save.

10. Click on "Configure login" next to the saved OpenID Connect login configuration and scroll to the bottom of the page.
    - Copy the ArcGIS Login and Logout redirect URLs.

11. Switch back to AWS Management console for Cognito.

12. Navigate to Applications > App clients and click on your app client name.
    - Click Login pages > Managed login pages configuration > Edit
    - Under "Allowed callback URLs", add the ArcGIS login redirect url
    - Under "Allowed sign-out URLs", add the ArcGIS logout redirect url
    - Under "OpenID Connect scopes", enable Email, OpenID and Profile.
    - Click Save changes.

13. In a new browser window open the ArcGIS home page, and choose the Open ID Connect login button you configured.
> [!NOTE]  
> While testing this workflow, it was noted that logouts clear the user session in both ArcGIS and AWS Cognito. However, AWS Cognito
> does not display a page asking the user to confirm the logout, as required by the OpenID specification. It also appears to not accept
> the standard OIDC logout request and returns an error page with the message "Error code: 400 Bad Request". There may be additional steps required 
> to successfully enable OIDC logouts that are beyond the scope of this guide.
> 
> For more information on the OpenID Connect logout request specification, please see:
> https://openid.net/specs/openid-connect-rpinitiated-1_0.html#RPLogout
