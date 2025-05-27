**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----

### In this article

* [Configure Azure Entra ID](#configure-azure-entra-id "Configure Azure Entra ID")
* [Register Azure Entra ID as the SAML IDP with ArcGIS](#register-azure-entra-id-as-the-saml-idp-with-arcgis "Register Azure Entra ID as the SAML IDP with ArcGIS")
* [Register ArcGIS as the trusted service provider with Azure Entra ID](#register-arcgis-as-the-trusted-service-provider-with-azure-entra-id "Register ArcGIS as the trusted service provider with Azure Entra ID")
* [Workaround the Azure SAML assertion group size limit](#workaround-the-azure-saml-assertion-group-size-limit "Workaround the Azure SAML assertion group size limit")


# Configure Azure Entra ID

Microsoft Azure Entra ID is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure it as your IDP for SAML logins in [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) and [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm) (Portal for ArcGIS on-premises and in the cloud). The configuration process involves two main steps: registering Azure Entra ID in ArcGIS and registering ArcGIS in your Azure Entra ID portal.

> [!NOTE]  
>  To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36).

To configure Azure AD with ArcGIS, you need a premium Azure Entra ID subscription.

## Required information

ArcGIS requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The `NameID` attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS work. Since ArcGIS uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the user name `NameID` will be created by ArcGIS organization in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the user name created by ArcGIS.

ArcGIS supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider.

## Register Azure Entra ID as the SAML IDP with ArcGIS

1. Verify that you are signed in as an administrator of your organization.

2. At the top of the site, click **Organization** and click the **Settings** tab.

3. Click **Security** on the left side of the page.

4. In the **Logins** section, click the **New SAML login** button, and select the **One identity provider** option. On the **Specify properties** page, type your organization's name (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign-in option (for example, `Using your City of Redlands account`).
> [!NOTE] 
> You can only register one SAML IDP, or [one federation of IDPs](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configure-a-federation-of-identity-providers.htm), for your portal. See [register a SAML-based federation](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_STEP_BD2FE74A6D9D41D88499035A69801EE6) for ArcGIS Online instructions.

6. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator. Their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to register the necessary accounts with the organization (using a [command line utility](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION1_29AF645AF75140698CA9879C3E059D39) or [sample Python script](https://enterprise.arcgis.com/en/portal/latest/administer/windows/example-add-members-to-the-portal.htm) for ArcGIS Enterprise users). Once the accounts have been registered, users can sign in to the organization.

> [!TIP]
> For ArcGIS Enterprise users, it's recommended that you designate at least one account as an administrator and demote or [delete the initial administrator account](https://enterprise.arcgis.com/en/portal/latest/administer/windows/about-the-initial-administrator-account.htm). It is also recommended that you [disable the **Create an account** button](https://enterprise.arcgis.com/en/portal/latest/administer/windows/add-members-to-your-portal.htm#ESRI_SECTION2_2D990320EC354A559A7081CF91709894) in the  website so people cannot create their own accounts. For full instructions, see [Configure a SAML-compliant identity provider with your portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_65AC88E72E2B4CFBBBC061311F9B4EA4).

7. Provide metadata information for the IDP using one of the options below:
   - **File**—[Download the Azure Entra ID metadata file](https://docs.microsoft.com/en-us/azure/active-directory/develop/single-sign-on-saml-protocol#download-the-azure-ad-metadata-or-certificate) and upload the file to ArcGIS using the **File** option.

>  [!NOTE] 
> If this is the first time you're registering a service provider with Azure Entra ID, you need to get the metadata file after registering ArcGIS with Azure Entra ID.
    
   - **Parameters**—Choose this option if the URL or federation metadata file is not accessible. Enter the values manually and supply the requested parameters: the login URL and the certificate, encoded in the BASE 64 format. Contact your Azure AD administrator to obtain these.

8. Configure the following advanced settings as applicable:
   - **Encrypt Assertion**—Enable this option to encrypt the Azure Entra ID SAML assertion responses.
   - **Enable signed request**—Enable this option to have ArcGIS sign the SAML authentication request sent to Azure Entra ID.
   - **Propagate logout to Identity Provider**—Enable this option to have ArcGIS use a logout URL to sign out the user from Azure Entra ID. Enter the URL to use in the **Logout URL** setting. If the IDP requires the logout URL to be signed, check **Enable Signed Request**.
   - **Update profiles on sign in**—Enable this option to have ArcGIS update users' `givenName` and `email address` attributes if they've changed since they last signed in.
   - **Enable SAML based group membership**—Enable this option to allow organization members to link specified SAML-based enterprise groups to ArcGIS groups during the [group creation process](https://enterprise.arcgis.com/en/portal/latest/administer/windows/create-groups.htm).
   - **Logout URL**—The IDP URL is used to sign out the currently signed in user.
   - **Entity ID**—Update this value to use a new entity ID to uniquely identify your portal or organization to Azure Entra ID.

   The Encrypt Assertion and Enable signed request settings use the certificate samlcert in the portal keystore. To use a new certificate, delete the samlcert certificate, create a new certificate with the same alias (samlcert) following the steps in [Import a certificate into the portal](https://enterprise.arcgis.com/en/portal/latest/administer/windows/import-a-certificate-into-the-portal.htm), and restart the portal.
  
9. When finished, click **Update Identity Provider**.

10. Click **Download service provider metadata** to download the portal's or organization's metadata file. Information in this file will be used to register the portal or organization as the trusted service provider with Azure Entra ID.

## Register ArcGIS as the trusted service provider with Azure Entra ID

1. Log in to your Azure portal as a member with administrative privileges.

2. Following the steps in Azure documentation, add ArcGIS as a non-gallery application to your Azure Entra ID and configure **Single sign-on**. You will need to provide the `Metadata.xml` file downloaded from ArcGIS.

   ArcGIS appears in the **Enterprise Applications** list in Azure Entra ID. 

3. [Add and assign users to the application](https://docs.microsoft.com/en-us/azure/active-directory/develop/single-sign-on-saml-protocol#assign-users-to-the-application) as needed.

4. Optionally [configure and customize the SAML claims](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-saml-claims-customization) passed to ArcGIS. The attributes of interest in the SAML response are `givenName` and `emailaddress`.

## Workaround the Azure SAML assertion group size limit

In Azure, the number of groups emitted in a SAML assertion is [limited to 150](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/how-to-connect-fed-group-claims). If you have users who are present in more than 150 groups, the SAML assertion response from Azure will not contain group membership. To obtain group membership information for such Azure users, you will need to use one of the following workarounds.

- Use the Azure group filtering options. Add a filter in the advanced section of Azure group claims to ensure the response only includes the groups you want to send to ArcGIS

- Implement [Azure application roles](https://learn.microsoft.com/en-us/entra/identity-platform/enterprise-app-role-management) instead of Azure groups for authorization.

- If you are using Portal for ArcGIS(only available in version 11.3 and higher), configure ArcGIS to retrieve group membership information using the graph API.
>  [!NOTE] 
> The Microsoft Graph API supports the use of the user principal name (UPN) or the object id (OID) of the user to retrieve membership information. Your must use either the UPN or OID as the SAML NAMEID claim.
>
  - Configure your Azure tenant to enable the following permissions for the Microsoft Graph API. For instructions, please refer to the [Azure](https://learn.microsoft.com/en-us/graph/migrate-azure-ad-graph-configure-permissions?tabs=http&pivots=entra-portal-api-permissions) documentation.
    - User.Read.All
	 - GroupMember.Read.All 
  - Configure ArcGIS to ignore the group attribute in the SAML assertion.
     - Sign in to ArcGIS Enterprise as an administrator and click Organization > Settings > Security.
     - In the Logins section, click on your current SAML configuration.
     - Click "Show advanced settings"
     - Disable the "Enable SAML based group membership" option. 
  - Configure ArcGIS with the Azure graph API properties corresponding to your ArcGIS app registration.
    - Sign in to the Portal Administrator Directory as an administrator of your organization. The URL is in the format https://webadaptorhost.domain.com/webadaptorname/portaladmin.
    - Click Security > Configuration > Update Identity Store
    - In the Group store configuration (in JSON format) text box, paste your organization's Azure configuration (in JSON format). 
      ```
      {
        "type": "AZURE",
        "properties": {
      	  "CLIENT_ID": "The Application (client) ID",
      	  "CLIENT_SECRET_PROP": "The client secret value",
      	  "TENANT_ID_PROP": "The Azure Directory (tenant) ID"
        }
      }
      ```
  - Create new groups in ArcGIS that map to your Azure groups.
    - Sign in to ArcGIS Enterprise as an administrator and click Groups > Create group
    - Under "Being a member of an Azure group" enter the Azure Group ID value.
