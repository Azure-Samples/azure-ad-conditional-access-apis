# Configure Conditional Access policies using Azure AD PowerShell

You can use the Conditional Access APIs to manage policies at scale. For example, you can:

- Get a snapshhot of all Conditional Access policies within your organization and take backups.
- Update a Conditional Access policy to add new groups as you rollout your policies within your organization.
- Update the list of applications you are targeting within the Conditional Access policies as new line of business application are added in your organization.
- Delete a policy that is no longer needed.

For more information about Conditional Access policy components, see the article [Components of CA policy](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies).

In this tutorial, you'll learn how to:

:heavy_check_mark: Authenticate with the right role or permissions

:heavy_check_mark: Connect to Azure AD PowerShell

:heavy_check_mark: Create a Conditional Access policy

:heavy_check_mark: List all Conditional Access policies

:heavy_check_mark: Get a specific Conditional Access policy

:heavy_check_mark: Update a Conditional Access policy

:heavy_check_mark: Delete a Conditional Access policy

## Step 1: Authenticate

### Choose the right role

If you are using delegated permissions flow, sign in using an account with one of these roles:

For read and write to Conditional Access policies:

- Global administrator
- [Conditional Access administrator](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#conditional-access-administrator-permissions) ***Recommended***
- Security administrator

For reading Conditional Access policies:

- [Security Reader permissions](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader-permissions)
- [Global Reader permissions](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#global-reader-permissions)

### Specific permissions

All of the following permissions are required to call the read and write API. To learn more, including how to choose permissions, see the article [Microsoft Graph permissions reference](https://docs.microsoft.com/graph/permissions-reference).

| Permission type | Permissions (all three permissions below are required for read and write operations) |
| --- | --- |
| Delegated (work or school account) | Policy.Read.All, Policy.ReadWrite.ConditionalAccess, and Application.Read.All |
| Delegated (personal Microsoft account) | Not supported. |
| Application | Policy.Read.All, Policy.ReadWrite.ConditionalAccess, and Application.Read.All |

One of the following permissions is required to call the read API. To learn more, including how to choose permissions, see the article [Microsoft Graph permissions reference](https://docs.microsoft.com/graph/permissions-reference).

| Permission type | Permissions |
| --- | --- |
| Delegated (work or school account)     | Policy.Read.All |
| Delegated (personal Microsoft account) | Not supported. |
| Application                            | Policy.Read.All |

## Step 2: Connect to Azure AD PowerShell module

Connect to the Azure Active Directory V2 PowerShell Module. A minimum version of [2.0.2.106](https://www.powershellgallery.com/packages/AzureAD/2.0.2.106) is required.

To install the most recen version, run Install-Module and add the -Force parameter, this command will install version '2.0.2.106', as of the initial publishing of this sample, side-by-side with any previous version.

```powershell
Install-Module -Name AzureAD -Force
```

Once completed, connect to Azure AD.

```powershell
Connect-AzureAD
```

To get help with Conditional Access policy cmdlets use the following Get-help command.

```powershell
Get-help AzureADMSConditionalAccessPolicy
```

## Step 3: Create a Conditional Access Policy

In this example, we will copy the policy documented in the article [Conditional Access: Sign-in risk-based Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk) using PowerShell commands instead of creating it manually in the Azure portal. We will set the policy to be in report-only mode so we can evaluate the impact before enabling it.

We will create and name the policy "CA002: Require MFA for medium + sign-in risk" using PowerShell.

To get help with New Conditional Access policy creation cmdlets, use the following `Get-help` command.

```powershell
Get-help New-AzureADMSConditionalAccessPolicy
```

To create a Conditional Access policy, use the `New-AzureADMSConditionalAccessPolicy` command.

```powershell
New-AzureADMSConditionalAccessPolicy -DisplayName "MFA policy" -State "Enabled" -Conditions $conditions -GrantControls $controls
```

### Create a request

To create the `New-AzureADMSConditionalAccessPolicy` request

#### Create the request body

The following common definitions are used to build a request body:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| DisplayName | true | String | Policy name |
| State | true | String | Policy state |
| Conditions | true | [Condition Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessconditionset?view=graph-rest-1.0) | Represents the type of conditions that govern when the policy applies       |
| GrantControls | true | [Grant Controls Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessgrantcontrols?view=graph-rest-1.0) | Represents grant controls that must be fulfilled to pass the policy |

#### Example template

The following template is used to create a Conditional Access policy with display name "CA002: Require MFA for medium + sign-in risk".

```powershell
$conditions = New-Object -TypeName Microsoft.Open.MSGraph.Model.ConditionalAccessConditionSet
$conditions.Applications = New-Object -TypeName Microsoft.Open.MSGraph.Model.ConditionalAccessApplicationCondition
$conditions.Applications.IncludeApplications = "All"
$conditions.Users = New-Object -TypeName Microsoft.Open.MSGraph.Model.ConditionalAccessUserCondition
$conditions.Users.IncludeGroups = "6c96716b-b32b-40b8-9009-49748bb6fcd5"
$conditions.Users.ExcludeGroups = "f753047e-de31-4c74-a6fb-c38589047723"
$conditions.SignInRiskLevels = @('high', 'medium')
$controls = New-Object -TypeName Microsoft.Open.MSGraph.Model.ConditionalAccessGrantControls
$controls._Operator = "OR"
$controls.BuiltInControls = "mfa"
```

To create the policy using the above template for conditions and control use the below command

```powershell
New-AzureADMSConditionalAccessPolicy -DisplayName "CA0002: Require MFA for medium + sign-in risk" -State "enabledForReportingButNotEnforced" -Conditions $conditions -GrantControls $controls
```

### Responses

#### Example response

A *successful* response from the previous example request body shows an *Id* has been assigned and the *State* is *enabledForReportingButNotEnforced* also known as report-only mode:

```rest
Id              : 46a45d1a-2ac8-43d2-b087-78f587f417db
DisplayName     : CA0002: Require MFA for medium + sign-in risk
State           : enabledForReportingButNotEnforced
Conditions      : class ConditionalAccessConditionSet {
                    Applications: class ConditionalAccessApplicationCondition {
                    IncludeApplications: System.Collections.Generic.List`1[System.String]
                    ExcludeApplications: System.Collections.Generic.List`1[System.String]
                    IncludeUserActions: System.Collections.Generic.List`1[System.String]
                    IncludeProtectionLevels:
                  }

                    Users: class ConditionalAccessUserCondition {
                    IncludeUsers: System.Collections.Generic.List`1[System.String]
                    ExcludeUsers: System.Collections.Generic.List`1[System.String]
                    IncludeGroups: System.Collections.Generic.List`1[System.String]
                    ExcludeGroups: System.Collections.Generic.List`1[System.String]
                    IncludeRoles: System.Collections.Generic.List`1[System.String]
                    ExcludeRoles: System.Collections.Generic.List`1[System.String]
                  }

                    Platforms:
                    Locations:
                    SignInRiskLevels: System.Collections.Generic.List`1[Microsoft.Open.MSGraph.Model.ConditionalAccessRiskLevel]
                    ClientAppTypes: System.Collections.Generic.List`1[Microsoft.Open.MSGraph.Model.ConditionalAccessClientApp]
                  }

GrantControls   : class ConditionalAccessGrantControls {
                    _Operator: OR
                    BuiltInControls: System.Collections.Generic.List`1[Microsoft.Open.MSGraph.Model.ConditionalAccessGrantControl]
                    CustomAuthenticationFactors: System.Collections.Generic.List`1[System.String]
                    TermsOfUse: System.Collections.Generic.List`1[System.String]
                  }

SessionControls :
```

## Step 4: Get all Conditional Access policies

To fetch all Conditional Access policies, use the following `Get-AzureADMSConditionalAccessPolicy` command

```powershell
Get-AzureADMSConditionalAccessPolicy  
```

The command has all the required parameters and will return all Conditional Access policies (up to the current maximum limit of 194 policies) in the response.

## Step 5: Get a specific Conditional Access policy

To fetch a specific Conditional Access policy, use the following *Get-AzureADMSConditionalAccessPolicy* command

```powershell
Get-AzureADMSConditionalAccessPolicy -PolicyId 46a45d1a-2ac8-43d2-b087-78f587f417db
```

The command requires only the `PolicyId` you wish to return.

### Example Get-AzureADMSConditionalAccessPolicy

Once the 'GET' request is submitted, the specified Conditional Access policy is returned in a response like the one that follows.

```rest
Id              : 46a45d1a-2ac8-43d2-b087-78f587f417db
DisplayName     : CA0002: Require MFA for medium + sign-in risk
State           : enabledForReportingButNotEnforced
Conditions      : class ConditionalAccessConditionSet {
                    Applications: class ConditionalAccessApplicationCondition {
                    IncludeApplications: System.Collections.Generic.List`1[System.String]
                    ExcludeApplications: System.Collections.Generic.List`1[System.String]
                    IncludeUserActions: System.Collections.Generic.List`1[System.String]
                    IncludeProtectionLevels:
                  }

                    Users: class ConditionalAccessUserCondition {
                    IncludeUsers: System.Collections.Generic.List`1[System.String]
                    ExcludeUsers: System.Collections.Generic.List`1[System.String]
                    IncludeGroups: System.Collections.Generic.List`1[System.String]
                    ExcludeGroups: System.Collections.Generic.List`1[System.String]
                    IncludeRoles: System.Collections.Generic.List`1[System.String]
                    ExcludeRoles: System.Collections.Generic.List`1[System.String]
                  }

                    Platforms:
                    Locations:
                    SignInRiskLevels: System.Collections.Generic.List`1[Microsoft.Open.MSGraph.Model.ConditionalAccessRiskLevel]
                    ClientAppTypes: System.Collections.Generic.List`1[Microsoft.Open.MSGraph.Model.ConditionalAccessClientApp]
                  }

GrantControls   : class ConditionalAccessGrantControls {
                    _Operator: OR
                    BuiltInControls: System.Collections.Generic.List`1[Microsoft.Open.MSGraph.Model.ConditionalAccessGrantControl]
                    CustomAuthenticationFactors: System.Collections.Generic.List`1[System.String]
                    TermsOfUse: System.Collections.Generic.List`1[System.String]
                  }

SessionControls :
```

To view details on a specific object within the response, for example, Users. Use the below example to reference the object.

```powershell
$policy = Get-AzureADMSConditionalAccessPolicy -PolicyId 9de529ed-3051-4643-bb9f-111309fe187d
$policy.Conditions.Users
```

Once the '$policy.Conditions.Users' command is submitted, the specified Conditional Access policy object details is returned in the response.

```rest
IncludeUsers  : {}
ExcludeUsers  : {}
IncludeGroups : {6c96716b-b32b-40b8-9009-49748bb6fcd5}
ExcludeGroups : {f753047e-de31-4c74-a6fb-c38589047723}
IncludeRoles  : {}
ExcludeRoles  : {}
```

## Step 6: Update a Conditional Access Policy

To update a Conditional Access policy, use the following `Set-AzureADMSConditionalAccessPolicy` command.

```powershell
Set-AzureADMSConditionalAccessPolicy -PolicyId 46a45d1a-2ac8-43d2-b087-78f587f417db -DisplayName "CA002: Require MFA for medium + sign-in risk" -State "Enabled" -Conditions $conditions -GrantControls $controls
```

### Update a policy

To create the `Set-AzureADMSConditionalAccessPolicy` request

#### Create an update

The following common definitions are used to build a request body:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| DisplayName | true | String | Policy name |
| State | true | String | Policy state |
| Conditions | true | [Condition Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessconditionset?view=graph-rest-1.0) | Represents the type of conditions that govern when the policy applies |
| GrantControls | true | [Grant Controls Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessgrantcontrols?view=graph-rest-1.0) | Represents grant controls that must be fulfilled to pass the policy |

#### Example update

The following command is used to update a Conditional Access policy by including an additional group.

```powershell
$conditions.Users.IncludeGroups = @('5f6ed05f-c5f4-41d0-96d4-516e2abf825d', 'f5471a71-6fd7-475f-bd09-cda32245e3aa')
```

To update the policy using the above conditions use the following command.

```powershell
Set-AzureADMSConditionalAccessPolicy -PolicyId 46a45d1a-2ac8-43d2-b087-78f587f417db -Conditions $conditions
```

## Step 7: Delete a Conditional Access policy

To delete a specific Conditional Access policy, use the following `Remove-AzureADMSConditionalAccessPolicy` operation.

```powershell
Remove-AzureADMSConditionalAccessPolicy -PolicyId 46a45d1a-2ac8-43d2-b087-78f587f417db
```

The Remove command has all the required parameters in the command. Once the 'Remove' request is submitted, the specified Conditional Access policy is deleted.

## Next steps

Preview features and new functionality are added to Conditional Access regularly, as they are added they become available first in the [Azure Active Directory V2 preview module](https://www.powershellgallery.com/packages/AzureADPreview).

For more information on the Conditional Access APIs, see the following documents:

- [Conditional Access API](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)
- [Named location API](https://docs.microsoft.com/graph/api/resources/namedlocation?view=graph-rest-1.0)
