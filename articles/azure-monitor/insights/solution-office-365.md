---
title: Azure에서 Office 365 관리 솔루션 | Microsoft Docs
description: 이 문서에서는 Azure에서 Office 365 솔루션을 구성하고 사용하는 방법에 대해 자세히 설명합니다.  또한 Azure Monitor에서 작성되는 Office 365 레코드에 대해서도 자세히 설명합니다.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: bwren
ms.openlocfilehash: 4c7e1225a8da1e20bc90986d1530b781f7f2c11a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357571"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure에서 Office 365 관리 솔루션(미리 보기)

![Office 365 로고](media/solution-office-365/icon.png)


> [!NOTE]
> 설치 하 고 Office 365 솔루션을 구성 하는 것이 좋습니다가 사용 하도록 설정 합니다 [Office 365 커넥터](../../sentinel/connect-office-365.md) 에 [Azure Sentinel](../../sentinel/overview.md) 이 문서의 단계를 사용 하는 대신 합니다. 이 향상 된 구성 환경 사용 하 여 Office 365 솔루션의 업데이트 된 버전입니다. Azure AD 로그를 연결 하려면 사용 합니다 [Azure Sentinel Azure AD 커넥터](../../sentinel/connect-azure-active-directory.md), Office 365 관리 로그 보다 더 다양 한 로그 데이터를 제공 하는 합니다. 
>
> 경우 있습니다 [온 보 딩 Azure Sentinel](../../sentinel/quickstart-onboard.md), Log Analytics 작업 영역에 설치 된 Office 365 솔루션을 지정 합니다. 커넥터를 사용 하도록 설정 하면 솔루션 작업 영역에서 사용할 수 있습니다 하 고 설치한 다른 모니터링 솔루션으로 사용 하는 동일 합니다.
>
> 사용자가 Azure government cloud의 Azure Sentinel 아직 사용할 수 없으므로 government 클라우드에서이 문서의 단계를 사용 하 여 Office 365를 설치 해야 합니다.

Office 365 관리 솔루션을 사용하면 Azure Monitor에서 Office 365 환경을 모니터링할 수 있습니다.

- Office 365 계정에서 사용자 활동을 모니터링하면 사용량 패턴을 분석하고 동작 경향을 식별할 수 있습니다. 예를 들어 조직 외부나 인기 SharePoint 사이트에서 공유하는 파일과 같은 특정 사용 시나리오를 추출할 수 있습니다.
- 관리자 활동을 모니터링하면 구성 변경 내용이나 높은 권한이 필요한 작업을 추적할 수 있습니다.
- 조직 요구 사항에 따라 사용자 지정할 수 있는 부적절한 사용자 행동을 검색하고 조사할 수 있습니다.
- 감사 및 규정 준수 방식을 제시할 수 있습니다. 예를 들어 기밀 파일에 대한 파일 액세스 작업을 모니터링하여 감사 및 규정 준수 프로세스를 보다 원활하게 진행할 수 있습니다.
- 조직의 Office 365 활동 데이터를 토대로 [로그 쿼리](../log-query/log-query-overview.md) 기능을 사용해 운영상의 문제를 해결할 수 있습니다.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

이 솔루션을 설치하고 구성하려면 다음 항목이 필요합니다.

