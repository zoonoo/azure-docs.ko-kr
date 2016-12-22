---
title: "PowerShell에서 Application Insights 리소스, 경고 및 가용성 테스트 만들기 | Microsoft Docs"
description: "Azure Resource Manager 템플릿을 사용하여 Application Insights 리소스의 관리를 자동화합니다."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc2d3c3fee5abbab0eab16c106c88c8753e703cc
ms.openlocfilehash: f6302d7d11691635c286164f11be74ff3a1ad8dd


---
# <a name="create-application-insights-resources-using-powershell"></a>PowerShell을 사용하여 Application Insights 리소스 만들기
이 문서에서는 Azure에서 [Application Insights](app-insights-overview.md) 리소스를 자동으로 만드는 방법을 보여 줍니다. 예를 들어 빌드 프로세스의 일부로 이 작업을 수행할 수 있습니다. 기본 Application Insights 리소스와 함께 [가용성 웹 테스트](app-insights-monitor-web-app-availability.md), [경고 설정](app-insights-alerts.md) 및 다른 Azure 리소스를 만들 수 있습니다.

이러한 리소스를 만드는 데 핵심 사항은 [Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)용 JSON 템플릿입니다. 간단히 말하면 절차는 다음과 같습니다. 기존 리소스의 JSON 정의를 다운로드하고, 이름과 같은 특정 값을 매개 변수화한 다음 새 리소스를 만들려고 할 때마다 템플릿을 실행합니다. 여러 리소스를 함께 패키지하여 모두 한꺼번에 만들 수 있습니다(예: 가용성 테스트, 경고 및 연속 내보내기에 대한 저장소를 포함하는 앱 모니터). 일부 매개 변수화에 있는 약간의 미묘한 사항은 여기서 설명합니다.

## <a name="one-time-setup"></a>일 회 설정
아직 Azure 구독에서 PowerShell을 사용한 적이 없을 경우:

스크립트를 실행하려는 컴퓨터에 Azure Powershell 모듈을 설치합니다.

