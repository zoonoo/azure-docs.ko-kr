---
title: Azure Portal에서 작업 그룹 만들기 및 관리
description: Azure Portal에서 작업 그룹을 만들고 관리하는 방법에 대해 알아봅니다.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a5d685e49d941d7b6febbc220cdbfbcb631c4496
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94746366"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal에서 작업 그룹 만들기 및 관리
작업 그룹은 Azure 구독 소유자가 정의한 알림 기본 설정 컬렉션입니다. Azure Monitor 및 Service Health 경고는 작업 그룹을 사용하여 경고가 트리거되었음을 사용자에게 알립니다. 사용자의 요구 사항에 따라 다양한 경고가 동일한 작업 그룹을 사용할 수도 있고 서로 다른 작업 그룹을 사용할 수도 있습니다. 구독에서는 작업 그룹을 2,000개까지 구성할 수 있습니다.

이 문서에서는 Azure Portal에서 작업 그룹을 만들고 관리하는 방법을 보여 줍니다.

각 작업은 다음과 같은 속성으로 구성됩니다.

* **유형**: 수행 되는 알림 또는 동작입니다. 음성 통화, SMS, 이메일 보내기나, 여러 자동화된 작업 유형 트리거를 예로 들 수 있습니다. 이 문서 뒷부분에 나오는 유형을 참조하세요.
* **Name**: 작업 그룹 내의 고유 식별자입니다.
* **세부 정보**: *유형별로* 달라 지는 해당 세부 정보입니다.

Azure 리소스 관리자 템플릿을 사용하여 작업 그룹을 구성하는 방법에 대한 자세한 내용은 [작업 그룹 리소스 관리자 템플릿](./action-groups-create-resource-manager-template.md)을 참조하세요.

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal을 사용하여 작업 그룹 만들기

