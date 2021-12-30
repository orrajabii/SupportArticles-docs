---
title: Fix user creation and deletion issues in Azure Active Directory
description: Troubleshoot user creation and deletion issues in Azure Active Directory. Create and delete users in Azure portal, Microsoft Graph, PowerShell, and Azure CLI.
ms.date: 12/15/2021
author: DennisLee-DennisLee
ms.author: v-dele
ms.reviewer: "tovascon,anhorta"
ms.service: active-directory
ms.subservice: domain-services
keywords:
#Customer intent: As an Azure Active Directory Global Administrator or User Administrator, I want create and delete users so that I can manage the current user list and let users access Azure services.
---
# Troubleshoot user creation and deletion issues in Azure Active Directory

This article outlines the methods in Azure Active Directory (Azure AD) you can use to:

- Create a user.
- Delete a user.
- Create users in bulk.

## Prerequisites

- One of the following role assignments:

  - [Global Administrator](/azure/active-directory/roles/permissions-reference#global-administrator)
  - [User Administrator](/azure/active-directory/roles/permissions-reference#user-administrator)

## Methods for user creation and deletion

Azure AD has many methods for creating and deleting users, such as:

- [Azure portal](https://portal.azure.com)
- [Microsoft Graph REST API](/graph/use-the-api)
- [Azure PowerShell](/powershell/azure/what-is-azure-powershell)
- [Azure CLI](/cli/azure/what-is-azure-cli)

These methods refer to users who are created directly in Azure AD. The article doesn't cover users who are created elsewhere and then synced to Azure AD or business-to-business scenarios.

## Create a user

Select a method to create a user in Azure AD.

## [Azure portal](#tab/azure-portal)

To add a new user in the Azure portal:

1. In the [Azure portal](https://portal.azure.com), sign in as a Global Administrator or a User Administrator.

1. Search for and select **Azure Active Directory**.

1. Select **Users**, and then select **New user**.

1. On the **User** page, enter the user's **Name**, **User name**, **Groups**, **Directory role**, and **Job info**.

1. Copy the autogenerated password provided in the **Password** box. You'll need to give this password to the user to sign in for the first time.

1. Select **Create**.

For more information, see [Add or delete users - Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory).

## [Microsoft Graph](#tab/microsoft-graph)

To add a user in Microsoft Graph, use the [Create user API](/graph/api/user-post-users):

```http
POST https://graph.microsoft.com/v1.0/users 
Content-type: application/json { 
  "accountEnabled": true, 
  "displayName": "<New User>", 
  "mailNickname": "<Newuser>", 
  "userPrincipalName": "<NewUser@contoso.onmicrosoft.com>", 
  "passwordProfile" : { 
    "forceChangePasswordNextSignIn": true, 
    "password": "xWwvJ]6NMw+bWH-d" 
  }
}
```

## [Azure PowerShell](#tab/azure-powershell)

To add a user in Azure PowerShell, run the [New-AzureADUser](/powershell/module/azuread/new-azureaduser) cmdlet:

```powershell-interactive
$PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
$PasswordProfile.Password = "<Password>"
New-AzureADUser -AccountEnabled $true `
    -DisplayName "<New User>" `
    -MailNickName "<Newuser>" `
    -PasswordProfile $PasswordProfile `
    -UserPrincipalName "<NewUser@contoso.onmicrosoft.com>"
```

## [Azure CLI](#tab/azure-cli)

To add a user in Azure CLI, run the [az ad user create](/cli/azure/ad/user#az_ad_user_create) command:

```azurecli-interactive
az ad user create --display-name "<New User>" \
    --password "xWwvJ]6NMw+bWH-d" \
    --user-principal-name "<NewUser@contoso.onmicrosoft.com>" \
    [--force-change-password-next-login {false, true}] \
    [--immutable-id "<base64-string-representation-of-object-guid>"] \
    [--mail-nickname "<Newuser>"]
```

---

## Delete a user

Select a method to delete a user in Azure AD.

## [Azure portal](#tab/azure-portal)

To delete a user in the Azure portal:

1. In the [Azure portal](https://portal.azure.com), sign in as a Global Administrator or a User Administrator.

1. Search for and select **Azure Active Directory**.

1. Select **Users**.

1. Search for and select the user you want to delete from your Azure AD tenant (for example, **Mary Parker**).

1. Select **Delete user**.

The user is deleted and no longer appears on the **Users - All users** page. You can view the user on the **Deleted users** page for the next 30 days. You may also restore the deleted user during that time. For more information about restoring a user, see [Restore or remove a recently deleted user using Azure Active Directory](/azure/active-directory/fundamentals/active-directory-users-restore).

After you delete a user, any licenses that the user consumes are made available for other users.

## [Microsoft Graph](#tab/microsoft-graph)

To delete a user in Microsoft Graph, use the [Delete a user API](/graph/api/user-delete):

```http
DELETE https://graph.microsoft.com/v1.0/users/{user-id-or-user-principal-name}
```

For example, if you want to delete a user who has a principal name of `NewUser@contoso.onmicrosoft.com`, use the following command:

```http
DELETE https://graph.microsoft.com/v1.0/users/NewUser@contoso.onmicrosoft.com
```

## [Azure PowerShell](#tab/azure-powershell)

To delete a user in Azure PowerShell, run the [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser) cmdlet:

```powershell-interactive
Remove-AzureADUser -ObjectId "<NewUser@contoso.onmicrosoft.com>" 
```

## [Azure CLI](#tab/azure-cli)

To delete a user in Azure CLI, run the [az ad user delete](/cli/azure/ad/user#az_ad_user_delete) command:

```azurecli-interactive
az ad user delete --id "<NewUser@contoso.onmicrosoft.com>"
```

---

## Create users in bulk

For more information about creating or deleting users in bulk, see [Bulk create users in Azure Active Directory](/azure/active-directory/enterprise-users/users-bulk-add).

## Permissions required to manage users with a service principal

If you want to automate the creation and deletion of your Azure Active Directory users on Microsoft Graph, your application needs the following permissions:

- [User.ReadWrite.All](/graph/permissions-reference#user-permissions)
- [Directory.ReadWrite.All](/graph/permissions-reference#directory-permissions)

For more information about who can manage each aspect of user management, see [Least privileged roles by task in Azure Active Directory&mdash;Users](/azure/active-directory/roles/delegate-by-task#users).

## Error messages and remediation actions

The following table contains a list of common error messages when you attempt to create or delete a user in Azure Active Directory (Azure AD), and describes the proper remediation actions for them. The error messages are as shown for the Microsoft Graph REST API, Azure PowerShell, or Azure CLI. Similar, but briefer error messages are shown in the Azure portal, and the remedial actions are identical.

| Error message | Action |
| ------------- | ------ |
| *Another object with the same value for property userPrincipalName already exists.* | Make the user principal name (UPN) unique. This error occurs when the administrator attempts to create a user with an existing user name in Azure AD. For more information, see [User name policies](/azure/active-directory/authentication/concept-sspr-policy#userprincipalname-policies-that-apply-to-all-user-accounts). |
| *Insufficient privileges to complete the operation.* | Find a Global Administrator or a User Administrator to add or delete the user. This error occurs when the security principal tries to create or delete users, but doesn't have the needed permissions. A Global admin can create or delete any user, including other admins. A User admin can create users and delete any non-admin users, Helpdesk administrators, and other User admins. |
| *Property userPrincipalName is invalid.* | See [User name policies](/azure/active-directory/authentication/concept-sspr-policy#userprincipalname-policies-that-apply-to-all-user-accounts) for a list of allowed and disallowed characters. This error occurs when you create a new user with unacceptable characters in the UPN. The user name and email address properties also can't contain accent characters. |
| *The specified password does not comply with password complexity requirements. Please provide a different password.* | Avoid using a password that: <ul><li>Doesn't follow the [Azure AD password policies](/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts). </li><li>Is on the [custom banned password list](/azure/active-directory/authentication/concept-password-ban-bad#custom-banned-password-list). </li><li>Contains the user name of the user. </li></ul> |
| *The domain portion of the userPrincipalName property is invalid. You must use one of the verified domain names in your organization.* | Make sure the domain you're using to create the user [is on the list of verified domains in the Azure AD portal](/azure/active-directory/fundamentals/add-custom-domain#verify-your-custom-domain-name). The status of the domain needs to be **Verified**. If you've verified the domain status, see whether the domain is **Federated** (has a checkmark) or **Managed** (doesn't have a checkmark). You can only create users in Azure AD for managed domains. For federated domains, you must create the user on the identity provider (IdP), and then sync to Azure AD. [You can't assign a federated domain to a user](/office365/troubleshoot/domain-management/cannot-assign-federated-domain-to-user). |

## Directory quotas

For the Free edition of Azure AD, you can create a maximum of 50,000 Azure AD resources in a single tenant by default. If you use at least one verified domain, the default Azure AD service quota for your organization is extended to 300,000 Azure AD resources. For organizations that are created by self-service sign-up, the Azure AD service quota remains 50,000 Azure AD resources. This limit applies even if you made an internal admin takeover and converted the organization to a managed tenant with one or more verified domains. This service limit is unrelated to the pricing tier limit of 500,000 resources on the Azure AD pricing page. To go beyond the default quota, you must contact Microsoft Support.

For more information, see [Azure AD service limits and restrictions](/azure/active-directory/enterprise-users/directory-service-limits-restrictions).