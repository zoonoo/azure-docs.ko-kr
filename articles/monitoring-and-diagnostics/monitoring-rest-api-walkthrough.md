---
title: "Azure Monitoring REST API 연습 | Microsoft Docs"
description: "Azure Monitoring REST API에 대해 요청을 인증하고 Azure Monitoring REST API를 사용하는 방법"
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mcollier
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 454a85c4752ec9c7522ef147d5ce594ef5992c32
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure Monitoring REST API 연습
이 문서에서는 [Microsoft Azure Monitor REST API 참조](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 사용할 수 있도록 인증을 수행하는 방법을 살펴봅니다.         

Azure Monitor API를 통해 사용 가능한 기본 메트릭 정의(CPU 시간, 요청 등과 같은 메트릭 형식), 세분성 및 메트릭 값을 프로그래밍 방식으로 검색할 수 있습니다. 검색 후에는 Azure SQL Database, Azure Cosmos DB 또는 Azure Data Lake 등과 같은 별도의 데이터 저장소에 데이터를 저장할 수 있습니다. 여기에서 필요에 따라 추가적인 분석을 수행할 수 있습니다.

다양한 메트릭 데이터 포인트 작업 외에도 이 문서에서 설명한 것처럼 모니터 API를 사용하여 경고 규칙을 나열하고, 활동 로그를 확인하는 등, 다양한 작업을 수행할 수 있습니다. 사용 가능한 작업의 전체 목록은 [Microsoft Azure Monitor REST API 참조](https://msdn.microsoft.com/library/azure/dn931943.aspx)에서 확인하세요.

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor 요청 인증
첫 번째 단계는 요청을 인증하는 것입니다.

Azure Monitor API에 대해 실행되는 모든 작업은 Azure Resource Manager 인증 모델을 사용합니다. 따라서 모든 요청은 Azure AD(Azure Active Directory)로 인증되어야 합니다. 클라이언트 응용 프로그램을 인증하는 한 가지 방법은 Azure AD 서비스 주체를 만들고 인증(JWT) 토큰을 검색하는 것입니다. 다음 예제 스크립트에서는 PowerShell을 통해 Azure AD 서비스 주체를 만드는 것을 보여 줍니다. 상세한 연습은 [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password)를 참조하세요. [Azure Portal을 통해 서비스 주체를 만들 수도](../azure-resource-manager/resource-group-create-service-principal-portal.md)있습니다.

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Azure Monitor API를 쿼리하려면 클라이언트 응용 프로그램이 이전에 만든 서비스 주체를 인증에 사용해야 합니다. 다음 예제 PowerShell 스크립트에서는 JWT 인증 토큰을 가져오기 위해 [Active Directory 인증 라이브러리(ADAL)](../active-directory/active-directory-authentication-libraries.md) 을 사용하는 방법을 보여 줍니다. JWT 토큰은 Azure Monitor REST API에 대한 요청에서 HTTP 권한 부여 매개 변수의 일부로 전달됩니다.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

인증 설정 단계가 완료된 다음 Azure Monitor REST API에 대해 쿼리를 실행할 수 있습니다. 다음과 같이 두 가지 유용한 쿼리 가지가 있습니다.

1. 리소스에 대한 메트릭 정의 나열
2. 메트릭 값 검색 

## <a name="retrieve-metric-definitions"></a>메트릭 정의 검색 
> [!NOTE]
> Azure Monitor REST API를 사용하여 메트릭 정의를 검색하려면 API 버전으로 "2016-03-01"을 사용합니다.
>
>

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Azure Logic App의 경우 메트릭 정의가 다음 스크린샷과 유사하게 표시됩니다.

![Alt "메트릭 정의 응답의 JSON 보기"](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

자세한 내용은 [Azure Monitor REST API에서 리소스에 대한 메트릭 정의 나열](https://msdn.microsoft.com/library/azure/mt743621.aspx) 설명서를 참조하세요.

## <a name="retrieve-metric-values"></a>메트릭 값 검색 
사용 가능한 메트릭 정의를 알고나면 관련 메트릭 값을 검색할 수 있습니다. 모든 필터링 요청(예: ‘CpuTime’ 및 ‘Requests’ 메트릭 데이터 포인트 요청)에서 메트릭 이름 ‘value’(‘localizedValue’ 아님)를 사용합니다. 필터를 지정하지 않은 경우 기본 메트릭이 반환됩니다.

> [!NOTE]
> Azure Monitor REST API를 사용하여 메트릭 값을 검색하려면 API 버전으로 "2016-06-01"을 사용합니다.
>
>

**메서드**: GET

**요청 URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*

예를 들어, 주어진 범위와 시간 조직 1시간에 대해 RunsSucceeded 메트릭 데이터를 검색하려면 요청은 다음과 같습니다.

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

결과는 다음 스크린샷 예제와 비슷하게 나타납니다.

![Alt "평균 응답 시간 메트릭 값을 나타내는 JSON 응답"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

여러 데이터 또는 집계 포인트를 검색하려면 다음 예제에서처럼 메트릭 정의 이름과 집계 형식을 필터에 추가합니다.

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>ARMClient 사용
PowerShell 대신 아래와 같이 Windows 컴퓨터에서 [ARMClient](https://github.com/projectkudu/ARMClient) 를 사용할 수 있습니다. ARMClient는 자동으로 Azure AD 인증을 처리합니다(결과 JWT 토큰 포함). 다음 단계는 ARMClient를 사용하여 메트릭 데이터를 검색하는 방법을 개괄적으로 보여 줍니다.

1. [Chocolatey](https://chocolatey.org/) 및 [ARMClient](https://github.com/projectkudu/ARMClient)를 설치합니다.
2. 터미널 창에서 *armclient.exe login*을 입력합니다. 그러면 Azure에 로그인하라는 메시지가 표시됩니다.
3. *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*을 입력합니다.
4. *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01*을 입력합니다.

![Alt "ARMClient를 사용한 Azure Monitoring REST API 작업"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)

## <a name="retrieve-the-resource-id"></a>리소스 ID 검색
REST API를 사용하면 사용 가능한 메트릭 정의, 세분성, 관련 값을 이해하는 데 크게 도움이 됩니다. 이런 정보는 [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx)를 사용할 때 유용합니다.

앞의 코드에서 사용하는 리소스 ID는 원하는 Azure 리소스의 전체 경로입니다. 예를 들어 Azure Web App에 대해 쿼리하려면 리소스 ID는 다음과 같습니다.

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

다음 목록은 다양한 Azure 리소스에 대한 리소스 ID 형식의 몇 가지 예입니다.

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **탄력적인 SQL 풀** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database(v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **VM Scale Sets** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Event Hubs** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Azure Resource Explorer 등, 다른 방법으로 리소스 ID를 검색하고 Azure 포털과 PowerShell 또는 Azure CLI를 통해 원하는 리소스를 확인할 수 있습니다.

### <a name="azure-resource-explorer"></a>Azure 리소스 탐색기
원하는 리소스에 대한 리소스 ID를 찾을 때는 [Azure Resource Explorer](https://resources.azure.com) 도구를 사용하는 것도 유용한 방법입니다. 원하는 리소스로 이동한 다음 스크린샷에서처럼 표시된 ID를 살펴봅니다.

![Alt "Azure Resource Explorer"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure 포털
Azure 포털에서 리소스 ID를 가져올 수도 있습니다. 이렇게 하려면 원하는 리소스로 이동한 다음 속성을 선택합니다. 리소스 ID는 다음 스크린샷에 표시 된 것처럼 속성 블레이드에 표시됩니다.

![Alt "Azure 포털의 속성 블레이드에 표시된 리소스 ID”](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell cmdlet을 사용해서 리소스 ID를 검색할 수도 있습니다. 예를 들어, Azure Web App의 리소스 ID를 가져오려면 다음 스크린샷에서처럼 Get-AzureRmWebApp cmdlet을 실행합니다.

![Alt "PowerShell을 통해 구한 리소스 ID”](./media/monitoring-rest-api-walkthrough/resourceid_powershell.png)

### <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용하여 리소스 ID를 검색하려면 다음 스크린샷에서처럼 '--json 옵션을 지정하여 'azure webapp show' 명령을 실행합니다.

![Alt "PowerShell을 통해 구한 리소스 ID”](./media/monitoring-rest-api-walkthrough/resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>활동 로그 데이터 검색
메트릭 정의 및 관련 값 작업 외에도, Azure 리소스와 관련한 다른 흥미로운 정보를 검색할 수도 있습니다. 예를 들어, [활동 로그](https://msdn.microsoft.com/library/azure/dn931934.aspx) 데이터를 쿼리할 수 있습니다. 다음 예제에서는 Azure Monitor REST API를 사용하여 Azure 구독에 대해 특정 날짜 범위의 활동 로그 데이터를 쿼리합니다.

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>다음 단계
* [모니터링 개요](monitoring-overview.md)를 검토합니다.
* [Azure Monitor에서 지원되는 메트릭](monitoring-supported-metrics.md)을 확인합니다.
* [Microsoft Azure Monitor REST API 참조](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 검토합니다.
* [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx)를 검토합니다.

