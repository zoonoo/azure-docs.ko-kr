---
title: Azure Event Grid에서 Azure AD를 사용 하 여 보안 WebHook 배달
description: 를 사용 하 여 Azure Active Directory 보호 하는 HTTPS 끝점에 이벤트를 전달 하는 방법을 설명 Azure Event Grid
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 0320e78e6b436f6ba1c0a6ca1bfec81eb974e106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812202"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory로 보호되는 엔드포인트에 이벤트 게시

이 문서에서는 Azure Active Directory를 활용 하 여 이벤트 구독과 webhook 끝점 간의 연결을 보호 하는 방법을 설명 합니다. Azure AD 애플리케이션 및 서비스 주체에 대한 개요는 [Microsoft ID 플랫폼(v2.0) 개요](../active-directory/develop/v2-overview.md)를 참조하세요.

이 문서에서는 데모를 위해 Azure Portal를 사용 하지만 CLI, PowerShell 또는 Sdk를 사용 하 여이 기능을 사용 하도록 설정할 수도 있습니다.


## <a name="create-an-azure-ad-application"></a>Azure AD 응용 프로그램 만들기

먼저 보호 된 끝점에 대 한 Azure AD 응용 프로그램을 만듭니다. https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview 을 참조하세요.
    - 디먼 앱에서 호출하도록 보호된 API를 구성합니다.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Event Grid를 사용 하 여 Azure AD 응용 프로그램 사용
이 섹션에서는 Azure AD 응용 프로그램을 사용 하기 위해 Event Grid를 사용 하도록 설정 하는 방법을 보여 줍니다. 

> [!NOTE]
> 이 스크립트를 실행하려면 [Azure AD 애플리케이션 관리자 역할](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles)의 멤버여야 합니다.

### <a name="connect-to-your-azure-tenant"></a>Azure 테 넌 트에 연결
먼저 명령을 사용 하 여 Azure 테 넌 트에 연결 `Connect-AzureAD` 합니다. 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Microsoft EventGrid 서비스 주체 만들기
다음 스크립트를 실행 하 여 **Microsoft EventGrid** 에 대 한 서비스 주체 (아직 없는 경우)를 만듭니다. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>응용 프로그램에 대 한 역할 만들기   
다음 스크립트를 실행 하 여 Azure AD 응용 프로그램에 대 한 역할을 만듭니다. 이 예제에서 역할 이름은 **Azureeventgridsecurewebhook**입니다. Azure ad `$myTenantId` 테 넌 트 id와 `$myAzureADApplicationObjectId` Azure Ad 응용 프로그램의 개체 id를 사용 하도록 PowerShell 스크립트를 수정 합니다.

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
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

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>역할에 Event Grid 서비스 주체 추가    
이제 `New-AzureADServiceAppRoleAssignment` 명령을 실행 하 여 이전 단계에서 만든 역할에 Event Grid 서비스 사용자를 할당 합니다. 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

다음 명령을 실행 하 여 다음 단계에서 사용할 정보를 출력 합니다. 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>이벤트 구독 구성

이벤트 구독에 대 한 만들기 흐름에서 끝점 유형 ' 웹 후크 '를 선택 합니다. 끝점 URI를 지정한 후에는 이벤트 구독 만들기 블레이드 맨 위에 있는 추가 기능 탭을 클릭 합니다.

![끝점 유형 webhook를 선택 합니다.](./media/secure-webhook-delivery/select-webhook.png)

추가 기능 탭에서 ' AAD 인증 사용 ' 상자를 선택 하 고 테 넌 트 ID 및 응용 프로그램 ID를 구성 합니다.

* 스크립트의 출력에서 Azure AD 테 넌 트 ID를 복사 하 고 AAD 테 넌 트 ID 필드에 입력 합니다.
* 스크립트의 출력에서 Azure AD 응용 프로그램 ID를 복사 하 고 AAD 응용 프로그램 ID 필드에 입력 합니다.

    ![보안 웹후크 작업](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