1. [Microsoft 웹 플랫폼 설치 관리자(v5 이상)](http://www.microsoft.com/web/downloads/platform.aspx)를 설치합니다.
2. 이를 사용하여 Microsoft Azure Powershell을 설치합니다.

## <a name="copy-the-json-for-existing-resources"></a>기존 리소스에 대한 JSON 복사
1. 자동으로 생성하려는 것과 비슷하게 프로젝트에 대한 [Application Insights](app-insights-overview.md) 를 설정합니다. 원하는 경우 웹 테스트 및 경고를 추가합니다.
2. 새 .json 파일을 만듭니다. 이 예제에서는 `template1.json`입니다. 아래 내용을 이 파일에 복사합니다.

    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }

            // Any other resources go here
          ]
        }

    ```

    이 템플릿은 주 리소스 외에 하나의 가용성 테스트를 설정합니다.


1. [Azure 리소스 관리자](https://resources.azure.com/)를 엽니다. `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`를 통해 응용 프로그램 리소스로 이동합니다. 
   
    ![Azure 리소스 탐색기에서 탐색](./media/app-insights-powershell/01.png)
   
    *구성 요소* 는 응용 프로그램을 표시하기 위한 기본 Application Insights 리소스입니다. 연결된 경고 규칙 및 가용성 웹 테스트에 대한 별도의 리소스가 있습니다.
2. 구성 요소의 JSON을 `template1.json`의 적절한 위치에 복사합니다.
3. 다음 속성을 삭제합니다.
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. webtests 및 alertrules 섹션을 열고 개별 항목에 대한 JSON을 템플릿에 복사합니다. (webtests 또는 alertrules 노드에서 복사하지 말고 그 아래에 있는 항목으로 이동)
   
    각 웹 테스트에는 연결된 경고 규칙이 있으므로 둘 다 복사해야 합니다.
   
    각 웹 테스트에는 해당 경고 규칙이 있습니다. 먼저 웹 테스트가 진행되어야 합니다.
   
    메트릭에 대한 경고를 포함할 수도 있습니다. [메트릭 이름](app-insights-powershell-alerts.md#metric-names)
5. 각 리소스에 다음 줄을 삽입합니다.
   
    `"apiVersion": "2015-05-01",`

## <a name="parameterize-the-template"></a>템플릿 매개 변수화
이제 특정 이름을 매개 변수로 대체해야 합니다. [템플릿을 매개 변수화](../azure-resource-manager/resource-group-authoring-templates.md)하려면 [도우미 함수 집합](../azure-resource-manager/resource-group-template-functions.md)을 사용하여 식을 작성합니다. 

문자열의 일부만 매개 변수화할 수 없으므로 `concat()`을 사용하여 문자열을 빌드합니다.

다음은 만들 수 있는 대체 예제입니다. 각 대체가 여러 번 발생합니다. 템플릿에서 다른 사항이 필요할 수 있습니다. 이러한 예제에서는 템플릿의 위쪽에서 정의한 매개 변수 및 변수를 사용합니다.

| find | 대체 |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (소문자) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>GUID 및 ID를 삭제합니다. |

## <a name="set-dependencies-between-the-resources"></a>리소스 간의 종속성 설정
Azure에서는 엄격한 순서로 리소스를 설정해야 합니다. 다음 설정 시작 전에 하나의 설정이 완료되게 하려면 종속성 줄을 추가합니다.

* 가용성 테스트 리소스:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 경고 리소스:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

가용성 테스트는 실제로 테스트 자체와 테스트 결과에 따라 발생하는 경고 규칙의 두 부분으로 이루어져 있습니다.

## <a name="create-application-insights-resources"></a>Application Insights 리소스 만들기
1. PowerShell에서 Azure에 로그인합니다.
   
    `Login-AzureRmAccount`
2. 다음과 같은 명령을 실행합니다.
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -url http://myapp.com `
               -text "Welcome!"
   
    ``` 
   
   * -ResourceGroupName은 새 리소스를 만들려는 그룹입니다.
   * -templateFile은 사용자 지정 매개 변수 앞에 있어야 합니다.
   * -appName은 만들려는 리소스의 이름입니다.
   * -webTestName은 만들려는 웹 테스트의 이름입니다.
   * -Url은 웹앱의 url입니다.
   * -text는 웹 페이지에 표시되는 문자열입니다.

## <a name="to-get-the-instrumentation-key"></a>계측 키를 가져오려면
응용 프로그램 리소스를 만든 후 iKey가 필요할 수 있습니다. 

```PS

    $resource = Get-AzureRmResource -ResourceId "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<YOUR RESOURCE GROUP>/providers/Microsoft.Insights/components/<YOUR APP NAME>"

    $resource.Properties.InstrumentationKey
```


## <a name="an-example"></a>예제
만든 전체 템플릿은 다음과 같습니다. 응용 프로그램 구성 요소, 가용성 테스트, 가용성 테스트 경고 및 응답 시간 메트릭에 대한 경고가 포함됩니다.

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type": "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]",
    "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //
      // App resource
      //
      "name": "[parameters('appName')]",
      "type": "Microsoft.Insights/components",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "East US", // Set to preferred location 
      "properties": {
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { "applicationType": "web" }
    },
    {
      //
      // Availability test
      //
      "name": "[variables('testName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "East US", // Set to preferred location
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[parameters('webTestName')]",
        "Description": "n",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', parameters('webTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('text'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //
      // Alert rule for the availability test
      //
      "name": "[variables('alertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }

    },
    {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": 3, //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
  ]
}


```


## <a name="next-steps"></a>다음 단계
다른 자동화 문서:

* [Application Insights 리소스 만들기](app-insights-powershell-script-create-resource.md) - 템플릿을 사용하지 않는 빠른 방법입니다.
* [경고 설정](app-insights-powershell-alerts.md)
* [웹 테스트 만들기](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Application Insights에 Azure 진단 보내기](app-insights-powershell-azure-diagnostics.md)
* [Github에서 Azure로 배포](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [릴리스 주석 만들기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)




<!--HONumber=Nov16_HO4-->


