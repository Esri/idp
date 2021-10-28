**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----


# Configure NetIQ Access Manager

NetIQ Access Manager is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure NetIQ Access Manager 3.2 and later versions as your IDP for SAML logins in [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) and [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm). The configuration process involves two main steps: registering your SAML IDP with ArcGIS and registering ArcGIS with the SAML IDP.

> **Note:** To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

## Required information

ArcGIS requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The `NameID` attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS work. Since ArcGIS uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the user name `NameID_<url_key_for_org>` will be created by ArcGIS in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the user name created by ArcGIS.

ArcGIS supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Register NetIQ Access Manager as the SAML IDP with ArcGIS

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the **Logins** section, click the **New SAML login button**, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).

   > **Note:** Selecting the **One identity provider** option allows you to register one SAML IDP for your ArcGIS organization. To authenticate users with SAML logins from multiple IDPs, [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) instead of a single IDP.

5. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization. When the user receives the invitation, they can sign in to the organization.

6. Provide metadata information for the IDP using one of the three options below:
   - **URL**—Choose this option if the URL of NetIQ Access Manager federation metadata is accessible by ArcGIS. The URL is usually `https://<host>:<port>/nidp/saml2/metadata` on the machine where NetIQ Access Manager is running.
   - **File**—Choose this option if the URL is not accessible by ArcGIS. Obtain the metadata from the URL above, save it as an XML file, and upload the file.
   - **Parameters specified here**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your NetIQ Access Manager administrator to obtain these.

7. Configure the advanced settings as applicable:
   - **Encrypt Assertion**—Enable this option if NetIQ Access Manager will be configured to encrypt SAML assertion responses.
   - **Enable signed request**—Enable this option to have ArcGIS sign the SAML authentication request sent to NetIQ Access Manager.
   - **Propagate logout to Identity Provider**—Enable this option to have ArcGIS use a **Logout URL** to sign out the user from Net IQ Access Manager. Enter the URL to use in the **Logout URL** setting. If the IDP requires the **Logout URL** to be signed, **Enable signed request** needs to be turned on.
   - **Update profiles on sign in**—Enable this option to automatically synchronize account information (full name and email address) stored in ArcGIS user profiles with the latest account information received from the IDP. Enabling this option allows your organization to verify, when a user signs in with a SAML login, whether the IDP information has changed since the account was created and, if so, to update the user's ArcGIS account profile accordingly.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based groups to ArcGIS groups during the [group creation process](https://doc.arcgis.com/en/arcgis-online/share-maps/create-groups.htm).
   - **Logout URL**—The IDP URL to use to sign out the currently signed in user. This value is automatically populated if defined in the IDP's metadata file. You can update this URL as needed.
   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your ArcGIS organization to NetIQ Access Manager.

8. Click **Save**.

## Register ArcGIS as the trusted service provider with NetIQ Access Manager

1. Configure an attribute set.

   Follow the steps below to create a new attribute set so the attributes can be sent to ArcGIS as part of the SAML assertion after authenticating the user. If you have an existing attribute set already configured in your NetIQ Access Manager, you can use that set instead.

   - Sign in to the NetIQ Access Manager administration console. This is usually available at `https://<host>:<port>/nps`.
   
   - Browse to your identity server in the NetIQ admin console and click the **Shared Settings** tab. Under **Attribute Sets**, you'll see any attribute sets you've already created. Click **New** and create a new attribute set. Enter `ArcGISOnline` under **Set Name** and click **Next**.
   
     ![Image-Create Attribute Set](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Create%20Attribute%20Set.png)
     
   - Define the attribute mappings and add them to the attribute set you created in the previous step.
   
   Click the **New** link and add any new attribute mappings. The screen captures below show adding attribute mapping for `givenName`, `email address`, and `uid`. You can choose any attributes from your authentication source instead of these examples.
   
   ![Image-Add Attribute Mapping givenName](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Add%20Attribute%20Mapping%20givenName.png)
   
   ![Image-Add Attribute Mapping email](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Add%20Attribute%20Mapping%20email.png)
   
   ![Image-Add Attribute Mapping uid](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Add%20Attribute%20Mapping%20uid.png)
   
   Click **Finish** in the **Create Attribute Set** wizard. This creates a new attribute set named **ArcGISOnline**.

2. Follow the steps below to add ArcGIS as a trusted provider with NetIQ Access Manager.

   - Sign in to the NetIQ admin console, choose your identity server, and click the **Edit** link.
     
     ![Image-Identity Servers](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Identity%20Servers.png)
     
     The **General** tab opens.
   - Click the **SAML 2.0** tab and click **New > Service Provider**.
     
     The **Service Provider** window is where you add ArcGIS as a trusted service provider with NetIQ Access Manager.
   - In the **Create Trusted Service Provider** wizard, click **Metadata Text** as the **Source** and paste the metadata of your ArcGIS organization in the **Text** box.
   
     To get the metadata of your ArcGIS organization, sign in to your organization as an administrator, click the **Settings** tab, click **Security** on the left side of the page. In the **Logins** section, under **SAML login**, click the **Download service provider metadata** button to download the metadata file for your organization.
     
     ![Image-Create Trusted Service Provider](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Create%20Trusted%20Service%20Provider.png)
     
     Click **Next** and click **Finish** to finish adding the trusted service provider.
     
     ![Image-Trusted Providers](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Trusted%20Providers.png)
     
3. Follow the steps below to configure ArcGIS and NetIQ Access Manager federation properties.

   - On the **SAML 2.0** tab, click the service provider link under **Service Providers**. (For example, in the screen capture above, the link is named **ArcGISOnlineSAMLTest**.) The Configuration tab opens. Click the **Metadata** tab and verify that the metadata for your ArcGIS organization is correct.
   
   - Click the **Configuration** tab to go back to the **Trust** section of the configuration. Select the **Encrypt assertions** option if you chose the advanced setting **Encrypt Assertion** when registering NetIQ Access Manager as the SAML IDP with ArcGIS.
   
   - Click the **Attributes** tab.
     
     In this step, you add the attribute mapping from the set you previously created so NetIQ Access Manager can send the attributes to ArcGIS in the SAML assertion.

     Select the attribute set you defined in step 2.1 above. After you select your attribute set, the attributes you defined in the set appear in the **Available** box. Move your givenName and email attributes to the **Send with authentication** box.
     
     ![Image-Configuration Attributes](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Configuration%20Attributes.png)
     
   - Click the **Authentication Response** tab under the **Configuration** tab of the service provider and set up the authentication response.
     
     Click **Post** from the **Binding** drop-down menu.

     In the **Name Identifier** column, check the box next to **Unspecified**.

     In the **Default** column, select the radio button next to **Unspecified**.

     In the **Value** column, choose **Ldap Attribute uid**.
     
     > **Note:** You can configure any other unique attribute in the attribute set from your authentication source to be sent as `NameID`. The value of this parameter will be used as the user name in the organization.
     
     Click **Apply**. Verify that your page matches the screen capture below.
     
     ![Image-Configuration Authentication Response](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Configuration%20Authentication%20Response.png)
     
     Under **Configuration**, click the **Options** tab and choose your user authentication contract, for example, **Name/Password - Form**, and click **Apply**.
     
     ![Image-Configuration Options](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Configuration%20Options.png)
     
4. Restart NetIQ Access Manager by browsing to your identity server and clicking the **Update All** link.

   ![Image-Identity Servers Warning](https://github.com/Esri/idp/blob/main/Documentation/SAML/Images/NetIQAM-Images/Image-Identity%20Servers%20Warning.png)
     
  
   
   
