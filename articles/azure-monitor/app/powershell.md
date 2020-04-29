---
title: PowerShell 사용하여 Azure Application Insights 자동화 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용 하 여 PowerShell에서 리소스, 경고 및 가용성 테스트 만들기 및 관리를 자동화 합니다.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275881"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>PowerShell을 사용 하 여 Application Insights 리소스 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서는 Azure Resource Management를 사용하여 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 리소스의 생성 및 업데이트를 자동화하는 방법을 보여줍니다. 예를 들어 빌드 프로세스의 일부로 이 작업을 수행할 수 있습니다. 기본 Application Insights 리소스와 함께 [가용성 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md)를 만들고, [경고](../../azure-monitor/app/alerts.md)를 설정하고, [가격 책정 계층](pricing.md)을 설정하고, 기타 Azure 리소스를 만들 수 있습니다.

이러한 리소스를 만드는 데 핵심 사항은 [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md)용 JSON 템플릿입니다. 기본 절차는 다음과 같습니다. 기존 리소스의 JSON 정의를 다운로드 합니다. 이름 등의 특정 값 매개 변수화 그런 다음 새 리소스를 만들려는 때마다 템플릿을 실행 합니다. 여러 리소스를 함께 패키지하여 모두 한꺼번에 만들 수 있습니다(예: 가용성 테스트, 경고 및 연속 내보내기에 대한 스토리지를 포함하는 앱 모니터). 일부 매개 변수화에 있는 약간의 미묘한 사항은 여기서 설명합니다.

## <a name="one-time-setup"></a>일 회 설정
아직 Azure 구독에서 PowerShell을 사용한 적이 없을 경우:

스크립트를 실행하려는 컴퓨터에 Azure Powershell 모듈을 설치합니다.

