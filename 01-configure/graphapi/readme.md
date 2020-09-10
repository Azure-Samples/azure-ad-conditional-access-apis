# Configure Conditional Access policies using the Microsoft Graph API

You can use the Conditional Access APIs to manage policies at scale. For example, you can:

- Get a snapshot of all Conditional Access policies within your organization and take backups.
- Update a Conditional Access policy to add new groups as you rollout your policies within your organization.
- Update the list of applications you are targeting within the Conditional Access policies as new line of business application are added in your organization.
- Delete a policy that is no longer needed.

For more information about Conditional Access, see the article [Components of CA policy](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies).

In this sample, you'll learn how to:

:heavy_check_mark: Authenticate with the right role or permissions

:heavy_check_mark: Create a Conditional Access policy using an API template

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

One of the following permissions is required to call this API. To learn more, including how to choose permissions, see the article [Microsoft Graph permissions reference](https://docs.microsoft.com/graph/permissions-reference).

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

## Step 2: Create a Conditional Access Policy

The steps to create a Sign-in risk-based Conditional Access policy within Azure Portal is documented in the article, [Conditional Access: Sign-in risk-based Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk). We will use that document as a reference to create a policy called "CA002: Require MFA for medium + sign-in risk" using the APIs.

To create a Conditional Access policy, use the following `POST` operation.

```http
POST https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

### Create a POST request

To create the `POST` request

The following headers are required:

| Request header | Description |
| --- | --- |
| *Content-Type:* | Required. Set to `application/json`. |
| *Authorization:* | Required. Set to a valid `Bearer` [access token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

For more information about how to create the request, see [Components of API request/response](https://docs.microsoft.com/rest/api/azure/#components-of-a-rest-api-requestresponse).

### Create the POST request body

The following common definitions are used to build a request body:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| displayName | true | String | Policy name |
| state | true | String | Policy state |
| conditions | true | [Condition Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessconditionset?view=graph-rest-1.0) | Represents the type of conditions that govern when the policy applies |
| grantControls | true | [Grant Controls Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessgrantcontrols?view=graph-rest-1.0) | Represents grant controls that must be fulfilled to pass the policy |

### Example POST request body

The following template is used to create a Conditional Access policy with display name "CA002: Require MFA for medium + sign-in risk".

```json
{
    "displayName": "CA002: Require MFA for medium + sign-in risk",
    "state": "enabledForReportingButNotEnforced",
    "conditions": {
        "signInRiskLevels": [ "high" ,
            "medium"
        ],
        "applications": {
            "includeApplications": [
                "All"
            ]
        },
        "users": {
            "includeGroups": [
                "6c96716b-b32b-40b8-9009-49748bb6fcd5"
            ],
            "excludeGroups": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ]
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ]
    }
}
```

### POST response

A successful response for the operation to create a Conditional Access policy:

| Name | Description |
| --- | --- |
| 201 Created | Created |

For more information about REST API responses, see the article [Process the response message](https://docs.microsoft.com/rest/api/azure/#process-the-response-message).

### Example POST response

A *201 Created* response from the previous example request body shows an *id* has been assigned and the *state* is *enabledForReportingButNotEnforced*:

```json
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#identity/conditionalAccess/policies/$entity",
    "id": "34ab2fe6-51ec-4442-9558-723f480ee29f",
    "displayName": "CA002: Require MFA for medium + sign-in risk",
    "createdDateTime": "2020-07-08T11:34:24.6674365Z",
    "modifiedDateTime": null,
    "state": "enabledForReportingButNotEnforced",
    "sessionControls": null,
    "conditions": {
        "signInRiskLevels": [
            "high",
            "medium"
        ],
        "clientAppTypes": [],
        "platforms": null,
        "locations": null,
        "devices": null,
        "applications": {
            "includeApplications": [
                "All"
            ],
            "excludeApplications": [],
            "includeUserActions": []
        },
        "users": {
            "includeUsers": [],
            "excludeUsers": [],
            "includeGroups": [
                "6c96716b-b32b-40b8-9009-49748bb6fcd5"
            ],
            "excludeGroups": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ],
            "includeRoles": [],
            "excludeRoles": []
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [],
        "termsOfUse": []
    }
}
```

## Step 3: List all Conditional Access policies

To fetch all Conditional Access policies, use the following `GET` operation

```http
GET https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

