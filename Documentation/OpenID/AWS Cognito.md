# OpenID Connect configuration using AWS Cognito

ArcGIS Online recently added support for OpenID Connect logins, a popular login pattern that can be used to integrate third-party providers and directories. With this update, ArcGIS Online can now be configured to use AWS Cognito as an enterprise identity provider for your ArcGIS Online organization. If you are in a small organization or working in a startup with few IT resources but want to achieve that single sign-on experience for your applications including ArcGIS Online, AWS Cognito can be used as managed identity provider option that can be set up quickly and securely. 

These instructions will help you get started quickly with Cognito and ArcGIS Online. To begin, ensure you have an AWS account, can sign in to the AWS console with full privileges for AWS Cognito, and are an Administrator for your ArcGIS Online organization. Also ensure you have a verified AWS Simple Email Service (SES) email address (for more information, see this [SES Verify Email procedure](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-email-addresses-procedure.html)).

**Creating an AWS Cognito user pool**

**Note:** For this walkthrough, we are assuming you are creating a Cognito user pool from scratch. There are hundreds of configurations for Cognito and this presents one set of options that we have tested. Please follow exactly the settings below for this walkthrough.

A Cognito user pool is a way to provide authentication (identity verification) for your apps as well as your ArcGIS Online organization. In the user pool, you will add users and configure your ArcGIS Online organization as an app client so that your ArcGIS Online organization can authenticate against your AWS Cognito user pool.

1. In the AWS Console, browse to the Cognito Service and click Manage User Pools.

2. Click the Create a user pool button to create a new user pool. This user pool is like a directory, where you will create and manage users, customize the sign-in experience, and configure your app clients like ArcGIS Online.

3. Give your user pool a name and select Step through settings to configure the pool.

4. Configure how you want your end users to sign in, as shown below, and click Next step.

![Image-How do you want your end users to sign in](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-How%20do%20you%20want%20your%20end%20users%20to%20sign%20in.png)

5. Next, customize your policies, like password strength, and click Next step.

6. Set up multifactor authentication. For this walkthrough we accepted the default options and chose Next step.

7. Now set up your message customizations with your SES verified email account and choose the Yes – Use Amazon SES option. Most accounts, if they have not used SES, are put into a Sandbox mode that requires SES verification of the “send to” address. In a production scenario, a request should be made to AWS to remove your account from the Sandbox. For this exercise, ensure that you have SES verified your email, so it is an option in the FROM email address and is selectable from the drop-down menu.

![Image-Do you want to customize your email address](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-Do%20you%20want%20to%20customize%20your%20email%20address.png)

8. Before moving on, you can accept the message customization defaults or change the messaging of the email and SMS templates used for communicating with your users (this can be changed later). Click Next step to continue.

9. Add any AWS tags for your user pool that may be required by your organization, for example, a contact or cost center code for the user pool. Click Next step to continue.

10. If you want, set up the pool to remember your user’s devices and click Next step to continue.

11. Now you will add an app client for your ArcGIS Online organization to connect with. An app client is any application that will use your user pool as an identity provider for authentication. Click Add an app client and give your app client a name. Configure the remaining options as shown below and click Create app client to create. You will dive into this in more detail later. Click Next step to continue.

![Image-Which app clients will have access to this user pool](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-Which%20app%20clients%20will%20have%20access%20to%20this%20user%20pool.png)

12. You will skip the trigger workflows step, but here you can assign custom AWS Lambda functions to be triggered in various steps of the authentication process for customized workflows. Click Next steps to continue.

13. Finally, click Create pool to create the user pool.

**Configuring app integration with ArcGIS Online**

Configuring app integration with ArcGIS Online is a two-step process. First you will configure the user pool with options for your app client. Additionally, you will set up a domain for the URL that ArcGIS Online will use to communicate with your AWS Cognito user pool. Next you will configure ArcGIS Online with the URLs created for the user pool. Then you will update the user pool with URLs generated for callbacks back to ArcGIS Online.

If needed, click the user pool you just created in AWS Cognito.

1. First go to General Settings and note the pool ID and AWS region where you just created your user pool. You will need these to construct URLs for ArcGIS Online to communicate with:
   - Pool ID: ______________________________
   - AWS Region: ___________________________

2. Next go to the App Clients section and note the app client id and app client secret:
   - App client id: ___________________________
   - App client secret: _______________________

3. Next go to the App client settings section. This is where you will customize some settings for your app client. Note that if you created multiple app clients for other applications, you will have multiple sections here for customizing each application setting. For configuring for ArcGIS Online, follow exactly as shown below (you will update the callback and sign-out URLs after configuring ArcGIS Online), and click Save changes when finished:

![Image-App client test](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-App%20client%20test.png)

4. Next move on to the Domain name section. This is the area where you will configure a domain name for the URLs for ArcGIS Online (and any other app clients you configure) to communicate back to your user pool. This domain name is not specific to just one app client but will be used for configuring any applications connecting to your user pool for authentication. 

AWS Cognito provides two options for creating a domain. The first is an Amazon Cognito domain that Cognito will create for you. The second is for a domain you configure yourself through other AWS services such as Route53 and Certificate Manager. For this walkthrough, you’ll let Cognito create one for you. Enter a valid domain prefix such as my-userpool-<pool name>. This prefix will have to be unique for the region being deployed. Click Check availability to verify until you have a domain you can use and click Save changes:

![Image-What domain would you like to use](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-What%20domain%20would%20you%20like%20to%20use.png)

