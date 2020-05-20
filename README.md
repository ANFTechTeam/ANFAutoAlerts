# ANFAutoAlerts
### An Azure Logic App that automates the creation and updating of capacity based alerts for Azure NetApp Files.

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

1. Click the button above to deploy this Logic App to Azure. Complete the following fields:

	* Resource group *, this is where the Logic App will live
	* Location *, this is the region where your Logic App will be deployed
	* Logic App Name, any name you would like, the default is recommended
	* Target Resource Group for Alerts, new alerts will be created here
	* Target Resource Group to Monitor, this is the resource group that we will monitor for new ANF resources
	* Subscription ID, this is needed to build some of the framework
	* Action Group for Notification, this is the action group that will be used for capacity based alert. This should be pre-created. 

2. Give your new Logic App permission to create and modify resources within your resource group(s): Navigate to Resource groups, choose your resource group that will contain your Azure NetApp Files resources. Choose 'Access control (IAM)' from the menu. Click the '+ Add' button and choose 'Add role assignment'. For the 'Role', choose Owner. For 'Assign access to', choose Logic App, now select 'ANFAutoAlerts'. Finally, click the 'Save' button.

3. Run the Logic App manually to build the supporting resources: Navigate to your Logic App and choose Run Trigger, Manual
   Running the Logic App manually kicks off a special workflow that does the following:
   
    * Creates an Alert Group called 'ANFAA_LogicAppTrigger', to be called when a new volume or capacity pool is created
    * Creates an Alert called 'ANFAA_VolumeModified' to trigger whenever a volume is created or modified
    * Creates an Alert called 'ANFAA_PoolModified' to trigger whenever a capacity pool is created or modified

4. Modify the ANFAutoAlerts Logic App capacity threshholds as needed: Navigate back to Logic Apps, choose your Logic App, and select 'Logic app designer'.
	
	Modify the two variables as needed:
    * Set Capacity Pool Alert Percentage (consumed)
       * default is 0.8 (80%)
       * leading zero (0) is required
       * This is the consumed threshold for Capacity Pools that will fire the alert
    * Set Volume Alert Percentage (consumed)
       * default is 0.8 (80%) 
       * leading zero (0) is required
       * This is the consumed threshold for Volumes that will fire the alert
  

That's it! When you create (or modify) a capacity pool or volume, the Logic App will automatically create (or modify) a capacity based alert with the name 'ANFAA\_Pool\_*poolname*' or 'ANFAA\_Volume\_*poolname*_*volname*'.

