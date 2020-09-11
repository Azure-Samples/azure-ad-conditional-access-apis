# Tutorial: Backup and restore Conditional Access policies with approval workflow

Intent: As an IT admin, I want to be able to easily backup-restore Conditional Access policies.

You can use the Conditional Access APIs to easily backup Conditional Access policies. For example, you can:

As a IT admin, be able to automate a regular backup/snapshot of all Conditional Access policies within your environment. Select a policy to Restore. Finally, restore the Conditional Access policy in your production environment.

   ![Manage](./media/configure5.png)

This automation can be very useful for:

- Organizations that manages large numbers of Conditional Access policies.
- Identity partners that manages policies for customers.

This tutorial shows how to build a [logic app](https://docs.microsoft.com/azure/logic-apps/) that allows backup and restore of Conditional Access policies. Specifically, this logic app takes a snapshot of all Conditional Access policies as a scheduled task within OneDrive. Another logic app monitors any policies being queued in the restore folder of OneDrive. If a new policy is detected, an approval workflow is triggered on a Teams channel. On approval, the policy is restored to production environment.

In this tutorial, you learn how to:

:heavy_check_mark: Deploy this logic app to your organization.

:heavy_check_mark: Authenticate your logic app to Azure AD with the right permissions.

:heavy_check_mark: Add parameters and connections specific to your organization within logic app.

When you're done, you will be able to backup-restore Conditional Access policies in your production environment:

1. Select the policy you want to restore from backup and copy to restore OneDrive folder.

   ![Copy to OneDrive](./media/backup-restore-step1.png)

1. Approve restore action in Teams

   ![Approve configuration](./media/backup-restore-step2.png)

1. Receive notification that restore action is successfully completed in your production environment

   ![Confirmation](./media/backup-restore-step3.png)

1. View your restored Conditional Access policy in Azure portal

   ![View policy](./media/backup-restore-step4.png)

1. Check your user assignments are correct within the policy

   ![Assign test users](./media/backup-restore-step5.png)

## Prerequisites

If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you start.

You will also need knowledge of key concepts within Azure Logic Apps, OneDrive and Teams.

Complete the steps in the article [Secure authentication for Conditional Access automation](../../doc/readme.md) to create a Key Vault and connect to Managed Identity.

## Step 1: Deploy this logic app to your organization

If your Azure environment meets the prerequisites, and you're familiar with using Azure Resource Manager templates, these steps help you sign in directly to Azure and open the Azure Resource Manager template in the Azure portal. For more information, see the article [Deploy resources with Azure Resource Manager templates and Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview).

Select the following image to sign in with your Azure account and open the logic app in the Azure portal:

## Logic App for Backup

   [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-ad-conditional-access-apis%2Fmain%2F05-manage%2F01-backup-restore%2Fjson%2Fbackup%2Fazuredeploy.json)

## Logic App for Restore
  
   [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-ad-conditional-access-apis%2Fmain%2F05-manage%2F01-backup-restore%2Fjson%2Frestore%2Fazuredeploy.json)

1. In the portal, on the **Custom deployment** page, enter or select these values:

   | Property | Value | Description |
   |----------|-------|-------------|
   | **Subscription** | <*Azure-subscription-name*> | The name for the Azure subscription to use |
   | **Resource group** | <*Azure-resource-group-name*> | The name for a new or existing Azure resource group. This example uses `AutoPilotConditionalAccess`. |
   | **Location** |  <*Azure-region-for-all-resources*> | The Azure region to use for all resources, if different from the default value. This example uses the default value, `[resourceGroup().location]`, which is the resource group location. |
   | **Logic App Name** | <*logic-app-name*> | The name to use for your logic app. This example uses `301-conditionalaccess-policy-backup-automation` and `301-conditionalaccess-policy-restore-automation`. |

   Here is how the page looks with the values used in this example:

   ![Provide information for quickstart template](./media/deploy.png)

1. When you're done, select **Review + Create** and finally **Create**.

## Step 2: Authenticate your logic app to Azure AD with the right permissions

This logic app uses Managed Identity to access secrets from Key Vault to call the Conditional Access APIs. As a prerequisite you must have completed the steps in the article [Secure authentication for Conditional Access automation](../../doc/readme.md) to create a Key Vault and connect to Managed Identity. To learn more about how to use managed identities within Logic Apps, see the article [**Logic Apps and Managed Identities**](https://docs.microsoft.com/azure/logic-apps/create-managed-service-identity).

1. In the left-hand navigation pane, select Identity > User Assigned > Select Add.

2. Select the User-assigned managed identity from the context pane that appears on the right, select Add.

   ![ManagedIdentity](./media/mi-edit.png)

## Step 3: Update parameters

1. In the left-hand navigation pane, select Logic App designer > Parameters > Replace the default value with Key Vault URI (storing Client Secret), Client ID and Tenant ID.

   ![Parameters](./media/la-parameters-edit.png)

## Step 4: Select appropriate managed identity

1. On the Logic App Designer, in the HTTP connection box, click `GET client secret from key vault using managed identity`. This example uses HTTP connector.

1. Specify the Managed Identity to use.

   ![Select "Managed Identity"](./media/mi-new.png)

## Step 5: Connect to your OneDrive account and select the Backup and Restore folders in respective logic apps you will like to use for automation

1. On the Logic App Designer, in the OneDrive for Business connection box, click `Connections`. This example uses OneDrive connector for Logic apps:

   ![Select "Connections"](./media/onedrive-new.png)

1. If prompted, sign in to your email account with your credentials so that Logic Apps can create a connection to your OneDrive account.

1. If connection is successful, select the OneDrive folder you would like to use for automation.

## Step 6: Connect to Teams channel for approving or rejecting Restore requests

1. On the Logic App Designer, in the Teams connection box, click `Connections`. This example uses Teams connector:

   ![Select "Connections"](./media/teams-new.png)

1. If prompted, sign in to your email account with your credentials so that Logic Apps can create a connection to your Teams account.

1. Specify the Team and channel you will like to use for automation of approval workflow.

## Step 7: Update all other connectors within Logic App

Similar to above, update remaining OneDrive and Teams connectors within the sample Logic App by selecting appropriate OneDrive and Teams account that needs to be used for automation.

> [!WARNING]
> Ensure you follow best practices regarding managing secrets within Logic apps by using secure inputs and outputs as documented in the article, [Secure access and data in Azure Logic Apps]](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app).

## Next steps

Try the following challenge:

:heavy_check_mark: Edit this logic app to send a custom message on Teams channel when the approval workflow selection is Reject restore action.

Next, [Configure emergency account management within Conditional Access policies](../02-emergency-access/readme.md).
