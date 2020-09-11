# Tutorial: Safe rollout of policies from pre-production to production with approval workflow

Intent: As an IT admin, I want to be able to easily copy-paste Conditional Access policies from pre-production to production environment.

You can use the Conditional Access APIs to easily deploy Conditional Access policies from one environment to another. For example, you can:

As a IT admin, be able to automate a regular snapshot of all Conditional Access policies within pre-production environment. Select a policy to copy from pre-production. Finally, paste the Conditional Access policy in production environment.  

   ![Test](./media/configure2.png)

This automation can be very useful for:

- Organizations that manages large numbers of Conditional Access policies.
- Identity partners that manages policies for customers.

This tutorial shows how to build a [logic app](https://docs.microsoft.com/azure/logic-apps/) that allows copy-paste of Conditional Access policies. Specifically, this logic app takes a snapshot of all Conditional Access policies as a scheduled task within OneDrive. Another logic app monitors any policies being pasted in the production folder of OneDrive. If a new policy is detected, an approval workflow is triggered on Team channel. On approval, the policy is pasted to production environment.

In this tutorial, you learn how to:

:heavy_check_mark: Deploy this logic app to your organization.

:heavy_check_mark: Authenticate your logic app to Azure AD with the right permissions.

:heavy_check_mark: Add parameters and connections specific to your organization within logic app.

When you're done, you will be able to copy-paste Conditional Access policies from pre-production to production environment:

1. Copy your Conditional Access policies from PPE folder and drop it in your PROD OneDrive folder

   ![Copy to OneDrive](./media/copy-paste-step1.png)

1. Approve Copy-Paste in Teams

   ![Approve configuration](./media/copy-paste-step2.png)

1. Receive notification that Copy-Paste action is successfully completed in your production environment

   ![Confirmation](./media/copy-paste-step3.png)

1. View your newly deployed Conditional Access policy in Azure portal

   ![View policy](./media/copy-paste-step4.png)

1. Check your production Ring 0 users are assigned to the policy

   ![Assign test users](./media/copy-paste-step5.png)

## Prerequisites

If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you start.

You will also need knowledge of key concepts within Azure Logic apps, OneDrive and Teams.

Complete the steps in the article [Secure authentication for Conditional Access automation](../../00-prereq/readme.md) to create a Key Vault and connect to Managed Identity.

## Step 1: Deploy this logic app to your organization

If your Azure environment meets the prerequisites, and you're familiar with using Azure Resource Manager templates, these steps help you sign in directly to Azure and open the Azure Resource Manager template in the Azure portal. For more information, see [Deploy resources with Azure Resource Manager templates and Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview).

Select the following image to sign in with your Azure account and open the logic app in the Azure portal:

### Logic App for Export or Copy

   [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-ad-conditional-access-apis%2Fmain%2F02-test%2Fjson%2Fexport-copy%2Fazuredeploy.json)

### Logic App for Import or Paste
  
   [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-ad-conditional-access-apis%2Fmain%2F02-test%2Fjson%2Fimport-paste%2Fazuredeploy.json)
  
1. In the portal, on the **Custom deployment** page, enter or select these values:

   | Property | Value | Description |
   |----------|-------|-------------|
   | **Subscription** | <*Azure-subscription-name*> | The name for the Azure subscription to use |
   | **Resource group** | <*Azure-resource-group-name*> | The name for a new or existing Azure resource group. This example uses `AutoPilotConditionalAccess`. |
   | **Location** |  <*Azure-region-for-all-resources*> | The Azure region to use for all resources, if different from the default value. This example uses the default value, `[resourceGroup().location]`, which is the resource group location. |
   | **Logic App Name** | <*logic-app-name*> | The name to use for your logic app. This example uses `301-conditionalaccess-policy-copy-automation` and `301-conditionalaccess-policy-paste-automation`. |

   Here is how the page looks with the values used in this example:

   ![Provide information for quickstart template](./media/templates-step6.png)

1. When you're done, select **Review + Create** and finally **Create**.

## Step 2: Authenticate your logic app to Azure AD with the right permissions

This logic app uses Managed Identity to access secrets from Key Vault to call the Conditional Access APIs. As a prerequisite you must have completed the steps in the article [Secure authentication for Conditional Access automation](../../00-prereq/readme.md) to create a Key Vault and connect to Managed Identity. To learn more about how to use managed identities within Logic Apps, see the article [**Logic Apps and Managed Identities**](https://docs.microsoft.com/azure/logic-apps/create-managed-service-identity).

1. In the left-hand navigation pane, select Identity > User Assigned > Select Add.

1. Select the User-assigned managed identity from the context pane that appears on the right, select Add.

   ![ManagedIdentity](./media/mi-edit.png)

## Step 3: Update parameters

1. In the left-hand navigation pane, select Logic App designer > Parameters > Replace the default value with Key Vault URI (storing Client Secret), Client ID and Tenant ID.

   ![Parameters](./media/la-parameters-edit.png)

## Step 4: Connect to your OneDrive account and select the PPE and PROD folders in respective logic apps you will like to use for automation

1. On the Logic App Designer, in the OneDrive for Business connection box, click `Connections`. This example uses OneDrive connector for Logic apps:

   ![Select "Connections"](./media/onedrive-new.png)

1. If prompted, sign in to your email account with your credentials so that Logic Apps can create a connection to your OneDrive account.

1. If connection is successful, select the OneDrive folder you would like to use for automation.

## Step 5: Connect to Teams channel for approving or rejecting Copy-Paste requests

1. On the Logic App Designer, in the Teams connection box, click `Connections`. This example uses Teams connector:

![Select "Connections"](./media/teams-new.png)

1. If prompted, sign in to your email account with your credentials so that Logic Apps can create a connection to your Teams account.

1. Specify the Team and channel you will like to use for automation of approval workflow.

## Step 6: Select appropriate managed identity

1. On the Logic App Designer, in the HTTP connection box, click `GET client secret from key vault using managed identity`. This example uses HTTP connector.

1. Specify the Managed Identity to use.

   ![Select "Managed Identity"](./media/mi-new.png)

## Step 7: Update all other connectors within Logic App

Similar to above, update remaining OneDrive and Teams connectors within the sample Logic App by selecting appropriate OneDrive and Teams account that needs to be used for automation.

> [!WARNING]
> Ensure you follow best practices regarding managing secrets within Logic apps by using secure inputs and outputs as documented in the article, [Secure access and data in Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app).

## Next steps

Try the following challenge:

:heavy_check_mark: Edit this logic app to send a custom message on Teams channel when the approval workflow selection is Reject copy-paste action.

:heavy_check_mark: Edit this logic app to delete the policy file in PROD OneDrive folder when the approval workflow selection is Reject copy-paste action.

Next, [Configure One-click deployment of Conditional Access policies to a large number of branch offices and subsidiaries](../03-deploy/readme.md).
