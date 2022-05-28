**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----


# Configure Azure Active Directory B2C

Azure Active Directory B2C provides business-to-customer identity as a service. Your customers use their preferred social, enterprise, or local account identities to get single sign-on access to your applications and APIs. You can also use Azure AD B2C as a broker for multiple SAML IdPs, while only registering a single provider (the B2C IdP) with ArcGIS. The configuration process involves two main steps: creating a SAML flow in Azure AD B2C & registering ArcGIS Online with that flow, and registering Azure AD B2C in ArcGIS.

In this guide, we will set up Azure AD B2C to allow customers to sign up and sign in to ArcGIS Online using their own email address and password, or Facebook. Azure AD also provides support for Twitter, WeChat, LinkedIn and others.

> **Note:** To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

> **Note:** The Firefox SAML-tracer add-in is a useful tool to interrogate any errors you encounter. ArcGIS will often give an error "NAME_ID attribute is not available from the identity provider" but this is not usually the root cause. By investigating the SAML response, you will be able to find more detailed error messages.

To configure Azure AD B2C with ArcGIS, you need a premium Azure AD subscription. 

## Generate a SAML user flow and register ArcGIS Online 

Out-of-the-box, Azure AD B2C provides some configurable user flow templates. These templates generate flows allowing users to follow sign up, sign in, password reset or profile editing journeys. These OOTB user flows can be found under Policies > User Flows on the left hand side of the B2C tenant homepage. However, these OOTB user flows do not support SAML, so we must generate a custom user flow instead.

1. Follow the [Create user flows tutorial](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-user-flows?pivots=b2c-custom-policy) to create a basic custom user flow, using the LocalAccounts sample.

2. Follow [these steps](https://docs.microsoft.com/en-us/azure/active-directory-b2c/saml-service-provider?tabs=windows&pivots=b2c-custom-policy) to configure B2C to provide a SAML response to ArcGIS
   > **Note:** When you reach the **Register your SAML application** > **Add the identifier** section of the tutorial, use the format https://tenant-name.onmicrosoft.com/application-id. This is the EntityID which will be shared with ArcGIS Online to ensure that the IdP is talking to the correct relying party. application-id is the client id of your app, found in the App registration screen in the Azure B2C Portal.

3. (Optional) The default username passed to ArcGIS will be the user's Azure AD B2C guid. To use the user's email address as the username instead:
   - In SignUpOrSignIn.xml, under OutputClaims, change `<OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />` to `<OutputClaim ClaimTypeReferenceId="signInNames.emailAddress"  PartnerClaimType="email" DefaultValue=""/>`
   - In SignUpOrSignIn.xml, change `<SubjectNamingInfo ClaimType="objectid" ExcludeAsClaim="true"/>` to `<SubjectNamingInfo ClaimType="email" ExcludeAsClaim="false"/>`
   - In TrustFrameworkBase.xml, search for `<ClaimType Id="email">`. Within its DefaultPartnerClaimTypes, add the element `<Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/email" />`
   - Re-upload and overwrite TrustFrameworkBase.xml and SignUpOrSignIn.xml on the Identity Experience Framework page.


## Register Azure AD B2C as the SAML IDP with ArcGIS

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the **Logins** section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).

   > **Note:** You can only register one SAML IDP, or [one federation of IDPs](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configure-a-federation-of-identity-providers.htm), for your portal. See [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) for ArcGIS Online instructions.

5. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator. Their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to register the necessary accounts with the organization (using a [command line utility](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION1_29AF645AF75140698CA9879C3E059D39) or [sample Python script](https://enterprise.arcgis.com/en/portal/latest/administer/windows/example-add-members-to-the-portal.htm) for ArcGIS Enterprise users). Once the accounts have been registered, users can sign in to the organization.

   > **Tip:** For ArcGIS Enterprise users, it's recommended that you designate at least one account as an administrator and demote or [delete the initial administrator account](https://enterprise.arcgis.com/en/portal/latest/administer/windows/about-the-initial-administrator-account.htm). It is also recommended that you [disable the **Create an account** button](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION2_2D990320EC354A559A7081CF91709894) in the  website so people cannot create their own accounts. For full instructions, see [Configure a SAML-compliant identity provider with your portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_65AC88E72E2B4CFBBBC061311F9B4EA4).

6. Provide metadata information for the IDP using the URL option. 
   - **URL**—https://**b2c-tenant-name**.b2clogin.com/**b2c-tenant-name**.onmicrosoft.com/**policy-name**/Samlp/metadata. 
   > **Note** The policy name will usually be B2C_1A_SIGNUP_SIGNIN_SAML. You can find it under the Identity Experience Framework section in the Azure Portal.

7. Configure the following advanced settings as applicable:
   - **Encrypt Assertion**—Enable this option to prepare ArcGIS to receive an encrypted SAML assertion response from Azure AD. 
   
   > **Note** Encryption of Azure AD B2C assertions is not covered in this guide but [detail on the setting this up can be found here](https://github.com/MicrosoftDocs/azure-docs/blob/main/articles/active-directory-b2c/saml-service-provider-options.md)
   
   - **Enable signed request**—Enable this option to have ArcGIS sign the SAML authentication request sent to Azure AD 
   
   > **Note** [Request signatures are not validated by Azure AD at this time](https://docs.microsoft.com/en-us/azure/active-directory/develop/single-sign-on-saml-protocol#signature).
   
   - **Propagate logout to Identity Provider**—Enable this option to have ArcGIS use a logout URL to sign out the user from Azure AD. Enter the URL to use in the **Logout URL** setting. If the IDP requires the logout URL to be signed, check **Enable Signed Request**.
   - **Update profiles on sign in**—Enable this option to have ArcGIS update users' `givenName` and `email address` attributes if they've changed since they last signed in.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based enterprise groups to ArcGIS groups during the [group creation process](https://enterprise.arcgis.com/en/portal/latest/administer/windows/create-groups.htm).
   - **Logout URL**—The IDP URL is used to sign out the currently signed in user. 
   
   > **Note** You can find this by going to App registrations > **your-app** > Endpoints
   
   - **Entity ID**—Change this to the identifier set when configuring your application manifest in Azure AD: https://your-tenant.onmicrosoft.com/application-id

   The Encrypt Assertion and Enable signed request settings use the certificate samlcert in the portal keystore. To use a new certificate, delete the samlcert certificate, create a new certificate with the same alias (samlcert) following the steps in [Import a certificate into the portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/import-a-certificate-into-the-portal.htm), and restart the portal.
  
8. When finished, click **Update Identity Provider**.