The GET URI has `{v1.0}`, `{identity}`, `{conditionalAccess}` and `{policies}` within the endpoint. As all the required parameters are given in the URI, there is no need for a separate request body.

### GET response

The successful response for the `GET` operation is shown below:

| Name | Description |
| --- | --- |
| 200 OK | OK |

### Example GET response

Once the `GET` request is submitted, a 200 (successful) response is returned. All Conditional Access policies (up to the current maximum limit of 194 policies) are returned in the response.

```json
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#identity/conditionalAccess/policies",
    "value": [
     {
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#identity/conditionalAccess/policies/$entity",
    "id": "34ab2fe6-51ec-4442-9558-723f480ee29f",
    "displayName": "CA002: Require MFA for medium + sign-in risk",
    "createdDateTime": "2020-07-08T11:34:24.6674365Z",
    "modifiedDateTime": null,
    "state": "enabledForReportingButNotEnforced",
    "sessionControls": null,
    "conditions": {
        "signInRiskLevels": [
            "high",
            "medium"
        ],
        "clientAppTypes": [],
        "platforms": null,
        "locations": null,
        "devices": null,
        "applications": {
            "includeApplications": [
                "All"
            ],
            "excludeApplications": [],
            "includeUserActions": []
        },
        "users": {
            "includeUsers": [],
            "excludeUsers": [],
            "includeGroups": [
                "6c96716b-b32b-40b8-9009-49748bb6fcd5"
            ],
            "excludeGroups": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ],
            "includeRoles": [],
            "excludeRoles": []
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [],
        "termsOfUse": []
       }
    }
  ]
}
```

## Step 4: Get a specific Conditional Access policy

To get a specific Conditional Access policy, use the following `GET` operation

```http
GET https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
```

The GET URI has `{policyid}` parameter. In this example, `{policyid}` is "34ab2fe6-51ec-4442-9558-723f480ee29f".  As all the required parameters are given in the URI, there is no need for a separate request body.

```http
GET https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/34ab2fe6-51ec-4442-9558-723f480ee29f
```

### GET specific policy response

The successful response for the 'GET' operation is shown below:

| Name | Description |
| --- | --- |
| 200 OK | OK |

#### Example GET specific policy response

Once the 'GET' request is submitted, a 200 (successful) response is returned.

```json
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#identity/conditionalAccess/policies/$entity",
    "id": "34ab2fe6-51ec-4442-9558-723f480ee29f",
    "displayName": "CA002: Require MFA for medium + sign-in risk",
    "createdDateTime": "2020-07-08T11:34:24.6674365Z",
    "modifiedDateTime": null,
    "state": "enabledForReportingButNotEnforced",
    "sessionControls": null,
    "conditions": {
        "signInRiskLevels": [
            "high",
            "medium"
        ],
        "clientAppTypes": [],
        "platforms": null,
        "locations": null,
        "devices": null,
        "applications": {
            "includeApplications": [
                "All"
            ],
            "excludeApplications": [],
            "includeUserActions": []
        },
        "users": {
            "includeUsers": [],
            "excludeUsers": [],
            "includeGroups": [
                "6c96716b-b32b-40b8-9009-49748bb6fcd5"
            ],
            "excludeGroups": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ],
            "includeRoles": [],
            "excludeRoles": []
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [],
        "termsOfUse": []
    }
}
```

## Step 5: Update a Conditional Access Policy

Let us now update the Conditional Access policy we retrieved in the previous step.

To update a Conditional Access policy, use the following `PATCH` operation.

```http
PATCH https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
```

The PATCH URI has `{policyid}` parameter. In this example, `{policyid}` is "34ab2fe6-51ec-4442-9558-723f480ee29f".  

```http
PATCH https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/34ab2fe6-51ec-4442-9558-723f480ee29f
```

### Create PATCH a request

To create the `PATCH` request

The following headers are required:

