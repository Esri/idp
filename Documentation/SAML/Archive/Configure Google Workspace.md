**_Please note the term 'ArcGIS' in the following document refers to both ArcGIS Online and ArcGIS Enterprise._**

----


# Configure Google Workspace

Google Workspace (previously known as G Suite) is a Security Assertion Markup Language (SAML)-compliant identity provider (IDP). You can configure it as your IDP for SAML logins in [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm) and [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm). The configuration process involves two main steps: registering your SAML IDP with ArcGIS and registering ArcGIS with the SAML IDP.

> **Note:** To ensure that your SAML logins are configured securely, review the best practices for SAML security in [ArcGIS Online](https://doc.arcgis.com/en/arcgis-online/administer/saml-logins.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) and [ArcGIS Enterprise](https://enterprise.arcgis.com/en/portal/latest/administer/windows/configuring-a-saml-compliant-identity-provider-with-your-portal.htm#ESRI_SECTION1_E8C7F86C02A04A778878B1327C633B36) 

## Required information

ArcGIS requires certain attribute information to be received from the IDP when a user signs in using SAML logins. The `NameID` attribute is mandatory and must be sent by your IDP in the SAML response to make the federation with ArcGIS work. Since ArcGIS uses the value of `NameID` to uniquely identify a named user, it is recommended that you use a constant value that uniquely identifies the user. When a user from the IDP signs in, a new user with the user name `NameID_<url_key_for_org>` will be created by ArcGIS in its user store. The allowed characters for the value sent by `NameID` are alphanumeric, _ (underscore), . (dot), and @ (at sign). Any other characters will be escaped to contain underscores in the user name created by ArcGIS.

ArcGIS supports the inflow of a user's email address, group memberships, given name, and surname from the SAML identity provider. It's recommended that you pass in the email address from the SAML IDP so the user can receive notifications. This helps if the user later becomes an administrator. Having an email address in the account entitles the user to receive notifications regarding any administrative activity and send invitations to other users to join the organization.

## Configure SAML logins

> **Note:** The following steps require a Google account with super administrator privileges for the Google Workspace subscription.

1. Log in to your Google Admin console (`https://admin.google.com`) to access the administrator dashboard.

2. From the Admin console Home page, go to **Apps**.

   > **Note:** To see Apps on the Home page, you might need to click More controls at the bottom. You can also use the main menu to access Apps.

3. Click **SAML Apps** and click the plus (+) button in the bottom corner.

4. In the window that appears, click **Set up My Own Custom App**.

5. In the **Google IdP Information** window, for **Option 2**, click **Download** to download the IDP metadata. Save this file to a location on your local storage.

6. Click **Next**.

7. Open a new browser window and go to your ArcGIS account.

8. Verify that you are signed in as an administrator of your organization.

9. At the top of the site, click **Organization** and click the **Settings** tab.

10. Click **Security** on the left side of the page.

11. In the **Logins** section, under **SAML login**, click the **Set up SAML login** button and select the **One identity provider** option. Type your organization's name in the window that appears (for example, `City of Redlands`). When users access the organization website, this text displays as part of the SAML sign in option (for example, `Using your City of Redlands account`).

12. Choose **Automatically** or **Upon invitation from an administrator** to specify how users can join the organization. Selecting the first option allows users to sign in to the organization with their SAML login without any intervention from an administrator; their account is registered with the organization automatically the first time they sign in. The second option requires the administrator to invite the necessary users to the organization. When the user receives the invitation, they can sign in to the organization.

13. Provide metadata information for the IDP by choosing the **File** option. Use the IDP metadata file that you downloaded earlier.

14. Leave the advanced settings unchanged and click **Save**.

15. Click **Download service provider metadata** and save the service provider metadata file locally.

    Information in this file will be used to register the organization as the trusted service provider with Google Workspace.

16. Open the file in a text editor, such as Notepad.

17. Return to the browser window for the Google Admin console. In the **Basic Information for your Custom App** window, type a name for your application and click **Next**.

18. In the **Service Provider Details** window, complete the following parameters using the content from the organization's metadata file:
   
    - For **ACS URL**, copy the value of the **AssertionConsumerService** element.

       Example: `https://[org name].maps.arcgis.com/sharing/rest/oauth2/saml/signin` (ArcGIS Online) or `https://webadaptorhost.domain.com/webadaptorname/sharing/rest/oauth2/saml/signin` (ArcGIS Enterprise)

    - For **Entity ID**, copy the value of the `entityID` attribute.
       
       Example: `[org name].maps.arcgis.com` (ArcGIS Online) or `portal.domain.com.arcgis` (ArcGIS Enterprise)

    - For **Start URL**, copy the value of the **OrganizationURL** attribute.
       
       Example: `https://[org name].maps.arcgis.com` (ArcGIS Online) or `portal.domain.com.arcgis` (ArcGIS Enterprise)

    - Leave **Name ID** as **Basic Information Primary Email** (or another unique attribute that your Google Workspace subscription uses).

    - Leave **Name ID Format** as **Unspecified**.

19. Click **Next**.

20. In the **Attribute Mapping** window, type givenName, choose **Basic Information**, and then choose **First Name**.

21. In the **Attribute Mapping** window, type surName, choose **Basic Information**, and then choose **Last Name**.

22. Click **Finish** to complete the configuration.

23. Click **Edit Service**. For **Service status**, choose **On for everyone** and click **Save**.
