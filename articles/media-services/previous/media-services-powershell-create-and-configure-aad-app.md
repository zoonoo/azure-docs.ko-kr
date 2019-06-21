---
title: PowerShell을 사용하여 Azure AD 앱 만들기 및 Azure Media Services API 액세스 | Microsoft Docs
description: PowerShell을 사용하여 Azure AD(Azure Active Directory) 앱을 만들고 Azure Media Services API에 액세스하도록 설정하는 방법을 알아봅니다.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680128"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>PowerShell을 사용하여 Azure Media Services API와 함께 사용할 Azure AD 앱 만들기

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

PowerShell 스크립트를 사용하여 Azure Media Services 리소스에 액세스하기 위한 Azure AD(Azure Active Directory) 애플리케이션 및 서비스 주체를 만드는 방법에 대해 알아봅니다.  

## <a name="prerequisites"></a>필수 조건

- Azure 계정. 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal에서 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조하세요.

- Azure PowerShell. 자세한 내용은 [Azure PowerShell 사용 방법](https://docs.microsoft.com/powershell/azure/overview)을 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>PowerShell을 사용하여 Azure AD 앱 만들기  

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

자세한 내용은 다음 문서를 참조하세요.

- [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Azure PowerShell을 사용하여 역할 기반 Access Control 관리](../../role-based-access-control/role-assignments-powershell.md)
- [인증서를 사용하여 디먼 앱을 수동으로 구성하는 방법](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-portal-upload-files.md) 시작
