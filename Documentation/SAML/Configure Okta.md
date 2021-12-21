**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----


# Configure Okta

Okta is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure it as your IDP for SAML logins in [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) and [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm). The configuration process involves two main steps: registering your SAML IDP with ArcGIS and registering ArcGIS with the SAML IDP.

> **Note:** To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

## Required information

ArcGIS requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The `NameID` attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS work. Since ArcGIS uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the username `NameID_<url_key_for_org>` will be created by ArcGIS Organization in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the username created by ArcGIS.

ArcGIS supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Register Okta as the SAML IDP with ArcGIS

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the **Logins** section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).

   > **Note:** Selecting the One identity provider option allows you to register one SAML IDP for your ArcGIS organization. To authenticate users with SAML logins from multiple IDPs, [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) instead of a single IDP. See [one federation of IDPs](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configure-a-federation-of-identity-providers.htm) for ArcGIS Enterprise instructions.

5. Choose Automatically or Upon invitation from an administrator to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization (using a [command line utility](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION1_29AF645AF75140698CA9879C3E059D39) for ArcGIS Enterprise users). When the user receives the invitation, they can sign in to the organization.

   > **Tip:** For ArcGIS Enterprise users, it's recommended that you designate at least one SAML account as an administrator of your portal and demote or delete the [initial administrator account](https://enterprise.arcgis.com/en/portal/latest/install/windows/about-the-initial-administrator-account.htm). It is also recommended that you [disable the **Create an account** button](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION2_2D990320EC354A559A7081CF91709894) in the portal website so people cannot create their own accounts. For full instructions, see [Configure a SAML-compliant identity provider with your portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_65AC88E72E2B4CFBBBC061311F9B4EA4).

6. Provide metadata information for the IDP using one of the options below:
   - **File**—Download or obtain a copy of the federation metadata file from Okta and upload the file to ArcGIS using the **File** option.
   
     > **Note:** For ArcGIS Enterprise users, if this is the first time you are registering a service provider with Okta, you will need to get the metadata file after registering ArcGIS Enterprise with Okta.
  
   - **Parameters specified here**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your Okta administrator to obtain these.

7. Configure the advanced settings as applicable:
   - **Encrypt Assertion**—Enable this option to encrypt the Okta SAML assertion responses.
   - **Enable signed request**—Enable this option to have ArcGIS sign the SAML authentication request sent to Okta.
   - **Propagate logout to Identity Provider**—Enable this option to have ArcGIS use a logout URL to sign out the user from Okta. Enter the URL to use in the Logout URL setting. If the IDP requires the logout URL to be signed, Enable signed request needs to be turned on.
   - **Update profiles on sign in**—Enable this option to automatically synchronize account information (full name and email address) stored in ArcGIS user profiles with the latest account information received from the IDP. Enabling this option allows your organization to verify, when a user signs in with a SAML login, whether the IDP information has changed since the account was created and, if so, to update the user's ArcGIS account profile accordingly.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based groups to ArcGIS groups during the group creation process.
   - **Logout URL**—The IDP URL to use to sign out the currently signed in user.
   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your ArcGIS organization to Okta.

    For ArcGIS Enterprise users, the **Encrypt Assertion** and **Enable signed request** settings use the certificate **samlcert** in the portal keystore. To use a new certificate, delete the **samlcert** certificate, create a certificate with the same alias (**samlcert**) following the steps in [Import a certificate into the portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/import-a-certificate-into-the-portal.htm), and restart the portal.
    
8. When finished, click **Save**.

9. Click **Download service provider metadata** to download the portal's or organization's metadata file. Information in this file will be used to register the portal or organization as the trusted service provider with Okta.

## Register ArcGIS as the trusted service provider with Okta

1. Log in to your Okta organization as a member with administrative privileges.

2. On the **Applications tab**, click the **Add Application** button.

3. Click **Create New App** and select the **SAML 2.0** option. Click **Create**.

4. In **General Settings**, enter an **App Name** for your portal or organization deployment and click **Next**.

5. On the **Configure SAML** tab, do the following:

   - Enter the value for **Single sign on URL**, for example, `https://portalhostname.domain.com/portalcontext/sharing/rest/oauth2/saml/signin`. This value can be copied from the service provider metadata file downloaded from your ArcGIS Organization.

   - Enter the value for the **Audience URI**. The default value is set to `portalhostname.domain.com.portalcontext`. This value can be copied from the service provider metadata file downloaded from your ArcGIS Organization.

   - Leave the **Name ID format** as **Unspecified**.

   - Under **Advanced Settings**, change the **Assertion Signature** option to **Unsigned**.
   
   - In the **Attribute Statements** section, add these attribute statements:
   
     `givenName` set to `user.firstName`

     `surname` set to `user.lastName`

     `email` set to `user.email`
    
    - Click **Next** and click **Finish**.

    - You will now see the Sign On section of your newly created SAML application. To get the Okta IDP metadata, click the **Sign On** tab and click the **Identity Provider metadata** link.

    - Right-click the **People** tab and configure which Okta authenticated users will have access to in your ArcGIS Organization.

