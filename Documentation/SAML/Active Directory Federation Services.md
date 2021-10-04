# Configure Active Directory Federation Services

Active Directory Federation Services (AD FS) is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure it in the Microsoft Windows Server operating system as [your IDP for SAML logins](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) in ArcGIS Online. The configuration process involves two main steps: registering your SAML IDP with ArcGIS Online and registering ArcGIS Online with the SAML IDP. 

> **NOTE:** To ensure that your SAML logins are configured securely, review the [best practices for SAML security](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

## Required information

ArcGIS Online requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The NameID attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS Online work. Since ArcGIS Online uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the user name `NameID_<url_key_for_org>` will be created by ArcGIS Online in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the user name created by ArcGIS Online.

ArcGIS Online supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Register AD FS as the SAML IDP with ArcGIS Online

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the **Logins** section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).

   > **NOTE:** Selecting the **One identity provider** option allows you to register one SAML IDP for your ArcGIS Online organization. To authenticate users with SAML logins from multiple IDPs, [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) instead of a single IDP.

5. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization. When the user receives the invitation, they can sign in to the organization.

6. Provide metadata information for the IDP using one of the options below:
   - **URL**—If the URL of AD FS federation metadata is accessible, select this option and enter the URL (for example, `https://<adfs-server>/federationmetadata/2007-06/federationmetadata.xml`).
   - **File**—Choose this option if the URL is not accessible. Download or obtain a copy of the federation metadata file from AD FS and upload the file to ArcGIS Online using the **File** option.
   - **Parameters specified here**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your AD FS administrator to obtain these.

7. Configure the advanced settings as applicable:
   - **Encrypt Assertion**—Enable this option to encrypt the AD FS SAML assertion responses.
   - **Enable signed request**—Enable this option to have ArcGIS Online sign the SAML authentication request sent to AD FS.
   - **Propagate logout to Identity Provider**—Enable this option to have ArcGIS Online use a logout URL to sign out the user from AD FS. Enter the URL to use in the **Logout URL** setting. If the IDP requires the logout URL to be signed, **Enable signed request** must be turned on.
     > **NOTE:** By default, AD FS requires logout requests to be signed using SHA-256, so you must enable the **Enable signed request** toggle button and select **Sign using SHA256**.
   - **Update profiles on sign in**—Enable this option to automatically synchronize account information (full name and email address) stored in ArcGIS Online user profiles with the latest account information received from the IDP. Enabling this option allows your organization to verify, when a user signs in with a SAML login, whether the IDP information has changed since the account was created and, if so, to update the user's ArcGIS Online account profile accordingly.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based groups to ArcGIS Online groups during the [group creation process](https://doc.arcgis.com/en/arcgis-online/share-maps/create-groups.htm).
   - **Logout URL**—The IDP URL to use to sign out the currently signed-in user.
   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your ArcGIS Online organization to AD FS.

8. Click **Save**.
