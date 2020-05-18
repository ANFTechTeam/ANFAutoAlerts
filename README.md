# ANFAutoAlerts
####An Azure Logic App that automates the creation and updating of capacity based alerts for Azure NetApp Files.

Things it does...

1. When an Azure NetApp Files Capacity Pool is created, ANFAutoAlerts creates an alert rule based on the specified percent capacity consumed.
2. When and Azure NetApp Files Volume is created, ANFAutoAlerts creates an alert rule based on the specified percent capacity consumed.
3. When an Azure NetApp Files Capacity Pool is resized, ANFAutoAlerts modifies an alert rule based on the specified percent capacity consumed. If the alert rule does not exist, it will be created.
4. When and Azure NetApp Files Volume is resized, ANFAutoAlerts modifies an alert rule based on the specified percent capacity consumed. If the alert rule does not exist, it will be created.

Things is does not do, that I would like it to do in the future...

1. Delete alert rules when a capacity pool or volume is deleted.
2. <del>Fully</del> More automated deployment.
3. **Suggestions?**

# Installation
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FANFTechTeam%2FANFAutoAlerts%2Fmaster%2Fanfautoalerts.json)

1. Click the button above to deploy this Logic App to Azure.

2. Give your new Logic App permission to create and modify resources within your resource group: Navigate to Resource groups, choose your resource group that will contain your Azure NetApp Files resources. Choose 'Access control (IAM)' from the menu. Click the '+ Add' button and choose 'Add role assignment'. For the 'Role', choose Owner. For 'Assign access to', choose Logic App, now select 'ANFAutoAlerts'. Finally, click the 'Save' button.

3. Get your Logic App's webhook URL: Navigate to your newly deployed Logic App titled ANFAutoAlerts, select 'Logic app designer' under 'Development Tools'. In the main designer pane, click the first box titled "When a HTTP request is recieved". Copy the 'HTTP POST URL' to your clipboard or text editor of your choice, you'll need it for the next step.

4. Create the primary action group: Navigate to 'Alerts', choose 'Manage actions', and 'Add action group'.
	* Action group name: ANFAA_ActionGroup
	* Short name: ANFAA_AG
	* Resource group: Choose the same resource group as step 2
	* Action name: ANFAutoAlerts
	* Action Type: Webhook
	* URI: paste the URI from step 5 here
	* Enable the common alert schema: change this to 'Yes'
	* Click OK
	* Click OK again

5. Create the Capacity Pool alert rule: Go back to 'Alerts' and choose '+ New alert rule', click on 'Select resource', use 'Filter by resource type' and choose 'Capacity Pools', select your Resource group from step 2 and click 'Done'. Click 'Select condition', and choose 'Write pool resource...', change the 'Status' to 'Succeeded' and click 'Done'.

	* Alert rule name: ANFAA_CapacityPool
	* Description: Calls the ANFAutoAlerts Logic App when a Capacity Pool is created or modified.
	* Save alert to resource group: choose the resource group from step 2.
	* Enable alert rule upon creation: leave this checked

	Click 'Select action group' and select 'ANFAA_ActionGroup'.

6. Create the Volume alert rule: Go back to 'Alerts' and choose '+ New alert rule', click on 'Select resource', use 'Filter by resource type' and choose 'Volumes', select your Resource group from step 2 and click 'Done'. Click 'Select condition', and choose 'Write volume resource...', change the 'Status' to 'Succeeded' and click 'Done'.

	* Alert rule name: ANFAA_Volume
	* Description: Calls the ANFAutoAlerts Logic App when a Volume is created or modified.
	* Save alert to resource group: choose the resource group from step 2.
	* Enable alert rule upon creation: leave this checked

	Click 'Select action group' and select 'ANFAA_ActionGroup'

	These two alert rules will call our logic app when a capacity pool or volume is created or modified.

7. Create the secondary action group (easier if it does not have any spaces in the name) that will be used to notify you and/or your staff when a capacity pool or volume has reached the specified percent full amount. This action group could send an email, sms, or any other notification method that suites you. Once you have it created, take note of the name. If you already have one created, you can skip this step.

8. Configure the ANFAutoAlerts Logic App: Navigate back to Logic Apps, choose 'ANFAutoAlerts', and select 'Logic app designer'.
	
	Modify the four variables:
    * Set Capacity Pool Alert Percentage (consumed)
       * default is 0.8 (80%)
       * leading zero (0) is required
       * This is the consumed threshold for Capacity Pools that will fire the alert
    * Set Volume Alert Percentage (consumed)
       * default is 0.8 (80%) 
       * leading zero (0) is required
       * This is the consumed threshold for Volumes that will fire the alert
    * Set Existing Action Group for Alerts
    	* Specify the name of the action group you created in step 7.

    * Set Target Resource Group for Alerts
       * Specify the resource group from step 2.

That's it! When you create (or modify) a capacity pool or volume, the Logic App will automatically create (or modify) a capacity based alert with the name 'ANFAA\_Pool\_*poolname*' or 'ANFAA\_Volume\_*volname*'.

