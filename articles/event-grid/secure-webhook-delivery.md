---
title: Azure 이벤트 그리드에서 Azure AD를 통해 안전한 웹후크 배달
description: Azure 이벤트 그리드를 사용하여 Azure Active Directory로 보호되는 HTTPS 엔드포인트에 이벤트를 제공하는 방법을 설명합니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931550"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory 보호된 끝점에 이벤트 게시

이 문서에서는 Azure Active Directory를 활용하여 이벤트 구독과 웹후크 끝점 간의 연결을 보호하는 방법을 설명합니다. Azure AD 응용 프로그램 및 서비스 주체에 대한 개요는 [Microsoft ID 플랫폼(v2.0) 개요를](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)참조하십시오.

이 문서에서는 데모를 위해 Azure 포털을 사용하지만 CLI, PowerShell 또는 SDK를 사용하여 기능을 사용할 수도 있습니다.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Azure AD 응용 프로그램 만들기

먼저 보호된 끝점에 대한 Azure AD 응용 프로그램을 만듭니다. https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview을 참조하세요.
    - 데몬 앱에서 호출하도록 보호된 API를 구성합니다.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Azure AD 응용 프로그램을 사용하도록 설정

Azure AD 응용 프로그램에서 역할 및 서비스 원칙을 만들려면 아래 PowerShell 스크립트를 사용합니다. Azure AD 응용 프로그램의 테넌트 ID 및 개체 ID가 필요합니다.

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Azure AD 테넌트 ID를 사용하도록 PowerShell 스크립트의 $myTenantId 수정합니다.
1. Azure AD 응용 프로그램의 개체 ID를 사용하도록 PowerShell 스크립트의 $myAzureADApplicationObjectId 수정합니다.
1. 수정된 스크립트를 실행합니다.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>이벤트 구독 구성

이벤트 구독의 생성 흐름에서 끝점 유형 '웹 후크'를 선택합니다. 끝점 URI를 지정한 후에는 만들기 이벤트 구독 블레이드 상단의 추가 기능 탭을 클릭합니다.

![엔드포인트 유형 웹후크 선택](./media/secure-webhook-delivery/select-webhook.png)

추가 기능 탭에서 'AAD 인증 사용' 확인란을 선택하고 테넌트 ID 및 응용 프로그램 ID를 구성합니다.

* 스크립트출력에서 Azure AD 테넌트 ID를 복사하여 AAD 테넌트 ID 필드에 입력합니다.
* 스크립트출력에서 Azure AD 응용 프로그램 ID를 복사하여 AAD 응용 프로그램 ID 필드에 입력합니다.

    ![보안 웹후크 작업](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
