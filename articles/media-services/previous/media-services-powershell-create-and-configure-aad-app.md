---
title: Use PowerShell to create an Azure AD app to access the Azure Media Services API | Microsoft Docs
description: Learn how to use PowerShell to create an Azure Active Directory (Azure AD) app and set it up to access the Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako

---

# Use PowerShell to create an Azure AD app to use with the Azure Media Services API

Learn how to use a PowerShell script to create an Azure Active Directory (Azure AD) application and service principal to access Azure Media Services resources.  

## Prerequisites

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- An Azure account. If you don't have an account, start with an [Azure free trial](https://azure.microsoft.com/pricing/free-trial/). 
- A Media Services account. For more information, see [Create an Azure Media Services account in the Azure portal](media-services-portal-create-account.md).

- Azure PowerShell. For more information, see [How to use Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## Create an Azure AD app by using PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
	# Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

For more information, see the following articles:

- [Use Azure PowerShell to create a service principal to access resources](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Manage Role-Based Access Control by using Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [How to manually configure daemon apps by using certificates](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## Next steps

Get started with [uploading files to your account](media-services-portal-upload-files.md).
