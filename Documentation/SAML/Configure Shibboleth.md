**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----


# Configure Shibboleth

Shibboleth is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure Shibboleth 3.2x and 3.3.x as your IDP for SAML logins in [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) and [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm). The configuration process involves two main steps: registering your SAML IDP with ArcGIS and registering ArcGIS with the SAML IDP.

> **Note:** To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

## Required information

ArcGIS requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The `NameID` attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS work. Since ArcGIS uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the username `NameID_<url_key_for_org>` will be created by ArcGIS organization in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the username created by ArcGIS.

ArcGIS supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Register Shibboleth as the SAML IDP with ArcGIS

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the Logins section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).

   > **Note:** Selecting the **One identity provider** option allows you to register one SAML IDP for your ArcGIS organization. To authenticate users with SAML logins from multiple IDPs, [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) instead of a single IDP. See [one federation of IDPs](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configure-a-federation-of-identity-providers.htm) for ArcGIS Enterprise instructions. 

5. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization. When the user receives the invitation, they can sign in to the organization.

6. Provide metadata information for the IDP using one of the two options below:

   - **File**—By default, Shibboleth provides the IdP metadata file in `SHIBBOLETH_HOME/metadata`. If the metadata file is accessible, choose the **File** option for the metadata of the SAML IDP and browse to the `SHIBBOLETH_HOME/metadata/idp-metadata.xml` file. ArcGIS validates the signature in SAML assertion responses from Shibboleth using the first signing certificate in the IDP metadata file. However, by default, the first signature listed in the Shibboleth IDP metadata file is for back channel TLS communication, while the second one is for signing assertion responses. You will need to either comment out the first signature or move it below the second one, and use the updated metadata file to register Shibboleth with ArcGIS. The signatures are defined using the `<KeyDescriptor use="signing">` element in the metadata file.

   - **Parameters specified here**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your Shibboleth administrator to obtain these.

7. Configure the advanced settings as applicable:

   - **Encrypt Assertion**—Enable this option if Shibboleth will be configured to encrypt SAML assertion responses.
   
   - **Enable signed request**—Enable this option to have ArcGIS sign the SAML authentication request sent to Shibboleth.

   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your ArcGIS organization to Shibboleth.

   - **Update profiles on sign in**—Enable this option to automatically synchronize account information (full name and email address) stored in ArcGIS user profiles with the latest account information received from the IDP. Enabling this option allows your organization to verify, when a user signs in with a SAML login, whether the IDP information has changed since the account was created and, if so, to update the user's ArcGIS account profile accordingly.

   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based groups to ArcGIS groups during the [group creation process](https://doc.arcgis.com/en/arcgis-online/share-maps/create-groups.htm).

     > **Note:** Currently, Propagate logout to Identity Provider and Logout URL are not supported.

8. Click **Save**.

## Register ArcGIS as the trusted service provider with Shibboleth

1. Configure ArcGIS as a relying party in Shibboleth.

   - Obtain the metadata file of your ArcGIS organization and save it as an XML file.

     To get the metadata file, sign in as an administrator of your organization and open your organization page. Click the **Settings** tab and click **Security** on the left side of the page. In the **Logins** section, under **SAML login**, click the **Download service provider metadata** button.

   - Add ArcGIS as the trusted service provider in Shibboleth by defining a new `MetadataProvider` element in the `SHIBBOLETH_HOME/conf/metadata-providers.xml` file.

     Add the snippet below within the root `MetadataProvider` element. Provide the path to your organization's metadata XML file (saved in step 1 above).
     
     ```
     <MetadataProvider id="EsriSP" xsi:type="FilesystemMetadataProvider"
     xmlns="urn:mace:shibboleth:2.0:metadata" 
  	        xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	          xsi:schemaLocation="urn:mace:shibboleth:2.0:metadata https://shibboleth.net/schema/idp/shibboleth-metadata.xsd"
	          failFastInitialization="true"
	          metadataFile=" <PATH_TO_THE_SAVED_METADATA>/esri-sp-metadata.xml" />
     ```
2. Configure user authentication. In the following example, an LDAP Directory Server is configured as the user store by updating the necessary properties in the `SHIBBOLETH_HOME/conf/ldap.properties:` file.

   `idp.authn.LDAP.authenticator = bindSearchAuthenticator`

   `idp.authn.LDAP.ldapURL = ldaps://myldap.example.org:port`

   `idp.authn.LDAP.baseDN = ou=People,dc=example,dc=org`

   `idp.authn.LDAP.userFilter= (uid={user})`

   `idp.authn.LDAP.bindDN = cn=readonlyuser,dc=example,dc=org`

   `idp.authn.LDAP.bindDNCredential = userpassword`


 
