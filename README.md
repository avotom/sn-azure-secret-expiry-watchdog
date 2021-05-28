# ServiceNow Azure Secret Expiry Watchdog

This application consists of a scheduled job which gets Azure Service Principals and Applications via Microsoft's Graph API. It then checks the expiry dates of the passwords and certificates to trigger a flow if the expiry is close.

 Settings
 ========
 
 There are some system properties for custom configuration:
 
 | Name | Description |
 | --- | --- |
 | x\_337610\_azure\_ser.azure.service.principal.leadtime.before.expiry  | Put in number of days before expiry the processing flow shall be triggered.  |
 | x\_337610\_azure\_ser.azure.service.principal.past.days.expiry  | Maximum number of days the secret is allowed to be expired to still be considered for creation of tasks. Shall help to avoid creating tasks for long expired secrets which already have been replaced. A value lower than 0 deactivates this check.  |
 | x\_337610\_azure\_ser.azure.service.principal.processinng.flow  | Put in the scope and name of the subflow to use to process a service principal whoose certificate or  password expires soon.(e.g. x\_337610\_azure\_ser.default\_azure\_service\_principal\_processing\_flow)  |
 | x\_337610\_azure\_ser.azure.service.principal.assignment.group  | Put in sys\_id of the assignment group which shall get assigned created tasks for expiring service principals (when using the default flow).  |
 
 Setup
 =====
 
 1.  Commit the update set
 2.  Configure the mentioned system properties
 3.  Go to the outbound REST message "Microsoft Graph API" (/nav\_to.do?uri=%2Fsys\_rest\_message.do%3Fsys\_id%3D63e15b022f4030108e0df64ef699b6bd), choose "OAuth 2.0" as "Authentication type" and select the profile to use to access MS Graph API 
 4.  You may want to adjust the schedule of the scheduled job "Get Service Principals and trigger task creation flow" (/nav\_to.do?uri=%2Fsysauto\_script.do%3Fsys\_id%3Da73fe70a2f8030108e0df64ef699b6eb)
 
 App registration in Azure
 -------------------------
 
 To access MS Graph API and get the data of your Applications and Service Principals you need to register an application and allow access.  
 While doing so make sure to:
 
 *   set the token type to Access tokens
 *   grant permission for "Microsoft Graph / Application.Read.All"
 *   note down/copy the secret
 
 Credential config in ServiceNow
 -------------------------------
 
 1.  Go to Application Registry
 2.  Create a new OAuth application of kind "Connect to an OAuth Provider (simplified)"
 3.  Fill in client ID, secret and token URL from Azure Portal
 4.  Set "Grant type" to "Client Credentials"
 5.  Submit and add an "OAuth Entity Scope" for "[https://graph.microsoft.com/.default](https://graph.microsoft.com/.default)"
 6.  Open the related "OAuth Entity Profile" and add the just created "OAuth Entity Scope"
 7.  Go to the outbound REST message "Microsoft Graph API" choose "OAuth 2.0" as "Authentication type" and select the just created "OAuth profile"