5. Next, you will customize the UI for the login screen your users will use to connect. You can customize a logo and other CSS parameters for the styling of the login screen. For this walkthrough, we have selected an Esri logo and kept the remainder as default. Currently, there is no way to preview the login screen here. You will see it when authenticating from ArcGIS Online.

6. At this point you have configured your app client and set up the remaining requirements for signing in but still need to create a user in your user pool to test with. Browse back up to the Users and groups section of the Cognito console and click the Create user button. Fill in the user name for yourself and create a temporary password (this will be emailed to you with your user name). Set up the remaining details as shown below and click Create user. If you have verified your email with SES, you should receive an email shortly.

![Image-Create user](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-Create%20user.png)

**Configuring ArcGIS Online**

Now you will configure ArcGIS Online to authenticate using OpenID Connect against the AWS Cognito user pool as an identity provider. Before moving on, ensure you have Administrator access to your ArcGIS Online organization. Also note that when you configure this login option, the button will be visible to many of your users. After configuration, OpenID Connect can be disabled easily, but you may have some users confused by the appearance of the new login button.

Complete the information below, which you will need for the configuration of the identity provider. The following items will be needed:

a. **App client id** — You should have noted this in the last section; if not, get it from the User Pool App client section.

b. **App client secret** — You should have noted this in the last section; if not, get it from the User Pool App client section.

c. **Provider scopes/permission** — Enter “email openid profile”. This is from when you configured the app client; you checked this for Allowed OAuth Scopes.

d. Complete the following URLs using your user pool ID, AWS region, and domain prefix to replace the sections in {brackets}:
   - **Provider Issuer ID:** https://cognito-idp.{aws_region}.amazonaws.com/{userpool_id}
   - **OAuth 2.0 authorization endpoint URL:** https://{domain_prefix}.auth.{aws_region}.amazoncognito.com/oauth2/authorize
   - **Token endpoint URL:** https://{domain_prefix}.auth.{aws_region}.amazoncognito.com/oauth2/token
   - **JSON web key set (JWKS) endpoint URL:** https://cognito-idp.{aws_region}.amazonaws.com/{userpool_id}/.well-known/jwks.json
   - **User profile URL can be left blank.** It will be provided by the JWKS endpoint above.

e. **Logout Endpoint** — https://{domain_prefix}.auth.{aws_region}.amazoncognito.com/logout?logout_uri=**https://myorg.maps.arcgis.com** - change this logout_uri parameter to the URL for your ArcGIS Online organization.

Now sign in to your ArcGIS Online organization and browse to Organization > Settings.

1. Click on the Security settings and scroll down to the OpenID Connect login section. Enter the information as shown below using the URLs above. **Ensure you have Let new members join set to Automatically; this will allow your user to be added upon first visitation to the organization site and enable the Send access token in header option at the bottom.**

2. For the Login Button Alias option, you can choose any short string you prefer. This will be what shows up in the button for users, so it can be something like CompanyName Enterprise or CompanyName SSO.

3. After you have filled in the information as shown, click Save to complete.

![Image-Edit OpenID Connect Login](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-Edit%20OpenID%20Connect%20login.png)

4. When complete, you will have two URIs for the login and logout URIs. You will enter these in the app client settings for callback and login URLs in your AWS Cognito user pool.

![Image-OpenID Connect login](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-OpenID%20Connect%20login.png)

5. Update the App client settings URLs for your app client in your AWS Cognito user pool as shown below by pasting in the two URLs from ArcGIS Online and clicking Save changes:

![Image-What identity providers and OAuth settings should be used for your app clients](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-What%20identity%20providers%20and%20OAuth%20settings%20should%20be%20used%20to%20your%20app%20clients.png)

**Verifying the AWS Cognito with ArcGIS Online OpenID Connect setup**

At this point you are ready to validate and test your setup. By now, you should have received an email with the user name and temporary password that you created earlier. If not, you can use the user name and password that you entered to continue the testing.

Ensure you are signed out of the ArcGIS Online organization so that you are taken to the sign-in screen. You should now see a new button with the label you used when configuring the OpenID Connect in your ArcGIS Online organization. The following example shows Esri InCognito is displayed above the existing SAML identify provider.

![Image-Sign in to Regional Services Development Sandbox](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-Sign%20in%20to%20Regional%20Services%20Development%20Sandbox.png)

Use this button to sign in with the AWS Cognito user account that you created. You will be sent to the AWS Cognito login page. You can see the customizations below with the logo update and a few CSS color updates. 

![Image-Sign in dialog](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-Sign%20in%20dialog.png)

After login, Cognito will (in this example) ask you to reset your password and provide the information for name, family name, given name, and so on. This information will be read by ArcGIS Online when automatically creating your account. You will be able to see this in the profile area for the new account that was just created.

![Image-User profile area](https://github.com/Esri/idp/blob/main/Documentation/OpenID/Images/AWS%20Cognito-Images/Image-User%20profile%20area.png)

This point onwards, follow all your regular workflows for providing permissions to an user or organizing into groups. If you have found issues, review the URLs, app IDs, user account, user pool ID, and AWS region information for typos or potential configuration inputs entered incorrectly.

The above walkthrough is to help you get up and running quickly to make that first configuration with ArcGIS Online OpenID Connect and AWS Cognito. You can now proceed with configuring any of the advanced details and settings within Cognito for use in a production use case.

If you would like to retain your settings, you can also disable OpenID Connect from the ArcGIS Online settings page, so that the button is hidden from user view until you’re ready to go live.





