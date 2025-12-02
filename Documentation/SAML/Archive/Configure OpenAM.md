**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----


# Configure OpenAM

OpenAM is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure OpenAM 10.1.0 and later versions as your IDP for SAML logins in ArcGIS. The configuration process involves two main steps: registering your SAML IDP with ArcGIS and registering ArcGIS with the SAML IDP.

> **Note:** To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

## Required information

ArcGIS requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The `NameID` attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS work. Since ArcGIS uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the username `NameID_<url_key_for_org>` will be created by ArcGIS Organization in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the username created by ArcGIS.

ArcGIS supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Register OpenAM as the SAML IDP with ArcGIS

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the **Logins** section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).

   > **Note:** Selecting the **One identity provider** option allows you to register one SAML IDP for your ArcGIS organization. To authenticate users with SAML logins from multiple IDPs, [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) instead of a single IDP. See [one federation of IDPs](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configure-a-federation-of-identity-providers.htm) for ArcGIS Enterprise instructions. 

5. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization (using a [command line utility](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION1_29AF645AF75140698CA9879C3E059D39) for ArcGIS Enterprise users). When the user receives the invitation, they can sign in to the organization.

   > **Tip:** For ArcGIS Enterprise users, it's recommended that you designate at least one SAML account as an administrator of your portal and demote or delete the [initial administrator account](https://enterprise.arcgis.com/en/portal/latest/install/windows/about-the-initial-administrator-account.htm). It is also recommended that you [disable the **Create an account** button](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION2_2D990320EC354A559A7081CF91709894) in the portal website so people cannot create their own accounts. For full instructions, see [Configure a SAML-compliant identity provider with your portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_65AC88E72E2B4CFBBBC061311F9B4EA4).

6. Provide metadata information for the IDP using one of the three options below:

   - **URL**—Choose this option if the URL of OpenAM federation metadata is accessible by ArcGIS. The URL is usually `https://<host>:<port>/openam/saml2/jsp/exportmetadata.jsp`.
  
       > **Note:** For ArcGIS Enterprise users, if your SAML IDP includes a self-signed certificate, you may encounter an error when attempting to specify the HTTPS URL of the metadata. This error occurs because ArcGIS Enterprise cannot verify the IDP's self-signed certificate. Alternatively, use HTTP in the URL, one of the other options below, or configure your IDP with a trusted certificate.
       
   - **File**—Choose this option if the URL is not accessible by ArcGIS. Obtain the metadata from the URL above, save it as an XML file, and upload the file.
   
   - **Parameters specified here**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your OpenAM administrator to obtain these.

7. Configure the advanced settings as applicable:
  
   - **Encrypt Assertion**—Enable this option if OpenAM will be configured to encrypt SAML assertion responses.
   - **Enable signed request**—Enable this option to have ArcGIS sign the SAML authentication request sent to OpenAM.
   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your ArcGIS organization to OpenAM.
   - **Update profiles on sign in**—Enable this option to automatically synchronize account information (full name and email address) stored in ArcGIS user profiles with the latest account information received from the IDP. Enabling this option allows your organization to verify, when a user signs in with a SAML login, whether the IDP information has changed since the account was created and, if so, to update the user's ArcGIS account profile accordingly.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based groups to ArcGIS groups during the group creation process.

    For ArcGIS Enterprise users, the **Encrypt Assertion** and **Enable signed request** settings use the certificate **samlcert** in the portal keystore. To use a new certificate, delete the **samlcert** certificate, create a certificate with the same alias (**samlcert**) following the steps in [Import a certificate into the portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/import-a-certificate-into-the-portal.htm), and restart the portal.
    
     > **Note:** Currently, **Propagate logout to Identity Provider** and **Logout URL** are not supported.
  
   - Click **Save**.

## Register ArcGIS as the trusted service provider with OpenAM

1. Configure a hosted IDP in OpenAM.
   
   - Sign in to the OpenAM administration console. This is usually available at `https://servername:port/<deploy_uri>/console`.
  
   - On the **Common Tasks** tab, click **Create Hosted Identity Provider**.
  
   - Create a hosted IDP and add it to a **Circle of Trust**. You can add it to an existing circle of trust if you already have it or create a new circle of trust.

   - By default, the hosted IDP works with OpenDJ, the embedded user store that comes with OpenAM. If you want to connect OpenAM to any other user stores such as Active Directory, you need to create a new data source on the **Access Control** tab of the mainOpenAM administration console.

2. Configure ArcGIS as a trusted service provider with OpenAM.

   - Obtain the metadata file of your ArcGIS organization and save it as an XML file.
  
   - To get the metadata file, sign in as an administrator of your organization and open your organization page. Click the **Settings** tab and click **Security** on the left side of the page. In the **Logins** section, under **SAML login**, click the **Download service provider metadata** button.
  
   - In the OpenAM administration console under **Common Tasks**, click **Register Remote Service Provider**.
  
   - Select the **File** option for the metadata and upload the metadata XML file saved in the previous step.
  
   - Add this service provider to the same circle of trust to which you added your IDP.
  
3. Configure the `NameID` format and attributes that OpenAM needs to send to ArcGIS after authenticating the user.
  
   - In the OpenAM administration console, click the **Federation** tab. The tab contains the circle of trust you previously added and the service and IDP.

   - Under **Entity Providers**, click your IDP.

   - On the **Assertion Content** tab, under **Name ID Format**, verify that `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` is listed at the top. This is the format of `NameID` that ArcGIS will request in its SAML request to OpenAM.

   - Under **Name ID Value Map**, map an attribute from the user's profile, such as `mail` or `upn`, that will be returned as `NameID` to ArcGIS after the user is authenticated.

     Example: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified =upn`

   - Click the **Assertion Processing** tab in the IDP. Under **Attribute Mapper**, configure attributes from the user profile that you want to be sent to ArcGIS.

     Click **Save** to save the `NameID` format and the attribute content changes.

   - On the **Federation** tab of the OpenAM administration console, browse to the ArcGIS service provider under **Entity Providers**.

   - On the **Assertion Content** tab, under **Encryption**, select the **Assertion** option if you chose the advanced setting **Encrypt Assertion** when registering OpenAM as the SAML IDP with ArcGIS.

   - Under **Name ID Format**, verify that `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` is listed at the top. This is the format of `NameID` that ArcGIS will request in its SAML request to OpenAM.

   - Click the **Assertion Processing** tab in the IDP. Under **Attribute Mapper**, configure attributes from the user profile that you want to be sent to ArcGIS.

   - Click **Save** to save the **Name ID Format** and the attribute content changes.

4. Restart the web server where OpenAM is deployed.
