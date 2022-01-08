**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----


# Configure SimpleSAMLphp

SimpleSAMLphp is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure SimpleSAMLphp 1.10 and later versions as your IDP for SAML logins in [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) and [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm). The configuration process involves two main steps: registering your SAML IDP with ArcGIS and registering ArcGIS with the SAML IDP.

> **Note:** To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

## Required information

ArcGIS requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The `NameID` attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS work. Since ArcGIS uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the username`NameID_<url_key_for_org>` will be created by ArcGIS Organization in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the username created by ArcGIS.

ArcGIS supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Register SimpleSAMLphp as the SAML IDP with ArcGIS

1. Configure an authentication source in the SimpleSAMLphp IdP.
   - Create an authentication source.
   
     SimpleSAMLphp supports authenticating users from various authentication sources such as LDAP server, users in an SQL server, Active Directory domain, and so on. The example below shows how to configure an Apache Directory Server as an authentication source in SimpleSAMLphp IdP.

     Authentication sources can be configured in the `<SimpleSAML_HOME>/config/authsources.php` file. To configure the LDAP server, open the `config/authsources.php` file and add an LDAP-based authentication source in the following format:
     
     ```
     'example-ldapApacheDS' => array(
		        'ldap:LDAP',

		        /* The hostname of the LDAP server. */
		        'hostname' => 'host:port',

		        /* Whether TLS should be used when contacting the LDAP server. */
		        'enable_tls' => TRUE,

	        	/*
		         * Which attributes should be retrieved from the LDAP server.
		         * This can be an array of attribute names, or NULL, in which case
		         * all attributes are fetched.
		         */
		        'attributes' => NULL,

		        /*
		         * The pattern that should be used to create the users DN given the username.
		         * %username% in this pattern will be replaced with the user's username.
		         *
		         * This option is not used if the search.enable option is set to TRUE.
		         */
		        'dnpattern' => 'uid=%username%,ou=users,ou=system',
	        	
            /*
		         * As an alternative to specifying a pattern for the users DN, it is possible to
		         * search for the username in a set of attributes. This is enabled by this option.
		         */
		        'search.enable' => FALSE,

		        /*
		         * The DN that will be used as a base for the search.
		         * This can be a single string, in which case only that DN is searched, or an
		         * array of strings, in which case they will be searched in the order given.
		         */
		        'search.base' => 'ou=users,ou=system',

		        /*
		         * The attribute(s) the username should match against.
		         *
	        	 * This is an array with one or more attribute names. Any of the attributes in
	        	 * the array may match the value the username.
		         */
		        'search.attributes' => array('uid', 'mail'),

		        /*
		         * The username & password the simpleSAMLphp should bind to before searching. If
		         * this is left as NULL, no bind will be performed before searching.
		         */
	        	'search.username' => 'uid=admin,ou=system',
	        	'search.password' => 'password',
	    ),
      
      ```
 
    - Configure the authentication source you created above as an authentication module in the SimpleSAMLphp IdP. Open the `metadata/ saml20-idp-hosted.php` file and add the authentication source to use.
     
      ```
      /*
	           * Authentication source to use. Must be one that is configured in
	           * 'config/authsources.php'.
	           */
	          'auth' => 'example-ldapApacheDS',
      ```
2. Configure the name identifier format supported by the SimpleSAMLphp IDP. Open the `< SimpleSAML_HOME >/metadata/saml20-idp-hosted.php` file and add the snippet below. In the following example, `uid` will be passed as `NameID` by the SimpleSAMLphp IDP to ArcGIS after authenticating the user.

   ```
   'NameIDFormat' => 'urn:oasis:names:tc:SAML:2.0:nameid-format:persistent',
		            'authproc' => array(

			          3 => array(

			          'class' => 'saml:AttributeNameID',
			          'attribute' => 'uid',
	              'Format' => 'urn:oasis:names:tc:SAML:2.0:nameid-format:persistent',
				           ),
		          	 ),
    ```
3. Register SimpleSAMLphp as the IDP for your ArcGIS.
   - Verify that you are signed in as an administrator of your organization.
   - At the top of the site, click **Organization** and click the **Settings** tab.
   - Click **Security** on the left side of the page.
   - In the **Logins** section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).
     
     > **Note:** Selecting the **One identity provider** option allows you to register one SAML IDP for your ArcGIS. To authenticate users with SAML logins from multiple IDPs, [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) instead of a single IDP. See [one federation of IDPs](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configure-a-federation-of-identity-providers.htm) for ArcGIS Enterprise instructions. 

   - Choose if users will be able to join the organization **Automatically** or **Upon invitation from an administrator**. Selecting the first option enables users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization. When the user receives the invitation, they will be able to sign in to the organization.

    - Provide metadata information for the IDP using one of the three options below:
    
      **URL**—Choose this option if the URL of the SimpleSAMLphp federation metadata is accessible. This is usually `https://<simpleSAML-server>/<saml-app-name>/saml2/idp/metadata.php`.

      **File**—Choose this option if the URL is not accessible. Save the metadata from the URL as an XML file and upload the file to ArcGIS using the **File** option.

      **Parameters specified here**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your SimpleSAMLphp administrator to obtain these.