1. [Azure Portal](https://portal.azure.com)에서 **모니터** 를 검색하여 선택합니다. **모니터** 창은 모든 모니터링 설정과 데이터를 하나의 보기로 통합합니다.

1. **경고** 를 선택한 다음 **작업 관리** 를 선택 합니다.

    ![작업 관리 단추](./media/action-groups/manage-action-groups.png)
    
1. **작업 그룹 추가** 를 선택 하 고 마법사 환경에서 관련 필드를 채웁니다.

    ![“작업 그룹 추가” 명령](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>기본 작업 그룹 설정 구성

**프로젝트 세부 정보**:

작업 그룹이 저장 되는 **구독** 및 **리소스 그룹** 을 선택 합니다.

**인스턴스 세부 정보** 에서

1. **작업 그룹 이름을** 입력 합니다.

1. **표시 이름을** 입력 합니다. 표시 이름은이 그룹을 사용 하 여 알림을 보낼 때 전체 작업 그룹 이름 대신 사용 됩니다.

      ![“작업 그룹 추가” 대화 상자](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>알림 구성

1. **다음: 알림 >** 단추를 클릭 하 여 **알림** 탭으로 이동 하거나 화면 맨 위에 있는 **알림** 탭을 선택 합니다.

1. 경고가 트리거될 때 보낼 알림 목록을 정의 합니다. 각 알림에 대해 다음을 제공 합니다.

    a. **알림 유형**: 송신 하려는 알림 유형을 선택 합니다. 사용 가능한 옵션은 다음과 같습니다.
      * 전자 메일 Azure Resource Manager 역할-특정 구독 수준 ARM 역할에 할당 된 사용자에 게 전자 메일을 보냅니다.
      * 이메일/SMS/푸시/음성-이러한 알림 유형을 특정 수신자에 게 보냅니다.
    
    b. **이름**: 알림의 고유한 이름을 입력 합니다.

    다. **세부 정보**: 선택한 알림 유형에 따라 전자 메일 주소, 전화 번호 등을 입력 합니다.
    
    d. **일반 경고 스키마**: Azure Monitor의 모든 경고 서비스에서 확장 가능하고 통합된 단일 경고 페이로드를 사용하는 이점을 제공하는 [일반 경고 스키마](./alerts-common-schema.md)를 사용하도록 선택할 수 있습니다.

    ![알림 탭](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>작업 구성

1. **다음: 작업 >** 단추를 클릭 하 여 **작업** 탭으로 이동 하거나 화면 위쪽에서 **작업** 탭을 선택 합니다.

1. 경고가 트리거될 때 트리거할 동작 목록을 정의 합니다. 각 작업에 대해 다음을 제공합니다.

    a. **작업 유형**: Automation Runbook, Azure Function, Itsm, 논리 앱, 보안 Webhook, webhook를 선택 합니다.
    
    b. **이름**: 동작에 대 한 고유한 이름을 입력 합니다.

    다. **세부 정보**: 작업 유형에 따라 webhook URI, Azure 앱, itsm 연결 또는 자동화 runbook을 입력 합니다. ITSM 작업의 경우 **작업 항목** 및 ITSM 도구에 필요한 다른 필드를 추가로 지정합니다.
    
    d. **일반 경고 스키마**: Azure Monitor의 모든 경고 서비스에서 확장 가능하고 통합된 단일 경고 페이로드를 사용하는 이점을 제공하는 [일반 경고 스키마](./alerts-common-schema.md)를 사용하도록 선택할 수 있습니다.
    
    ![작업 탭](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>작업 그룹 만들기

1. 원하는 경우 **태그** 설정을 둘러볼 수 있습니다. 그러면 분류의 작업 그룹에 키/값 쌍을 연결할 수 있으며 모든 Azure 리소스에 사용할 수 있는 기능입니다.

    ![태그 탭](./media/action-groups/action-group-4-tags.png)
    
1. **검토 + 만들기** 를 클릭하여 설정을 검토합니다. 그러면 입력의 빠른 유효성 검사를 수행 하 여 모든 필수 필드가 선택 되었는지 확인 합니다. 문제가 있는 경우 여기에 보고됩니다. 설정을 검토 한 후 **만들기** 를 클릭 하 여 작업 그룹을 프로 비전 합니다.
    
    ![검토 + 만들기 탭](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> 전자 메일 또는 SMS를 통해 사용자에 게 알리는 작업을 구성 하는 경우 작업 그룹에 추가 되었음을 나타내는 확인 메시지가 표시 됩니다.

## <a name="manage-your-action-groups"></a>작업 그룹 관리

작업 그룹을 만든 후 **모니터** 창의 **경고** 방문 페이지에서 **작업 관리** 를 선택하여 **작업 그룹** 을 볼 수 있습니다. 관리하려는 작업 그룹을 선택합니다.

* 작업을 추가, 편집 또는 제거합니다.
* 작업 그룹을 삭제합니다.

## <a name="action-specific-information"></a>작업별 정보

> [!NOTE]
> 아래의 각 항목에 대한 숫자 제한은 [모니터링에 대한 구독 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits)을 참조하세요.  

### <a name="automation-runbook"></a>자동화 Runbook
Runbook 페이로드에 대한 제한 사항은 [Azure 구독 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

작업 그룹에서 Runbook 작업의 수가 제한될 수 있습니다. 

### <a name="azure-app-push-notifications"></a>Azure 앱 푸시 알림
작업 그룹에서 Azure 앱 작업의 수가 제한될 수 있습니다.

### <a name="email"></a>Email
다음 이메일 주소에서 이메일이 전송됩니다. 이메일 필터링이 적절하게 구성되었는지 확인합니다.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

작업 그룹에서 이메일 작업의 수가 제한될 수 있습니다. [속도 제한 정보](./alerts-rate-limiting.md) 문서를 참조하세요.

### <a name="email-azure-resource-manager-role"></a>메일 Azure Resource Manager 역할
구독 역할의 멤버에게 이메일을 보냅니다. 이메일은 해당 역할의 **Azure AD 사용자** 멤버에게만 발송됩니다. Azure AD 그룹 또는 서비스 주체에게는 이메일이 전송되지 않습니다.

알림 전자 메일은 *기본 전자 메일* 주소로만 전송 됩니다.

작업 그룹에서 이메일 작업의 수가 제한될 수 있습니다. [속도 제한 정보](./alerts-rate-limiting.md) 문서를 참조하세요.

### <a name="function"></a>함수
[Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app)에서 기존 HTTP 트리거 엔드포인트를 호출합니다.

작업 그룹에서 함수 작업의 수가 제한될 수 있습니다.

### <a name="itsm"></a>ITSM
ITSM 작업에는 ITSM 연결이 필요합니다. [ITSM 연결](./itsmc-overview.md)을 만드는 방법에 대해 알아봅니다.

작업 그룹에서 ITSM 작업의 수가 제한될 수 있습니다. 

### <a name="logic-app"></a>논리 앱
작업 그룹에서 논리 앱 작업의 수가 제한될 수 있습니다.

### <a name="secure-webhook"></a>보안 웹후크
작업 그룹 웹후크 작업을 사용하면 Azure Active Directory를 활용하여 작업 그룹과 보호된 웹 API(웹후크 엔드포인트) 간의 연결을 보호할 수 있습니다. 이 기능을 활용하기 위한 전체 워크플로는 아래에 설명되어 있습니다. Azure AD 애플리케이션 및 서비스 주체에 대한 개요는 [Microsoft ID 플랫폼(v2.0) 개요](../../active-directory/develop/v2-overview.md)를 참조하세요.

1. 보호된 웹 API에 대한 Azure AD 애플리케이션을 만듭니다. [보호 된 웹 API: 앱 등록](../../active-directory/develop/scenario-protected-web-api-app-registration.md)을 참조 하세요.
    - [디먼 앱에서 호출](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)하도록 보호 된 API를 구성 합니다.
    
2. Azure AD 애플리케이션을 사용하도록 작업 그룹을 설정합니다.

    > [!NOTE]
    > 이 스크립트를 실행하려면 [Azure AD 애플리케이션 관리자 역할](../../active-directory/roles/permissions-reference.md#available-roles)의 멤버여야 합니다.
    
    - PowerShell 스크립트의 Connect-AzureAD 호출을 수정하여 Azure AD 테넌트 ID를 사용합니다.
    - PowerShell 스크립트의 변수 $myAzureADApplicationObjectId 수정 하 여 Azure AD 응용 프로그램의 개체 ID를 사용 합니다.
    - 수정된 스크립트를 실행합니다.
    
3. 작업 그룹 보안 웹후크 작업을 구성합니다.
    - 스크립트에서 $myApp.ObjectId 값을 복사하여 웹후크 작업 정의의 애플리케이션 개체 ID 필드에 입력합니다.
    
    ![보안 웹후크 작업](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>보안 웹후크 PowerShell 스크립트

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
[속도 제한 정보](./alerts-rate-limiting.md) 및 [SMS 경고 동작](./alerts-sms-behavior.md)에서 중요한 추가 정보를 참조하세요. 

작업 그룹에서 SMS 작업의 수가 제한될 수 있습니다.

> [!NOTE]
> Azure Portal 작업 그룹 사용자 인터페이스에서 국가/지역 코드를 선택할 수 없는 경우 해당 국가/지역에 대해서는 SMS가 지원되지 않는 것입니다.  국가/지역 코드를 사용할 수 없는 경우 [사용자 의견](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)에서 사용자의 국가/지역이 추가되도록 투표할 수 있습니다. 그동안에는 작업 그룹에서 해당 국가/지역을 지원하는 타사 SMS 공급자에 대한 웹후크를 호출하도록 하여 문제를 해결합니다.  

지원되는 국가/지역에 대한 가격은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 나와 있습니다.
  

### <a name="voice"></a>음성
[속도 제한 정보](./alerts-rate-limiting.md) 문서에서 중요한 추가 동작을 참조하세요.

작업 그룹에서 음성 작업의 수가 제한될 수 있습니다.

> [!NOTE]
> Azure Portal 작업 그룹 사용자 인터페이스에서 국가/지역 코드를 선택할 수 없는 경우 해당 국가/지역에 대해서는 음성 통화가 지원되지 않는 것입니다. 국가/지역 코드를 사용할 수 없는 경우 [사용자 의견](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)에서 사용자의 국가/지역이 추가되도록 투표할 수 있습니다.  그동안에는 작업 그룹에서 해당 국가/지역을 지원하는 타사 음성 통화 공급자에 대한 웹후크를 호출하도록 하여 문제를 해결합니다.  

지원되는 국가/지역에 대한 가격은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 나와 있습니다.

### <a name="webhook"></a>웹후크
웹 후크는 다음 규칙을 사용 하 여 처리 됩니다.
- Webhook 호출은 최대 3 회 시도 됩니다.
- 제한 시간 내에 응답이 수신 되지 않거나 다음 HTTP 상태 코드 중 하나가 반환 되 면 (408, 429, 503 또는 504) 호출을 다시 시도 합니다.
- 첫 번째 호출은 응답을 위해 10 초를 기다립니다.
- 두 번째와 세 번째 시도는 응답에 대해 30 초 동안 대기 합니다.
- 3 번의 webhook 호출이 실패 한 후에는 작업 그룹에서 15 분 동안 끝점을 호출 하지 않습니다.

원본 IP 주소 범위:

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603:1020: a04:402:: 178/125
 - 2603:1020: b04:402:: 178/125
 - 2603:1020: c04:402:: 178/125
 - 2603:1020: d04:402:: 178/125
 - 2603:1020: e04:402:: 178/125
 - 2603:1020: f04:402:: 178/125
 - 2603:1020:1004:800:: f8/125
 - 2603:1020:1104:400::178/125
 - 2603: f:400:: 978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603:가 중: 107:400:: f0/125
 - 2603:1030:210:402::178/125
 - 2603:가 중: 40b: 400:: 978/125
 - 2603:. 40c: 402:: 178/125
 - 2603: 이란: 504:802:: f8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603:. a07:402:: 8f8/125
 - 2603:. b04:402:: 178/125
 - 2603:. c06:400:: 978/125
 - 2603:. f05:402:: 178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603:1040: a06:402:: 178/125
 - 2603:1040: b04:402:: 178/125
 - 2603:1040: c06:402:: 178/125
 - 2603:1040: d04:800:: f8/125
 - 2603:1040: f05:402:: 178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603:1050:403:400:: 1f8/125

이러한 IP 주소에 대한 변경 내용에 대한 업데이트를 받으려면 작업 그룹 서비스에 대한 정보 알림을 모니터링하는 Service Health 경고를 구성하는 것이 좋습니다.

작업 그룹에서 웹후크 작업의 수가 제한될 수 있습니다.

원본 IP 주소에 대 한 빈번한 업데이트는 Webhook에서 상당한 시간이 걸릴 수 있습니다. *Actiongroup* 에 **서비스 태그** 를 사용 하면 IP 주소를 수동으로 업데이트 하는 복잡성을 최소화 하는 데 도움이 됩니다. 위에서 공유 하는 원본 IP 주소 범위 접두사는 Microsoft **서비스 태그** 에 의해 자동으로 관리 됩니다.

#### <a name="service-tag"></a>서비스 태그
서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그가 들어 있는 주소 접두사를 관리 하 고, 주소가 변경 될 때 서비스 태그를 자동으로 업데이트 하 여 ActionGroup에 대 한 네트워크 보안 규칙을 자주 업데이트 하는 복잡성을 최소화 합니다.

1. Azure 서비스의 Azure Portal에서 *네트워크 보안 그룹* 을 검색 합니다.
2. **추가** 를 클릭 하 고 네트워크 보안 그룹을 만듭니다.

   1. 리소스 그룹 이름을 추가 하 고 *인스턴스 세부 정보* 를 입력 합니다.
   1. **검토 + 만들기** 를 클릭 한 다음 *만들기* 를 클릭 합니다.
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="네트워크 보안 그룹을 만드는 방법에 대 한 예입니다."border="true":::

3. 리소스 그룹으로 이동한 다음 만든 *네트워크 보안 그룹* 을 클릭 합니다.

    1. *인바운드 보안 규칙* 을 선택 합니다.
    1. **추가** 를 클릭 합니다.
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="서비스 태그를 추가 하는 방법에 대 한 예입니다."border="true":::

4. 오른쪽 창에 새 창이 열립니다.
    1.  원본 선택: **서비스 태그**
    1.  원본 서비스 태그: **Actiongroup**
    1.  **추가** 를 클릭합니다.
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="서비스 태그를 추가 하는 방법에 대 한 예입니다."border="true":::

## <a name="next-steps"></a>다음 단계
* [SMS 경고 동작](./alerts-sms-behavior.md)에 대해 자세히 알아보세요.  
* [활동 로그 경고 웹후크 스키마의 이해](./activity-log-alerts-webhook.md)를 확인해 보세요.  
* [ITSM 커넥터](./itsmc-overview.md)에 대해 자세히 알아보세요.
* 경고의 [속도 제한](./alerts-rate-limiting.md)에 대해 자세히 알아보세요.
* [활동 로그 경고의 개요](./alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.  
* [서비스 상태 알림이 게시될 때마다 경고를 구성](../../service-health/alerts-activity-log-service-notifications-portal.md)하는 방법을 알아보세요.
