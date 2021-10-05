# Configure Active Directory Federation Services

Active Directory Federation Services (AD FS) is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure it in the Microsoft Windows Server operating system as [your IDP for SAML logins](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) in ArcGIS Online. The configuration process involves two main steps: registering your SAML IDP with ArcGIS Online and registering ArcGIS Online with the SAML IDP. 

> **Note:** To ensure that your SAML logins are configured securely, review the [best practices for SAML security](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

## Required information

ArcGIS Online requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The NameID attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS Online work. Since ArcGIS Online uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the user name `NameID_<url_key_for_org>` will be created by ArcGIS Online in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the user name created by ArcGIS Online.

ArcGIS Online supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Register AD FS as the SAML IDP with ArcGIS Online

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the **Logins** section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).

   > **Note:** Selecting the **One identity provider** option allows you to register one SAML IDP for your ArcGIS Online organization. To authenticate users with SAML logins from multiple IDPs, [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) instead of a single IDP.

5. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization. When the user receives the invitation, they can sign in to the organization.

6. Provide metadata information for the IDP using one of the options below:
   - **URL**—If the URL of AD FS federation metadata is accessible, select this option and enter the URL (for example, `https://<adfs-server>/federationmetadata/2007-06/federationmetadata.xml`).
   - **File**—Choose this option if the URL is not accessible. Download or obtain a copy of the federation metadata file from AD FS and upload the file to ArcGIS Online using the **File** option.
   - **Parameters specified here**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your AD FS administrator to obtain these.

7. Configure the advanced settings as applicable:
   - **Encrypt Assertion**—Enable this option to encrypt the AD FS SAML assertion responses.
   - **Enable signed request**—Enable this option to have ArcGIS Online sign the SAML authentication request sent to AD FS.
   - **Propagate logout to Identity Provider**—Enable this option to have ArcGIS Online use a logout URL to sign out the user from AD FS. Enter the URL to use in the **Logout URL** setting. If the IDP requires the logout URL to be signed, **Enable signed request** must be turned on.
       
     > **Note:** By default, AD FS requires logout requests to be signed using SHA-256, so you must enable the **Enable signed request** toggle button and select **Sign using SHA256**.
     
   - **Update profiles on sign in**—Enable this option to automatically synchronize account information (full name and email address) stored in ArcGIS Online user profiles with the latest account information received from the IDP. Enabling this option allows your organization to verify, when a user signs in with a SAML login, whether the IDP information has changed since the account was created and, if so, to update the user's ArcGIS Online account profile accordingly.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based groups to ArcGIS Online groups during the [group creation process](https://doc.arcgis.com/en/arcgis-online/share-maps/create-groups.htm).
   - **Logout URL**—The IDP URL to use to sign out the currently signed-in user.
   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your ArcGIS Online organization to AD FS.

8. Click **Save**.

## Register ArcGIS Online as the trusted service provider with AD FS

1. Open the AD FS management console.

2. Choose **Relying Party Trusts** > **Add Relying Party Trust**.

3. In the **Add Relying Party Trust Wizard**, click the **Start** button

4. For **Select Data Source**, choose one option for obtaining data about the relying party: import from a URL, import from a file, or enter manually.

   URL and file options require that you obtain the metadata from your organization. If you don't have access to the metadata URL or file, you can enter the information manually. In some cases, entering the data manually may be the easiest option.
   
   - **Import data about the relying party published online or on a local network**: This option uses the URL metadata of your ArcGIS Online organization. The URL is `https://<url_key_for_org>.maps.arcgis.com/sharing/rest/portals/self/sp/metadata?token=<token>`, for example, `https://samltest.maps.arcgis.com/sharing/rest/portals/self/sp/metadata?token=G6943LMReKj_kqdAVrAiPbpRloAfE1fqp0eVAJ-IChQcV-kv3gW-gBAzWztBEdFY`.

     Generate a token using https://www.arcgis.com/sharing/rest/generateToken. You must generate a token using HTTPS POST programmatically with JSON output format. For more information, see [ArcGIS REST API](https://developers.arcgis.com/rest/users-groups-and-items/generate-token.htm).
     
   - **Import data about the relying party from a file**: This option uses a `metadata.xml` file from your ArcGIS Online organization. There are two ways you can get a metadata `.xml` file:
     
      - On the organization page, click the **Settings** tab and click **Security** on the left side of the page. In the **Logins** sections, under **SAML login**, click the **Download service provider metadata** button to download the metadata file for your organization.
      - Open the URL of the metadata file and save it as an .xml file on your computer. You can view and copy the URL in the **Edit SAML login** window under **Link to download the service provider metadata**.
     
   - **Enter data about the relying party manually**: With this option, the **Add Relying Party Trust Wizard** displays additional windows where you enter the data manually. These are explained in steps 6 through 8 below.