4. Configure the advanced settings as applicable:
   - **Encrypt Assertion**—Enable this option if SimpleSAMLphp will be configured to encrypt SAML assertion responses.
   - **Enable signed request**—Enable this option to have ArcGIS sign the SAML authentication request sent to SimpleSAMLphp.
   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your ArcGIS organization to SimpleSAMLphp.
   - **Propagate logout to Identity Provider**—Enable this option to have ArcGIS use a Logout URL to sign out the user from SimpleSAMLphp. Enter the URL to use in the Logout URL setting. If the IDP requires the Logout URL to be signed, Enable signed request needs to be turned on.
   - **Update profiles on sign in**—Enable this option to automatically synchronize account information (full name and email address) stored in ArcGIS user profiles with the latest account information received from the IDP. Enabling this option allows your organization to verify, when a user signs in with a SAML login, whether the IDP information has changed since the account was created and, if so, to update the user's ArcGIS account profile accordingly.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based groups to ArcGIS groups during the [group creation process](https://doc.arcgis.com/en/arcgis-online/share-maps/create-groups.htm).
   - **Logout URL**—The IDP URL to use to sign out the currently signed in user. The logout URL is usually `https://idphost.domain.com/simplesaml/saml2/idp/SingleLogoutService.php`. This URL is defined in the SingleLogoutService element in the IDP's metadata file. The metadata file URL is usually `https://[simpleSAML-server]/simplesaml/saml2/idp/metadata.php`.

5. Click **Save**.

## Register ArcGIS as the trusted service provider with SimpleSAMLphp

1. Configure ArcGIS as a trusted service provider with SimpleSAMLphp by configuring the `<SimpleSAMLphp_HOME>/metatadata/saml20-sp-remote.php` file.
   - Obtain the metadata XML file of your ArcGIS organization.
     
     To get the metadata file, sign in as an administrator of your organization and open your organization page. Click the **Settings** tab and click **Security** on the left side of the page. In the **Logins** section, under **SAML login**, click the **Download service provider metadata** button.
     
   - Convert the XML file you obtained in the previous substep to PHP format.

     SimpleSAMLphp expects the service provider’s metadata information to be provided in PHP format. SimpleSAMLphp provides a built-in XML-to-PHP metadata converter which, by default, is available as `https://<simpleSAML-server>/<saml-app-name>/admin/metadata-converter.php` in your SimpleSAMLphp installation. Use the converter to convert the XML to PHP.
     
    - Open the `metatadata/saml20-sp-remote.php` file and add the configuration of the service provider in PHP format created in the step above.

      Below is an example of the added service provider's configuration in the metadata file.

      ```
      /* The following is an ArcGIS Online organization service provider */
          $metadata['citygis.maps.arcgis.com'] = array (
          'entityid' => ' citygis.maps.arcgis.com',
          'name' =>
          array (
          'en' => 'citygis ',
          ),
          'description' =>
          array (
          'en' => 'citygis ',
          ),
          'OrganizationName' =>
          array (
          'en' => 'citygis ',
          ),
          'OrganizationDisplayName' =>
          array (
          'en' => 'citygis ',
          ),
          'url' =>
          array (
          'en' => 'https://citygis.maps.arcgis.com',
          ),
          'OrganizationURL' =>
          array (
          'en' => 'https://citygis.maps.arcgis.com',
          ),
          'contacts' =>
          array (
          ),
          'metadata-set' => 'saml20-sp-remote',
          'AssertionConsumerService' =>
           array (
              0 =>
              array (
                  'Binding' => 'urn:oasis:names:tc:SAML:2.0:bindings:HTTPS-POST',
                  'Location' => 'https://citygis.maps.arcgis.com/sharing/rest/oauth2/saml/signin',
                  'index' => 1,
              ),
              1 =>
              array (
                  'Binding' => 'urn:oasis:names:tc:SAML:2.0:bindings:HTTPS-Redirect',
                  'Location' => 'https://citygis.maps.arcgis.com/sharing/rest/oauth2/saml/signin',
                  'index' => 2,
              ),
            ),
            'SingleLogoutService' =>
            array (
            ),
          );
      ```

2. Configure the attribute that gets passed as `NameID` to ArcGIS from the SimpleSAMLphp IdP after authenticating the user. To do this, add the attribute at the end of the service provider’s configuration you added in the previous step.

   In the following example, `uid` is passed as `NameID` by the SimpleSAMLphp IdP to ArcGIS after authenticating the user. (Replace `citygis.maps.arcgis.com` with the URL of your organization.)
   
   ```
   'NameIDFormat'               => 'urn:oasis:names:tc:SAML:2.0:nameid-format:persistent',
   'simplesaml.nameidattribute' => 'uid',

    /* The following indicates whether or not to send all the attributes received from the authentication source to the service provider.
      If true, it will send, otherwise it will not send all the attributes*/

   'simplesaml.attributes'      => true,
   );
   ```

3. If you chose the advanced setting **Encrypt Assertion** when registering SimpleSAMLphp as the SAML IDP, add the attribute below to the end of the service provider’s configuration you added in step 1.
   
   ```
   /*
    Whether assertions sent to this SP should be encrypted. The default value is FALSE.
    */
    'assertion.encryption' => true,
    );
   ```