1. [Microsoft 웹 플랫폼 설치 관리자(v5 이상)](https://www.microsoft.com/web/downloads/platform.aspx)를 설치합니다.
2. 이를 사용하여 Microsoft Azure Powershell을 설치합니다.

리소스 관리자 템플릿 사용 외에도 Application Insights 리소스를 프로그래밍 방식으로 쉽게 구성할 수 있도록 하는 다양 한 [Application Insights PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)이 있습니다. Cmdlet에 의해 활성화 되는 기능은 다음과 같습니다.

* Application Insights 리소스 만들기 및 삭제
* Application Insights 리소스 및 해당 속성의 목록을 가져옵니다.
* 연속 내보내기 만들기 및 관리
* 응용 프로그램 키 만들기 및 관리
* 일일 상한 설정
* 가격 책정 계획 설정

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>PowerShell cmdlet을 사용 하 여 Application Insights 리소스 만들기

[AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) cmdlet을 사용 하 여 Azure 미국 동부 데이터 센터에 새 Application Insights 리소스를 만드는 방법은 다음과 같습니다.

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 Application Insights 리소스 만들기

리소스 관리자 템플릿을 사용 하 여 새 Application Insights 리소스를 만드는 방법은 다음과 같습니다.

### <a name="create-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기

새 .json 파일을 만듭니다. 이 예제에서는 `template1.json`입니다. 아래 내용을 이 파일에 복사합니다.

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>리소스 관리자 템플릿을 사용 하 여 새 Application Insights 리소스 만들기

1. PowerShell에서를 사용 하 여 Azure에 로그인 합니다.`$Connect-AzAccount`
2. 다음을 사용 하 여 컨텍스트를 구독으로 설정 합니다.`Set-AzContext "<subscription ID>"`
2. 새 배포를 실행 하 여 새 Application Insights 리소스를 만듭니다.
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`은 새 리소스를 만들려는 그룹입니다.
   * `-TemplateFile`은 사용자 지정 매개 변수 앞에 있어야 합니다.
   * `-appName` 만들려는 리소스의 이름입니다.

다른 매개 변수를 추가할 수 있습니다. 템플릿의 매개 변수 섹션에서 해당 설명을 찾을 수 있습니다.

## <a name="get-the-instrumentation-key"></a>계측 키 가져오기

애플리케이션 리소스를 만든 후 계측 키가 필요할 수 있습니다. 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Application Insights 리소스의 여러 다른 속성 목록을 보려면 다음을 사용 합니다.

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Cmdlet을 통해 추가 속성을 사용할 수 있습니다.
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

이러한 cmdlet의 매개 변수에 대 한 [자세한 설명서](https://docs.microsoft.com/powershell/module/az.applicationinsights) 를 참조 하세요.  

## <a name="set-the-data-retention"></a>데이터 보존 설정 

Application Insights 리소스에 대 한 현재 데이터 보존을 얻으려면 OSS 도구 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용할 수 있습니다.  [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 및 [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)에서 ARMClient에 대해 자세히 알아보세요.  현재 보존을 가져오는를 `ARMClient`사용 하는 예제는 다음과 같습니다.

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

보존을 설정 하기 위해 명령은 유사 하 게 배치 됩니다.

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

위의 템플릿을 사용 하 여 데이터 보존을 365 일로 설정 하려면 다음을 실행 합니다.

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

다음 스크립트를 사용 하 여 보존 기간을 변경할 수도 있습니다. 이 스크립트를 복사 하 여 `Set-ApplicationInsightsRetention.ps1`다른 이름으로 저장 합니다.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

그런 다음이 스크립트를 다음과 같이 사용할 수 있습니다.

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>일일 상한 설정

일일 상한 속성을 가져오려면 [AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet을 사용 합니다. 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

일일 상한 속성을 설정 하려면 동일한 cmdlet을 사용 합니다. 예를 들어 cap를 300 g b/일로 설정 하려면

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

[ARMClient](https://github.com/projectkudu/ARMClient) 를 사용 하 여 일일 상한 매개 변수를 가져오고 설정할 수도 있습니다.  현재 값을 가져오려면 다음을 사용 합니다.

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>일일 상한 다시 설정 시간 설정

일일 상한 다시 설정 시간을 설정 하려면 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용 하면 됩니다. 다음은를 사용 하 `ARMClient`여 다시 설정 시간을 새 시간 (이 예제에서는 12:00 UTC)으로 설정 하는 예제입니다.

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>가격 책정 계획 설정 

현재 가격 책정 계획을 얻으려면 [AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet을 사용 합니다.

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

가격 책정 계획을 설정 하려면 `-PricingPlan` 지정 된와 동일한 cmdlet을 사용 합니다.  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

위의 리소스 관리자 템플릿을 사용 하 여 기존 Application Insights 리소스에 대 한 가격 책정 계획을 설정 하 고 "microsoft Insights/components" 리소스와 청구 리소스에서 `dependsOn` 노드를 생략할 수도 있습니다. 예를 들어 GB 당 요금제 (이전의 기본 계획)로 설정 하려면 다음을 실행 합니다.

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

는 `priceCode` 다음과 같이 정의 됩니다.

|priceCode|계획|
|---|---|
|1|GB 당 (이전의 기본 계획 이라고 명명)|
|2|노드당 (이전에는 엔터프라이즈 계획 이름)|

마지막으로 [ARMClient](https://github.com/projectkudu/ARMClient) 를 사용 하 여 가격 책정 계획과 일일 상한 매개 변수를 가져오고 설정할 수 있습니다.  현재 값을 가져오려면 다음을 사용 합니다.

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

다음을 사용 하 여 이러한 모든 매개 변수를 설정할 수 있습니다.

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

일일 한도를 200 g b/일로 설정 하 고, 일일 캡 다시 설정 시간을 12:00 UTC로 구성 하 고, cap에 도달 하 여 경고 수준이 충족 되 면 전자 메일을 보내고, 경고 임계값을 cap의 90%로 설정 합니다.  

## <a name="add-a-metric-alert"></a>메트릭 경고 추가

메트릭 경고 만들기를 자동화 하려면 [메트릭 경고 템플릿 문서](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert) 를 참조 하세요.


## <a name="add-an-availability-test"></a>가용성 테스트 추가

가용성 테스트를 자동화 하려면 [메트릭 경고 템플릿 문서](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)를 참조 하세요.

## <a name="add-more-resources"></a>리소스 추가

다양한 종류의 다른 리소스의 생성을 자동화하려면 수동으로 예제를 만든 다음 [Azure Resource Manager](https://resources.azure.com/)에서 해당 코드를 복사하고 매개 변수화합니다. 

1. [Azure 리소스 관리자](https://resources.azure.com/)를 엽니다. `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`를 통해 애플리케이션 리소스로 이동합니다. 
   
    ![Azure 리소스 탐색기에서 탐색](./media/powershell/01.png)
   
    *구성 요소*는 애플리케이션을 표시하기 위한 기본 Application Insights 리소스입니다. 연결된 경고 규칙 및 가용성 웹 테스트에 대한 별도의 리소스가 있습니다.
2. 구성 요소의 JSON을 `template1.json`의 적절한 위치에 복사합니다.
3. 다음 속성을 삭제합니다.
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. `webtests` 및 `alertrules` 섹션을 열고 개별 항목에 대 한 JSON을 템플릿에 복사 합니다. `webtests` 또는 `alertrules` 노드에서 복사 하지 않습니다. 아래 항목으로 이동 합니다.
   
    각 웹 테스트에는 연결된 경고 규칙이 있으므로 둘 다 복사해야 합니다.
   
    메트릭에 대한 경고를 포함할 수도 있습니다. [메트릭 이름](powershell-alerts.md#metric-names)입니다.
5. 각 리소스에 다음 줄을 삽입합니다.
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>템플릿 매개 변수화
이제 특정 이름을 매개 변수로 대체해야 합니다. [템플릿을 매개 변수화](../../azure-resource-manager/templates/template-syntax.md)하려면 [도우미 함수 집합](../../azure-resource-manager/templates/template-functions.md)을 사용하여 식을 작성합니다. 

문자열의 일부만 매개 변수화할 수 없으므로 `concat()`을 사용하여 문자열을 빌드합니다.

다음은 만들 수 있는 대체 예제입니다. 각 대체가 여러 번 발생합니다. 템플릿에서 다른 사항이 필요할 수 있습니다. 이러한 예제에서는 템플릿의 위쪽에서 정의한 매개 변수 및 변수를 사용합니다.

| 찾기 | 대체 |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (소문자) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>리소스 간의 종속성 설정
Azure에서는 엄격한 순서로 리소스를 설정해야 합니다. 다음 설정 시작 전에 하나의 설정이 완료되게 하려면 종속성 줄을 추가합니다.

* 가용성 테스트 리소스:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 가용성 테스트에 대한 경고 리소스:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>다음 단계
다른 자동화 문서:

* [Application Insights 리소스 만들기](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) - 템플릿을 사용하지 않는 빠른 방법입니다.
* [경고 설정](powershell-alerts.md)
* [웹 테스트 만들기](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Application Insights Azure 진단 보내기](powershell-azure-diagnostics.md)
* [GitHub에서 Azure로 배포](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [릴리스 주석 만들기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)