| Request header | Description |
| --- | --- |
| *Content-Type:* | Required. Set to `application/json`. |
| *Authorization:* | Required. Set to a valid `Bearer` [access token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

For more information about how to create the request, see the article [Components of API request/response](https://docs.microsoft.com/rest/api/azure/#components-of-a-rest-api-requestresponse).

### Create the PATCH request body

The following common definitions are used to build a request body:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| displayName | true | String | Policy name |
| state | true | String | Policy state |
| conditions | true | [Condition Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessconditionset?view=graph-rest-1.0) | Represents the type of conditions that govern when the policy applies |
| grantControls | true | [Grant Controls Set](https://docs.microsoft.com/graph/api/resources/conditionalaccessgrantcontrols?view=graph-rest-1.0) | Represents grant controls that must be fulfilled to pass the policy |

### Example PATCH request body

The following example body is used to update the Conditional Access policy with an additional group id {"ba8e7ded-8b0f-4836-ba06-8ff1ecc5c8ba"}.

```json
{
    "conditions": {
        "users": {
            "includeGroups": [
                "6c96716b-b32b-40b8-9009-49748bb6fcd5", "ba8e7ded-8b0f-4836-ba06-8ff1ecc5c8ba"
            ]
        }
    }
}
```

### PATCH Response

A successful response for the operation to create a Conditional Access policy:

| Name | Description |
| --- | --- |
| 204 No Content | Updated |

For more information about REST API responses, see the article [Process the response message](https://docs.microsoft.com/rest/api/azure/#process-the-response-message).

#### Example PATCH response

A *204 No Content* response from the previous example request body shows the *groupid* has been added to *includeGroups* property:

```json
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#identity/conditionalAccess/policies/$entity",
    "id": "34ab2fe6-51ec-4442-9558-723f480ee29f",
    "displayName": "CA002: Require MFA for medium + sign-in risk",
    "createdDateTime": "2020-07-08T11:34:24.6674365Z",
    "modifiedDateTime": "2020-07-08T12:22:42.3500388Z",
    "state": "enabledForReportingButNotEnforced",
    "sessionControls": null,
    "conditions": {
        "signInRiskLevels": [
            "high",
            "medium"
        ],
        "clientAppTypes": [
            "browser",
            "mobileAppsAndDesktopClients"
        ],
        "platforms": null,
        "locations": null,
        "devices": null,
        "applications": {
            "includeApplications": [
                "All"
            ],
            "excludeApplications": [],
            "includeUserActions": []
        },
        "users": {
            "includeUsers": [],
            "excludeUsers": [],
            "includeGroups": [
                "6c96716b-b32b-40b8-9009-49748bb6fcd5",
                "ba8e7ded-8b0f-4836-ba06-8ff1ecc5c8ba"
            ],
            "excludeGroups": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ],
            "includeRoles": [],
            "excludeRoles": []
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [],
        "termsOfUse": []
    }
}
```

## Step 6: Delete a specific Conditional Access policy

To delete a specific Conditional Access policy, use the following `DELETE` operation

```http
DELETE https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
```

The DELETE URI has `{policyid}` parameter. In this example, `{policyid}` is "34ab2fe6-51ec-4442-9558-723f480ee29f".  As all the required parameters are given in the URI, there is no need for a separate request body.

```http
DELETE https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/34ab2fe6-51ec-4442-9558-723f480ee29f
```

### DELETE response

The successful response for the `DELETE` operation is shown below:

| Name | Description |
| --- | --- |
| 204 No Content | Deleted |

### Example DELETE response

Once the `DELETE` request is submitted, a 204 (successful) response is returned.

## Next steps

Try creating and updating a Conditional Access policy using the following JSON templates:

- [JSON template - Require MFA for administrative roles](./json/template-require-mfa-admin-roles.json)
- [JSON template - Require MFA for B2B and guest users](./json/template-require-mfa-b2b-users.json)
- [JSON template - Require MFA for security info registration](./json/template-require-mfa-security-info-registration.json)
- [JSON template - Require a trusted device for specific applications](./json/template-require-trusted-device-app-access.json)
- [JSON template - Require MFA on medium or high risk (Requires Azure AD Identity Protection)](./json/template-require-mfa-risk.json)
- [JSON template - Require MAM policy for Android and IOS devices](./json/template-require-mam-android-ios.json)

Preview features and new functionality are added to Conditional Access regularly, as they are added they become available first in the [Conditional Access API beta endpoint](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta).

Next, [Configure Conditional Access policies using templates based on Microsoft Graph APIs](../templates/readme.md).

For more information on the Conditional Access APIs, see the following documents:

- [Conditional Access API](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)
- [Named location API](https://docs.microsoft.com/graph/api/resources/namedlocation?view=graph-rest-1.0)
