# Troubleshooting OpenID Connect issues

## Back-channel request from ArcGIS to the OpenID connect IdP fails
When logging into ArcGIS using an OpenID connect account, ArcGIS redirects the web browser to the Identity Provider's login page. After successful user authentication with the OIDC IdP, ArcGIS makes a back-channel request to the OIDC IdP to obtain an access token and an ID token. If the back-channel request is successful, the browser redirects to the ArcGIS home page. If the back-channel request fails, the error message "Did not receive required parameter from the provider" or "Did not receive 'user profile' parameter from the provider" will be displayed. To resolve this error, please carefully check the following:

- Verify that ArcGIS can reach the OIDC IdP's token endpoint URL and that the certificate used by the web server hosting the OIDC IdP is issued by a commercial CA. If you are using ArcGIS Enterprise and a local CA authority to generate your IdP's web server certificate, you must import the CA root and intermediate certificates into ArcGIS Enterprise.

- Ensure that the value of the ArcGIS "Client secret" parameter exactly matches the "Client secret" value on the IdP side. 
> [!NOTE]  
> Some OIDC IdPs, have a Client secret name or ID. You cannot use the Client secret name or ID in place of the Client secret value.

- Verify that the ArcGIS "Provider issuer ID" parameter exactly matches the "issuer" property value obtained from the well known OIDC configuration (https://{your_issuer_domain}/{idp_specific_context}/.well-known/openid-configuration).

- Verify that the ArcGIS "Token endpoint URL" parameter exactly matches the "token_endpoint" property value obtained from the well known OIDC configuration (https://{your_issuer_domain}/{idp_specific_context}/.well-known/openid-configuration).

- Verify that the ArcGIS "JSON web key set (JWKS) URL" parameter exactly matches the "jwks_uri" property value obtained from the well known OIDC configuration (https://{your_issuer_domain}/{idp_specific_context}/.well-known/openid-configuration).

## Advanced Troubleshooting of OpenID Connect logins in ArcGIS Enterprise 

 OpenID Connect login failures can be difficult to troubleshoot since communication, to obtain the access and id tokens, occurs directly between ArcGIS Enterprise and the Identity Provider. This topic demonstrates how to capture and analyze the backend communication between ArcGIS Enterprise and the IDP. 

### Overview

  * Setup a proxy server.
  * Configure ArcGIS Enterprise to use a proxy.
  * Analyze the communication between ArcGIS Enterprise and the IDP.

## Setup a proxy server

 You may use any proxy server of your choice; this topic demonstrates troubleshooting using the web debugging proxy, Fiddler.
 
> [!WARNING]
> The workflow and configuration documented in this topic should only be used temporarily for troubleshooting in a development environment.
 
1. ***Run Fiddler on a new host machine, separate from the host machine that is running ArcGIS Enterprise.***

2. Under Tools select Options.

3. Configure Fiddler to allow remote computers to connect, and also to capture and decrypt HTTPS traffic.

    <img src="https://github.com/Esri/idp/assets/51384051/4bdfc91c-97e5-4f08-b617-7de57a90e4bc">
    
    <img src="https://github.com/Esri/idp/assets/51384051/225a3372-3149-49fa-89d3-ea8d99872f39">   
   
4. Export the Fiddler root certificate.

    <img src="https://github.com/Esri/idp/assets/51384051/2e6052b2-3c46-4b4f-a426-ec3644d734e6" width="720">

5. [Import the Fiddler Root certificate into ArcGIS Enterprise as a trusted root CA certificate](https://enterprise.arcgis.com/en/portal/latest/administer/windows/import-a-certificate-into-the-portal.htm).

6. [Configure ArcGIS Enteprise to use Fiddler as a proxy](https://enterprise.arcgis.com/en/portal/latest/administer/windows/using-a-forward-proxy-server-with-portal-for-arcgis.htm). Your configuration should look similar to:

      ``` 
      {"disableSignup":true, 
      "httpProxyHost": "fiddlerhost.domain.com",
      "httpsProxyHost": "fiddlerhost.domain.com",
      "httpProxyPort": 8888,
      "httpsProxyPort": 8888,
      "nonProxyHosts": "enterprisehost.domain.com"}
      ```
7. After ArcGIS Enterprise portal restarts from the configuration changes, your proxy settings and verify that Fiddler is capturing and decrypting traffic. In a new browser window, open:
    ```
      https://portalhost.domain.com/portal/sharing/checkUrl.jsp?url=https://www.esri.com/
    ```

    <img src="https://github.com/Esri/idp/assets/51384051/25d91837-d1aa-4ba2-a195-1289d7fad5ab" width="720">
   
8. Login to ArcGIS Enterprise using an OpenID connect account. In Fiddler, view the network capture corresponding to the OpenID Connect login.

9. If the OpenID connect request failed, you should see a corresponding error message.

    <img src="https://github.com/Esri/idp/assets/51384051/8f8c39f3-f5be-412b-bc69-473601608e5b" width="720">

10. If the OpenID connect request succeeded, but the validation of the token failed, select the id_token, decode it and then examine it carefully to ensure that it matches what is expected. To decode the id_token, make sure to delete the id_token qualifier

    <img src="https://github.com/Esri/idp/assets/51384051/8113b39b-ab60-4e38-a360-953bb8e1fe33" width="720">

    <br>
    <br>

    <img src="https://github.com/Esri/idp/assets/51384051/d2454b45-1d82-42c1-b39f-9f6ad57aee95" width="720">

