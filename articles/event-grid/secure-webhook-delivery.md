---
title: Azure Event Grid에서 Azure AD를 사용하여 보안 웹후크 배달
description: Azure Event Grid를 사용하여 Azure Active Directory가 보호하는 HTTPS 엔드포인트에 이벤트를 전달하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 6a0f9059e17d96d497b425abc9749e69c5ab4d41
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575550"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory로 보호되는 엔드포인트에 이벤트 게시
이 문서에서는 Azure AD(Azure Active Directory)를 사용하여 **이벤트 구독** 과 **웹후크 엔드포인트** 간의 연결을 보호하는 방법을 설명합니다. Azure AD 애플리케이션 및 서비스 주체에 대한 개요는 [Microsoft ID 플랫폼(v2.0) 개요](../active-directory/develop/v2-overview.md)를 참조하세요.

이 문서에서는 데모를 위해 Azure Portal을 사용하지만 CLI, PowerShell 또는 SDK를 사용하여 이 기능을 사용하도록 설정할 수도 있습니다.

> [!IMPORTANT]
> 보안 취약성을 해결하기 위해 2021년 3월 30일에 이벤트 구독을 만들거나 업데이트하는 과정의 일부로 추가 액세스 검사가 도입되었습니다. 구독자 클라이언트의 서비스 주체는 소유자이거나 대상 애플리케이션 서비스 주체에 할당된 역할이 있어야 합니다. 아래 새 지침에 따라 AAD 애플리케이션을 다시 구성하세요.


## <a name="create-an-azure-ad-application"></a>Azure AD 애플리케이션 만들기
보호된 엔드포인트에 대한 Azure AD 애플리케이션을 만들어 Azure AD에 웹후크를 등록합니다. [시나리오: 보호된 웹 API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview)를 참조하세요. 디먼 앱에서 호출하도록 보호된 API를 구성합니다.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Azure AD 애플리케이션을 사용하기 위해 Event Grid를 사용하도록 설정
이 섹션에서는 Azure AD 애플리케이션을 사용하기 위해 Event Grid를 사용하도록 설정하는 방법을 보여 줍니다. 

> [!NOTE]
> 이 스크립트를 실행하려면 [Azure AD 애플리케이션 관리자 역할](../active-directory/roles/permissions-reference.md#all-roles)의 멤버여야 합니다.

### <a name="connect-to-your-azure-tenant"></a>Azure 테넌트에 연결
먼저 `Connect-AzureAD` 명령을 사용하여 Azure 테넌트에 연결합니다. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Microsoft.EventGrid 서비스 주체 만들기
다음 스크립트를 실행하여 **Microsoft.EventGrid** 에 대한 서비스 주체(아직 없는 경우)를 만듭니다. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else {
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>애플리케이션에 대한 역할을 만듭니다.   
다음 스크립트를 실행하여 Azure AD 애플리케이션에 대한 역할을 만듭니다. 이 예에서 역할의 이름은 **AzureEventGridSecureWebhookSubscriber** 입니다. Azure AD 테넌트 ID를 사용하기 위해 PowerShell 스크립트의 `$myTenantId`를 수정하고 Azure AD 애플리케이션의 개체 ID로 `$myAzureADApplicationObjectId`를 사용하도록 수정합니다.

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
} else {      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>이벤트 구독을 만드는 클라이언트에 대한 역할 할당 만들기
이벤트 구독을 만드는 AAD 앱 또는 AAD 사용자에 대한 웹후크 Azure AD 앱에 역할 할당을 만들어야 합니다. AAD 앱 또는 AAD 사용자가 이벤트 구독을 만드는지 여부에 따라 아래 스크립트 중 하나를 사용합니다.

> [!IMPORTANT]
> 보안 취약성을 해결하기 위해 2021년 3월 30일에 이벤트 구독을 만들거나 업데이트하는 과정의 일부로 추가 액세스 검사가 도입되었습니다. 구독자 클라이언트의 서비스 주체는 소유자이거나 대상 애플리케이션 서비스 주체에 할당된 역할이 있어야 합니다. 아래 새 지침에 따라 AAD 애플리케이션을 다시 구성하세요.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>이벤트 구독 AAD 앱에 대한 역할 할당 만들기 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>이벤트 구독 AAD 사용자에 대한 역할 할당 만들기 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Event Grid 서비스 주체에 대한 역할 할당 만들기
New-AzureADServiceAppRoleAssignment 명령을 실행하여 이전 단계에서 만든 역할에 Event Grid 서비스 주체를 할당합니다.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

다음 명령을 실행하여 나중에 사용할 정보를 출력합니다.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>이벤트 구독 구성
이벤트 구독을 만들 때 다음 단계를 수행합니다.

1. 엔드포인트 유형을 **웹후크** 로 선택합니다. 
1. 엔드포인트 **URI** 를 지정합니다.

    ![엔드포인트 유형 웹후크를 선택합니다.](./media/secure-webhook-delivery/select-webhook.png)
1. **이벤트 구독 만들기** 페이지의 위쪽에서 **추가 기능** 탭을 선택합니다.
1. **추가 기능** 탭에서 다음 단계를 수행합니다.
    1. **AAD 인증 사용** 을 선택하고 테넌트 ID 및 애플리케이션 ID를 구성합니다.
    1. 스크립트의 출력에서 Azure AD 테넌트 ID를 복사하고 **AAD 테넌트 ID** 필드에 입력합니다.
    1. 스크립트의 출력에서 Azure AD 애플리케이션 ID를 복사하고 **AAD 애플리케이션 ID** 필드에 입력합니다. 또는 AAD 애플리케이션 ID URI를 사용할 수 있습니다. 애플리케이션 ID URI에 대한 자세한 내용은 [이 문서](../app-service/configure-authentication-provider-aad.md)를 참조하세요.

        ![보안 웹후크 작업](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
