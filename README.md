---
page_type: sample
languages:
- rest
- powershell
products:
- azure
- azure-active-directory
urlFragment: azure-ad-conditional-access-apis
---
# Conditional Access: Programmatic access

Many organizations have expressed their need to manage as much of their environments like code as possible. Using Microsoft Graph you can treat Conditional Access policies like any other piece of code in your environment.

Microsoft Graph provides a unified programmability model that organizations can use to interact with data in Microsoft 365, Windows 10, and Enterprise Mobility + Security. For more information about Microsoft Graph, see the article, [Overview of Microsoft Graph](https://docs.microsoft.com/graph/overview).

![An image showing the primary resources and relationships that are part of the graph](./media/microsoft-graph.png)

The following examples are provided as is with no support. You can use these examples as a basis for tooling in your organization.

Many of the following examples use tools like [Managed Identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/), and [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview).

## Configure

### PowerShell

For many administrators, PowerShell is already an understood scripting tool. The following example shows how to use the [Azure AD PowerShell module](https://www.powershellgallery.com/packages/AzureAD) to manage Conditional Access policies.

- [Configure Conditional Access policies with Azure AD PowerShell commands](./01-configure/powershell/readme.md)

### Graph API

This example shows the basic Create, Read, Update, and Delete (CRUD) options available in the Conditional Access Graph APIs. The example also includes some JSON templates you can use to create some sample policies.

- [Configure Conditional Access policies with Microsoft Graph API calls](./01-configure/graphapi/readme.md)

### Configure using templates

Use Conditional Access APIs to deploy Conditional Access policies in your pre-production environment using a template.

- [Configure Conditional Access policies with Microsoft Graph API templates](./01-configure/templates/readme.md)

## Test

This example models safer deployment practices with approval workflows that can copy Conditional Access policies from one environment, like pre-production, to another, like your production environment.

- [Promote Conditional Access policies from test environments](./02-test/readme.md)

## Deploy

This example provides a mechanism to perform a staged deployment Conditional Access policies gradually to your user population, allowing you to manage support impact and spot issues early.

- [Deploy Conditional Access policies to production environments with approval workflows](./03-deploy/readme.md)

## Monitor

This example provides a mechanism to monitor Conditional Access policy changes over time and can trigger alerts when key policies are changed.

- [Monitor deployed Conditional Access policies for changes and trigger alerts](./04-monitor/readme.md)

## Manage

### Backup and restore

Automate the backup and restoration of Conditional Access policies with approvals in Teams using this example.

- [Manage the backup and restore process of Conditional Access policies using Microsoft Graph API calls](./05-manage/01-backup-restore/readme.md)

### Emergency access accounts

Multiple administrators may create Conditional Access policies and may forget to add your [emergency access accounts](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) as an exclusion to those policies. This example ensures that all policies are updated to include your designated emergency access accounts.

- [Manage the assignment of emergency access accounts to Conditional Access policies using Microsoft Graph API calls](./05-manage/02-emergency-access/readme.md)

### Contingency planning

Things don't always work the way you want, when that happens you need a way to get back to a state where work can continue. The following example provides you a way to revert your policies to a known good contingency plan and disable other Conditional Access policies.

- [Manage the activation of Conditional Access contingency policies using Microsoft Graph API calls](./05-manage/03-contingency/readme.md)

## Community contribution

These samples are available in our [GitHub repository](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). We are happy to support community contributions thorough GitHub Issues and Pull Requests.

## Next steps

- [Overview of Microsoft Graph](https://docs.microsoft.com/graph/overview)
- [Conditional Access API](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)
- [Named location API](https://docs.microsoft.com/graph/api/resources/namedlocation?view=graph-rest-1.0)