- 조직 Office 365 구독
- 전역 관리자 사용자 계정의 자격 증명
- 감사 데이터를 수신하려면 Office 365 구독에서 [감사를 구성](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)해야 합니다.  [사서함 감사](https://technet.microsoft.com/library/dn879651.aspx)는 별도로 구성합니다.  감사를 구성하지 않아도 솔루션을 설치하고 기타 데이터를 수집할 수는 있습니다.
 

## <a name="management-packs"></a>관리 팩

이 솔루션은 [연결된 관리 그룹](../platform/om-agents.md)에 관리 팩을 설치하지 않습니다.
  

## <a name="install-and-configure"></a>설치 및 구성

먼저 [구독에 Office 365 솔루션](solutions.md#install-a-monitoring-solution)을 추가합니다. 추가된 후에는 이 섹션의 구성 단계를 수행하여 Office 365 구독에 대한 액세스 권한을 부여해야 합니다.

### <a name="required-information"></a>필요한 정보

이 절차를 시작하려면 먼저 다음 정보를 수집해야 합니다.

Log Analytics 작업 영역에서 수집할 정보:

- 작업 영역 이름: Office 365 데이터가 수집되는 작업 영역입니다.
- 리소스 그룹 이름: 작업 영역을 포함하는 리소스 그룹입니다.
- Azure 구독 ID: 작업 영역을 포함하는 구독입니다.

Office 365 구독에서 수집할 정보:

- 사용자 이름: 관리 계정의 메일 주소입니다.
- 테넌트 ID: Office 365 구독의 고유 ID입니다.
- 클라이언트 ID: Office 365 클라이언트를 나타내는 16자 길이의 문자열입니다.
- 클라이언트 암호: 인증에 필요한 암호화된 문자열입니다.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Azure Active Directory에서 Office 365 애플리케이션 만들기

첫 번째 단계는 Azure Active Directory에 관리 솔루션이 Office 365 솔루션에 액세스할 때 사용할 응용 프로그램을 만드는 것입니다.

1. Azure Portal([https://portal.azure.com](https://portal.azure.com/))에 로그인합니다.
1. **Azure Active Directory**를 선택한 다음, **앱 등록**을 선택합니다.
1. **새 애플리케이션 등록**을 클릭합니다.

    ![앱 등록 추가](media/solution-office-365/add-app-registration.png)
1. 애플리케이션 **이름** 및 **로그온 URL**을 입력합니다.  이름은 구체적이어야 합니다.  사용 하 여 `http://localhost` URL을 유지 _웹 앱 / API_ 에 대 한는 **응용 프로그램 유형**
    
    ![애플리케이션 만들기](media/solution-office-365/create-application.png)
1. **만들기**를 클릭하고 응용 프로그램 정보의 유효성을 검사합니다.

    ![앱 등록](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Office 365에 대한 애플리케이션 구성

1. **설정**을 클릭하여 **설정** 메뉴를 엽니다.
1. **속성**을 선택합니다. **다중 테넌트**를 _예_로 변경합니다.

    ![다중 테넌트 설정](media/solution-office-365/settings-multitenant.png)

1. **설정** 메뉴에서 **필요한 권한**을 선택하고 **추가**를 클릭합니다.
1. **API 선택**을 클릭한 다음, **Office 365 관리 API**를 클릭합니다. **Office 365 관리 API**를 클릭합니다. **선택**을 클릭합니다.

    ![API 선택](media/solution-office-365/select-api.png)

1. **권한 선택** 아래에서 **응용 프로그램 권한** 및 **위임된 권한**에 대해 다음 옵션을 선택합니다.
   - 조직의 서비스 상태 정보 읽기
   - 조직의 활동 데이터 읽기
   - 조직의 활동 보고서 읽기

     ![API 선택](media/solution-office-365/select-permissions.png)

1. **선택**, **완료**를 차례로 클릭합니다.
1. **권한 부여**를 클릭한 다음, 확인하라는 메시지가 나오면 **예**를 클릭합니다.

    ![권한 부여](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>응용 프로그램 키 추가

1. **설정** 메뉴에서 **키**를 선택합니다.
1. 새 키의 **설명** 및 **기간**을 입력합니다.
1. **저장**을 클릭한 다음, 생성되는 **값**을 복사합니다.

    ![구성](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>관리자 동의 추가

관리 계정을 처음으로 사용하는 경우 애플리케이션에 대한 관리자 동의를 제공해야 합니다. 이 작업은 PowerShell 스크립트를 사용하여 수행할 수 있습니다. 

1. 다음 스크립트를 *office365_consent.ps1*로 저장합니다.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. 다음 명령을 사용하여 스크립트를 실행합니다. 자격 증명을 묻는 메시지가 두 번 표시됩니다. 먼저 Log Analytics 작업 영역용 자격 증명을 입력한 다음, Office 365 테넌트의 글로벌 관리자 자격 증명을 입력합니다.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    예제:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. 아래와 비슷한 창이 나타납니다. **Accept**를 클릭합니다.
    
    ![관리자 동의](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Log Analytics 작업 영역에 가입

마지막 단계는 애플리케이션을 Log Analytics 작업 영역에 가입하는 것입니다. 이 작업 역시 PowerShell 스크립트를 사용합니다.

1. 다음 스크립트를 *office365_subscription.ps1*로 저장합니다.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. 다음 명령을 사용하여 스크립트를 실행합니다.

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    예제:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>문제 해결

애플리케이션이 해당 작업 영역에 이미 가입되고 있거나 이 테넌트가 다른 작업 영역에 가입되어 있으면 이 오류가 표시될 수 있습니다.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

잘못된 매개 변수 값을 제공하면 다음 오류가 발생할 수 있습니다.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>제거

[관리 솔루션을 제거](solutions.md#remove-a-monitoring-solution)의 프로세스를 사용하여 Office 365 관리 솔루션을 제거할 수 있습니다. 이렇게 해도 Office 365에서 Azure Monitor로 데이터 수집이 중단되지 않습니다. 아래 절차에 따라 Office 365에서 구독을 취소하고 데이터 수집을 중지합니다.

1. 다음 스크립트를 *office365_unsubscribe.ps1*로 저장합니다.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. 다음 명령을 사용하여 스크립트를 실행합니다.

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    예제:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>데이터 수집

### <a name="supported-agents"></a>지원되는 에이전트

Office 365 솔루션은 [Log Analytics 에이전트](../platform/agent-data-sources.md)에서 데이터를 검색하지 않습니다.  즉, Office 365에서 직접 데이터를 검색합니다.

### <a name="collection-frequency"></a>수집 빈도

데이터가 처음으로 수집될 때까지 몇 시간이 걸릴 수 있습니다. 데이터 수집이 시작되면 Office 365는 레코드가 생성될 때마다 상세 데이터가 포함된 [webhook 알림](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications)을 Azure Monitor로 보냅니다. 이 레코드는 수신된 후 몇 분 안에 Azure Monitor에서 사용할 수 있습니다.

## <a name="using-the-solution"></a>솔루션 사용

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Office 365 솔루션을 Log Analytics 작업 영역에 추가하면 대시보드에 **Office 365** 타일이 추가됩니다. 이 타일은 현재 환경의 컴퓨터 수 및 그래픽 표현과 업데이트 준수를 표시합니다.<br><br>
![Office 365 요약 타일](media/solution-office-365/tile.png)  

**Office 365** 타일을 클릭하여 **Office 365** 대시보드를 엽니다.

![Office 365 대시보드](media/solution-office-365/dashboard.png)  

대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 범위 및 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 열 개의 경고를 나열합니다. 열 아래쪽의 모두 보기를 클릭하거나 열 머리글을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| 열 | 설명 |
|:--|:--|
| 작업 | 모니터링되는 모든 Office 365 구독의 활성 사용자에 대한 정보를 제공합니다. 시간에 따라 발생하는 활동의 수도 확인할 수 있습니다.
| Exchange | Add-MailboxPermission, Set-Mailbox 등의 Exchange Server 활동이 자세히 구분되어 표시됩니다. |
| SharePoint | 사용자가 SharePoint 문서에 대해 가장 많이 수행하는 활동이 표시됩니다. 이 타일에서 드릴다운하면 검색 페이지에 이 활동의 위치와 대상 문서 등 이러한 활동의 세부 정보가 표시됩니다. 예를 들어 파일 액세스 이벤트의 경우 액세스한 문서, 연결된 계정 이름 및 IP 주소를 확인할 수 있습니다. |
| Azure Active Directory | 사용자 암호 다시 설정, 로그인 시도 등 자주 수행하는 사용자 활동이 포함됩니다. 드릴다운하면 결과 상태와 같은 이러한 활동의 세부 정보를 확인할 수 있습니다. 이러한 정보는 대개 Azure Active Directory에서 의심스러운 활동을 모니터링하려는 경우에 유용합니다. |




## <a name="azure-monitor-log-records"></a>Azure Monitor 로그 레코드

Office 365 솔루션이 Azure Monitor의 Log Analytics 작업 영역에서 생성하는 모든 레코드의 **Type**은 **OfficeActivity**입니다.  **OfficeWorkload** 속성에 따라 해당 레코드가 참조하는 Office 365 서비스(Exchange, AzureActiveDirectory, SharePoint 또는 OneDrive)가 결정됩니다.  **RecordType** 속성은 작업의 유형을 지정합니다.  속성은 각 작업 유형에 따라 달라지며, 아래 표에 나와 있습니다.

### <a name="common-properties"></a>공용 속성

다음 속성은 모든 Office 365 레코드에 공통적으로 적용됩니다.

| 자산 | Description |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | 활동이 기록될 때 사용된 디바이스의 IP 주소입니다. IP 주소는 IPv4 또는 IPv6 주소 형식으로 표시됩니다. |
| OfficeWorkload | 레코드가 참조하는 office 365 서비스입니다.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| 작업(Operation) | 사용자 또는 관리자 활동의 이름입니다.  |
| OrganizationId | 조직의 Office 365 테넌트 GUID입니다. 이 값은 값이 나타나는 Office 365 서비스에 관계없이 조직에서 항상 동일하게 유지됩니다. |
| RecordType | 수행한 작업의 유형입니다. |
| ResultStatus | Operation 속성에 지정된 작업이 성공했는지 여부를 나타냅니다. 가능한 값은 Succeeded, PartiallySucceeded 또는 Failed입니다. Exchange 관리자 활동의 경우 값은 True 또는 False입니다. |
| UserId | 레코드가 기록된 원인인 작업을 수행한 사용자의 UPN(사용자 계정 이름)입니다. 예를 들면 my_name@my_domain_name과 같습니다. SHAREPOINT\system 또는 NTAUTHORITY\SYSTEM과 같은 시스템 계정이 수행한 활동에 대한 레코드도 포함됩니다. | 
| UserKey | UserId 속성에 나와 있는 사용자의 대체 ID입니다.  예를 들어 SharePoint, 비즈니스용 OneDrive 및 Exchange에서 사용자가 수행한 이벤트의 경우에는 이 속성에 PUID(Passport 고유 ID)가 입력됩니다. 시스템 계정이 수행한 이벤트와 기타 서비스에서 발생하는 이벤트의 경우 이 속성이 UserID 속성과 같은 값을 지정할 수도 있습니다.|
| UserType | 작업을 수행한 사용자의 유형입니다.<br><br>관리자<br>애플리케이션<br>DcAdmin<br>일반 <br>Reserved<br>ServicePrincipal<br>시스템 |


### <a name="azure-active-directory-base"></a>Azure Active Directory 기본 속성

다음 속성은 모든 Azure Active Directory 레코드에 공통적으로 적용됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD 이벤트의 유형입니다. |
| ExtendedProperties | Azure AD 이벤트의 확장 속성입니다. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory 계정 로그온

다음 레코드는 Active Directory 사용자가 로그온을 시도할 때 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| 애플리케이션 | Office 15 등의 계정 로그인 이벤트를 트리거하는 애플리케이션입니다. |
| 클라이언트 | 계정 로그인 이벤트에 사용된 클라이언트 디바이스, 디바이스 OS 및 디바이스 브라우저에 대한 세부 정보입니다. |
| LoginStatus | OrgIdLogon.LoginStatus에서 직접 생성되는 속성입니다. 알림 알고리즘을 사용하면 로그온 실패를 다양한 방식으로 매핑할 수 있습니다. |
| UserDomain | TII(테넌트 ID 정보)입니다. | 


### <a name="azure-active-directory"></a>Azure Active Directory

이러한 레코드는 Azure Active Directory 개체를 추가하거나 변경할 때 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Operation 속성에서 식별할 수 있는 작업이 수행된 대상 사용자입니다. |
| 행위자 | 작업을 수행한 사용자 또는 서비스 주체입니다. |
| ActorContextId | 작업자가 속한 조직의 GUID입니다. |
| ActorIpAddress | 작업자의 IP 주소(IPV4 또는 IPV6 주소 형식)입니다. |
| InterSystemsId | Office 365 서비스 내에서 구성 요소에 대한 작업을 추적하는 GUID입니다. |
| IntraSystemId |   작업을 추적하기 위해 Azure Active Directory에서 생성하는 GUID입니다. |
| SupportTicketId | "대신 작업"을 수행하는 상황에서 작업의 고객 지원 티켓 ID입니다. |
| TargetContextId | 대상 사용자가 속한 조직의 GUID입니다. |


### <a name="data-center-security"></a>데이터 센터 보안

다음 레코드는 데이터 센터 보안 감사 데이터에서 생성됩니다.  

| 자산 | 설명 |
|:--- |:--- |
| EffectiveOrganization | 권한 상승/cmdlet의 대상 테넌트 이름입니다. |
| ElevationApprovedTime | 권한 상승이 승인된 시간에 해당하는 타임스탬프입니다. |
| ElevationApprover | Microsoft 관리자의 이름입니다. |
| ElevationDuration | 권한 상승이 활성 상태였던 기간입니다. |
| ElevationRequestId |  권한 상승 요청의 고유 식별자입니다. |
| ElevationRole | 권한 상승이 요청된 역할입니다. |
| ElevationTime | 권한 상승의 시작 시간입니다. |
| Start_Time | cmdlet 실행의 시작 시간입니다. |


### <a name="exchange-admin"></a>Exchange 관리

다음 레코드는 Exchange 구성을 변경하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  cmdlet을 실행한 주체(조직 내 사용자, Microsoft 데이터 센터 담당자 또는 데이터 센터 서비스 계정, 위임된 관리자)를 지정합니다. 값이 False이면 조직 내 사용자가 cmdlet을 실행한 것입니다. 값이 True이면 데이터 센터 담당자, 데이터 센터 서비스 계정 또는 위임된 관리자가 cmdlet을 실행한 것입니다. |
| ModifiedObjectResolvedName |  cmdlet에 의해 수정된 개체의 사용자에게 친숙한 이름입니다. cmdlet이 개체를 수정하는 경우에만 이 속성이 기록됩니다. |
| OrganizationName | 테넌트의 이름입니다. |
| OriginatingServer | cmdlet이 실행된 서버의 이름입니다. |
| 매개 변수 | Operations 속성에 나와 있는 cmdlet과 함께 사용된 모든 매개 변수의 이름과 값입니다. |


### <a name="exchange-mailbox"></a>Exchange 사서함

다음 레코드는 Exchange 사서함을 변경하거나 추가하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | 브라우저 버전, Outlook 버전, 모바일 디바이스 정보 등 작업을 수행하는 데 사용된 전자 메일 클라이언트에 대한 정보입니다. |
| Client_IPAddress | 작업이 기록될 때 사용된 디바이스의 IP 주소입니다. IP 주소는 IPv4 또는 IPv6 주소 형식으로 표시됩니다. |
| ClientMachineName | Outlook 클라이언트를 호스트하는 컴퓨터 이름입니다. |
| ClientProcessName | 사서함에 액세스하는 데 사용된 전자 메일 클라이언트입니다. |
| ClientVersion | 전자 메일 클라이언트의 버전입니다. |
| InternalLogonType | 내부용으로 예약된 속성입니다. |
| Logon_Type | 사서함에 액세스하여 기록된 작업을 수행한 사용자의 유형을 나타냅니다. |
| LogonUserDisplayName |    작업을 수행한 사용자의 사용자에게 친숙한 이름입니다. |
| LogonUserSid | 작업을 수행한 사용자의 SID입니다. |
| MailboxGuid | 액세스한 사서함의 Exchange GUID입니다. |
| MailboxOwnerMasterAccountSid | 사서함 소유자 계정의 마스터 계정 SID입니다. |
| MailboxOwnerSid | 사서함 소유자의 SID입니다. |
| MailboxOwnerUPN | 액세스한 사서함의 소유자 전자 메일 주소입니다. |


### <a name="exchange-mailbox-audit"></a>Exchange 사서함 감사

다음 레코드는 사서함 감사 항목을 만들면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| 항목 | 작업을 수행한 항목을 나타냅니다. | 
| SendAsUserMailboxGuid | 전자 메일을 보내기 위해 액세스한 사서함의 Exchange GUID입니다. |
| SendAsUserSmtp | 가장하는 사용자의 SMTP 주소입니다. |
| SendonBehalfOfUserMailboxGuid | 대신 메일을 보내기 위해 액세스한 사서함의 Exchange GUID입니다. |
| SendOnBehalfOfUserSmtp | 대신 전자 메일을 보낸 사용자의 SMTP 주소입니다. |


### <a name="exchange-mailbox-audit-group"></a>Exchange 사서함 감사 그룹

다음 레코드는 Exchange 그룹을 변경하거나 추가하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | 그룹의 각 항목에 대한 정보입니다. |
| CrossMailboxOperations | 두 개 이상의 사서함에 대해 작업이 수행되었는지를 나타냅니다. |
| DestMailboxId | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 GUID를 지정합니다. |
| DestMailboxOwnerMasterAccountSid | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 소유자의 마스터 계정 SID에 대한 SID를 지정합니다. |
| DestMailboxOwnerSid | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함의 SID를 지정합니다. |
| DestMailboxOwnerUPN | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 소유자의 UPN을 지정합니다. |
| DestFolder | 이동 등의 작업에 대한 대상 폴더입니다. |
| 폴더 | 항목 그룹이 있는 폴더입니다. |
| 폴더 |     폴더를 선택한 후 삭제하는 등의 작업에서 사용된 원본 폴더에 대한 정보입니다. |


### <a name="sharepoint-base"></a>Sharepoint 기본 속성

다음 속성은 모든 SharePoint 레코드에 공통적으로 적용됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | SharePoint에서 이벤트가 발생했음을 나타냅니다. 가능한 값은 SharePoint 또는 ObjectModel입니다. |
| ItemType | 액세스했거나 수정한 개체의 형식입니다. 개체 형식에 대한 자세한 내용은 ItemType 표를 참조하세요. |
| MachineDomainInfo | 디바이스 동기화 작업에 대한 정보입니다. 이 정보는 요청에 있는 경우에만 보고됩니다. |
| MachineId |   디바이스 동기화 작업에 대한 정보입니다. 이 정보는 요청에 있는 경우에만 보고됩니다. |
| Site_ | 사용자가 액세스한 파일 또는 폴더가 있는 사이트의 GUID입니다. |
| Source_Name | 감사된 작업을 트리거한 엔터티입니다. 가능한 값은 SharePoint 또는 ObjectModel입니다. |
| UserAgent | 사용자의 클라이언트 또는 브라우저에 대한 정보입니다. 이 정보는 클라이언트 또는 브라우저에서 제공됩니다. |


### <a name="sharepoint-schema"></a>SharePoint 스키마

다음 레코드는 SharePoint 구성을 변경하면 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | 사용자 지정 이벤트에 대한 선택적 문자열입니다. |
| Event_Data |  사용자 지정 이벤트에 대한 선택적 페이로드입니다. |
| ModifiedProperties | 이 속성은 사이트 또는 사이트 모음 관리 그룹의 멤버로 사용자를 추가하는 등의 관리 이벤트에 대해 포함됩니다. 이 속성은 수정된 속성의 이름(예: 사이트 관리자 그룹), 수정된 속성의 새 값(예: 사이트 관리자로 추가된 사용자), 그리고 수정된 개체의 이전 값을 포함합니다. |


### <a name="sharepoint-file-operations"></a>SharePoint 파일 작업

다음 레코드는 SharePoint의 파일 작업에 대한 응답으로 생성됩니다.

| 자산 | 설명 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | 복사하거나 이동한 파일의 파일 확장명입니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| DestinationFileName | 복사하거나 이동한 파일의 이름입니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| DestinationRelativeUrl | 파일을 복사하거나 이동한 대상 폴더의 URL입니다. SiteURL, DestinationRelativeURL 및 DestinationFileName 매개 변수의 값 조합은 복사한 파일의 전체 경로 이름인 ObjectID 속성의 값과 같습니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| SharingType | 리소스를 공유한 사용자에게 할당된 공유 권한 유형입니다. UserSharedWith 매개 변수를 통해 이 사용자를 식별합니다. |
| Site_Url | 사용자가 액세스한 파일 또는 폴더가 있는 사이트의 URL입니다. |
| SourceFileExtension | 사용자가 액세스한 파일의 파일 확장명입니다. 액세스한 개체가 폴더이면 이 속성은 비어 있습니다. |
| SourceFileName |  사용자가 액세스한 파일 또는 폴더의 이름입니다. |
| SourceRelativeUrl | 사용자가 액세스한 파일이 포함된 폴더의 URL입니다. SiteURL, SourceRelativeURL 및 SourceFileName 매개 변수의 값 조합은 사용자가 액세스한 파일의 전체 경로 이름인 ObjectID 속성의 값과 같습니다. |
| UserSharedWith |  리소스를 공유한 사용자입니다. |




## <a name="sample-log-searches"></a>샘플 로그 검색

다음 테이블은 이 솔루션에 의해 수집된 업데이트 레코드에 대한 샘플 로그 검색을 제공합니다.

| 쿼리 | 설명 |
| --- | --- |
|Office 365 구독의 모든 작업 수 |OfficeActivity &#124; summarize count() by Operation |
|SharePoint 사이트 사용량|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl \| sort by Count asc|
|사용자 유형별 파일 액세스 작업|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|특정 키워드를 사용한 검색|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Exchange에서 외부 작업 모니터링|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>다음 단계

* [Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md)를 사용하여 자세한 업데이트 데이터를 확인합니다.
* [고유한 대시보드 만들기](../learn/tutorial-logs-dashboards.md)를 수행하여 자주 사용하는 Office 365 검색 쿼리를 표시합니다.
* 중요한 Office 365 활동에 대해 미리 알림을 받을 수 있도록 [경고 만들기](../platform/alerts-overview.md)를 수행합니다.  