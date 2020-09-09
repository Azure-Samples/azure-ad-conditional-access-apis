# Manage Conditional Access policies with Microsoft Graph

Many organizations have expressed their need to manage as much of their environments like code as possible. Using Microsoft Graph you can treat Conditional Access policies like any other piece of code in your environment.

Microsoft Graph provides a unified programmability model that organizations can use to interact with data in Microsoft 365, Windows 10, and Enterprise Mobility + Security. For more information about Microsoft Graph, see the article, [Overview of Microsoft Graph](/graph/overview).

![An image showing the primary resources and relationships that are part of the graph](./media/microsoft-graph.png)

The following examples are provided as is with no support. You can use these examples as a basis for tooling in your organization.

Many of the following examples use tools like [Managed Identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/), and [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview).

## Configure

### PowerShell

For many administrators, PowerShell is already an understood scripting tool. The following example shows how to use the [Azure AD PowerShell module](https://www.powershellgallery.com/packages/AzureAD) to manage Conditional Access policies.

### Graph API

This example shows the basic Create, Read, Update, and Delete (CRUD) options available in the Conditional Access Graph APIs. The example also includes some JSON templates you can use to create some sample policies.

### Configure using templates

Use Conditional Access APIs to deploy Conditional Access policies in your pre-production environment using a template.

## Test

This example models safer deployment practices with approval workflows that can copy Conditional Access policies from one environment, like pre-production, to another, like your production environment.

## Deploy

This example provides a mechanism to perform a staged deployment Conditional Access policies gradually to your user population, allowing you to manage support impact and spot issues early.

## Monitor

This example provides a mechanism to monitor Conditional Access policy changes over time and can trigger alerts when key policies are changed.

## Manage

### Backup and restore

Automate the backup and restoration of Conditional Access policies with approvals in Teams using this example.

### Emergency access accounts

Multiple administrators may create Conditional Access policies and may forget to add your [emergency access accounts](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) as an exclusion to those policies. This example ensures that all policies are updated to include your designated emergency access accounts.

### Contingency plan

Things don't always work the way you want, when that happens you need a way to get back to a state where work can continue. The following example provides you a way to [revert your policies to a known good contingency plan]([fall back to a contingency policy](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls)) and disable other Conditional Access policies.

https://github.com/videor/AutoPilotConditionalAccess/blob/master/AutoPilotConditionalAccess/azure-quickstart-templates/301-conditionalaccess-contingency-policies-automation/readme.md

## Next steps

- [Conditional Access API](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)
- [Named location API](https://docs.microsoft.com/graph/api/resources/namedlocation?view=graph-rest-1.0)