5. For **Specify Display Name**, enter the display name.

   The display name is used to identify the relying party in AD FS. Outside of this, it doesn’t have any meaning. Set this to either ArcGIS or to the name of the organization within ArcGIS, for example, `ArcGIS—SamlTest`.
   
   > **Tip:** If you chose to import the data source from a URL or file, proceed to **step 9**.

6. **(Manual data source only)** For **Choose Profile**, choose the AD FS profile that's applicable in your environment.

7. **(Manual data source only)** For **Configure URL**, check the **Enable support for the SAML 2.0 WebSSO protocol** box and enter the URL for the relying party SAML 2.0 SSO service.
   The relying party URL must be the URL where AD FS sends the SAML response after authenticating the user. This must be an HTTPS URL: `https://<url_key_for_org>.maps.arcgis.com/sharing/rest/oauth2/saml/signin`.
   
8. **(Manual data source only)** For **Configure Identifiers**, enter the URL for the relying party trust identifier. 

   This must be `<url_key_for_org>.maps.arcgis.com`.
   
9. For **Choose Issuance Authorization Rules**, choose **Permit all users to access this relying party**.

10. For **Ready to Add Trust**, review all the settings for the relying party.

   The metadata URL is only populated if you chose to import the data source from a URL.

   > **Tip:** If the **Monitor relying party** option is enabled, AD FS periodically checks the federating metadata URL and compares it with the current state of the relying party trust. However, monitoring fails once the token in the federating metadata URL expires. Failures are recorded in the AD FS event log. To suppress these messages, it is recommended that you disable monitoring or update the token.

11. Click **Next**.

12. For **Finish**, check the box to automatically open the **Edit Claim Rules** dialog box after you click the **Close** button.

13. To set the claim rules, open the **Edit Claim Rules** wizard and click **Add Rule**.

14. For the **Select Rule Template** step, select the **Send LDAP Attributes as Claims** template for the claim rule you want to create. Click **Next**.

15. For the **Configure Claim Rule** step, follow the instructions below to edit the claims rules.

    a. For **Claim rule name**, provide a name for the rule, such as `DefaultClaims`.
    
    b. For **Attribute store**, select **Active Directory**.
    
    c. For **Mapping of LDAP attributes to outgoing claim types**, select values from the drop-down menus to specify how the LDAP attributes map to the outgoing claim types that are issued from the rule.
    
       Use the following table as a guide:
       
       LDAP attribute | Outgoing claim type
       -------------- | -------------------
       The LDAP attribute that contains the unique user names (for example, **User-Principal-Name** or **SAM-Account-Name**) | **Name ID**
       **Given-Name** | **Given Name**
       **Surname** | **Surname**
       **E-Mail-Addresses** | **E-Mail Address**
       **Token-Groups - Unqualified Names** | **Group**
       
    ![Image-Configure Rule](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/ADFS-Images/Image-Configure%20Rule.jpg)
    
    > **CAUTION:** Manually typing values instead of selecting them from the drop-down menus creates user-defined attributes and could result in errors. For best results, use the drop-down menus to specify values.
    
    With this claim, AD FS sends attributes with the names `givenname`, `surname`, `email`, and `group membership` to ArcGIS Online after authenticating the user. ArcGIS Online then uses the values received in the `givenname`, `surname`, and `email` attributes and populates the first name, last name, and email address of the user account. The values in the group attribute are used to update the user's group membership.
    
    > **Note:** If you selected the **Enable SAML based group membership** option when registering AD FS as the SAML IDP, membership for each user is obtained from the SAML assertion response received from the identity provider every time the user successfully signs in. For information on linking SAML groups, see [Create groups](https://doc.arcgis.com/en/arcgis-online/share-maps/create-groups.htm).
    
16. Click **Finish** to finish configuring the AD FS IDP to include ArcGIS Online as a relying party.
     
     
     
---
**_Please note these instructions apply to both ArcGIS Online and ArcGIS Enterprise._**
       
       
       
   
