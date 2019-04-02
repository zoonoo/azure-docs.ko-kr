---
title: Azure App Services 성능 모니터링 | Microsoft Docs
description: Azure App Services에 대한 애플리케이션 성능 모니터링입니다. 성능 경고 설정 및 부하 및 응답 시간, 종속성 정보를 차트입니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 7386f6bd92143cf3fb7b37725900425f99371cd0
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804995"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service 성능 모니터링

.NET 및.NET Core에서 Azure App Service에서 실행 하는 기반된 웹 응용 프로그램 모니터링을 사용 하도록 설정 하면 그 어느 때 보다 쉽게 되었습니다. 수동으로 사이트 확장을 설치 해야 하는 이전에, 하지만 최신 확장/에이전트에 빌드된 앱 서비스 이미지를 기본적으로 합니다. 이 문서는 Application Insights 모니터링을 사용 하는 과정을 안내해 뿐만 아니라 대규모 배포에 대 한 프로세스를 자동화 하는 것에 대 한 예비 지침을 제공 합니다.

> [!NOTE]
> Application Insights 사이트 확장을 통해 수동으로 추가 **개발 도구** > **확장** 는 사용 되지 않습니다. 확장의 안정적인 최신 릴리스는 이제 [사전](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) App Service 이미지의 일부로. 파일에 위치한 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` 안정적인 릴리스마다 자동으로 업데이트 됩니다. 모니터링을 사용 하도록 에이전트 기반 지침을 따르는 경우를 사용 되지 않는 확장을 자동으로 제거 됩니다 아래.

## <a name="enable-application-insights"></a>Application Insights 사용

두 가지 방법으로 호스트 된 Azure App Services 응용 프로그램에 대 한 응용 프로그램 모니터링을 사용 하도록 설정 하려면:

* **에이전트 기반 응용 프로그램 모니터링** (ApplicationInsightsAgent).  
    * 이 메서드는 가장을 사용 하기 쉬운 및 고급 구성은 필요 하지 않습니다. 이 라고도 런타임은 모니터링 합니다. Azure App Services에 대 한이 수준의 모니터링을 사용 하도록 설정 하면 최소 권장 하 고 필요한 수동 계측을 통해 고급 모니터링 하는지 여부를 평가할 수 있습니다 특정 시나리오에 따라 다음.

* **수동으로 코드를 통해 응용 프로그램을 계측** Application Insights SDK를 설치 하 여 합니다.

    * 이 방법은 사용자 지정할 수 있는 훨씬 더 적은 [Application Insights SDK NuGet 패키지에 종속성을 추가](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)합니다. 이 메서드는 또한 패키지의 최신 버전으로 업데이트를 직접 관리 해야 의미 합니다.

    * 이벤트 추적/종속성 에이전트 기반 모니터링에 대해 기본적으로 캡처되지 않습니다 사용자 지정 API 호출을 수행 해야 할 경우이 메서드를 사용 해야 합니다. 체크 아웃 합니다 [사용자 지정 이벤트 및 메트릭 문서에 대 한 API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 자세한 합니다.

> [!NOTE]
> 에이전트 기반 모니터링 및 수동 SDK 기반 계측 하는 경우 수동 계측이 설정만 적용 됩니다 검색 됩니다. 중복 데이터 방지 하는 것이 전송 합니다. 이 체크 아웃에 대해 자세히 알아보려면 합니다 [문제 해결 섹션](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) 아래.

## <a name="enable-agent-based-monitoring-net"></a>에이전트 기반 모니터링.NET을 사용 하도록 설정

1. App Service의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

     > [!NOTE]
     > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거**됩니다. 

     ![웹앱 계측](./media/azure-web-apps/create-resource-01.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. 기본적으로 ASP.NET 앱 모니터링은 두 개의 서로 다른 수준의 컬렉션입니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options-new.png)

   * .NET **기본 수집** 수준은 필수적인 단일 인스턴스 APM 기능을 제공합니다.

   * .NET **추천 수집** 수준은 다음과 같습니다.
       * CPU, 메모리 및 I/O 사용량 추세를 추가합니다.
       * 요청/종속성 경계 간에 마이크로 서비스를 상호 연결합니다.
       * 사용량 추세를 수집하고, 가용성 결과와 트랜잭션의 상관 관계를 사용하도록 설정합니다.
       * 호스트 프로세스에서 처리되지 않은 예외를 수집합니다.
       * 샘플링을 사용하는 경우 부하 상태에서 APM 메트릭 정확도가 향상됩니다.

3. 이전에 applicationinsights.config 파일을 통해 제어할 수 있습니다.는 샘플링 같은 설정을 구성 하는 해당 접두사를 사용 하 여 응용 프로그램 설정을 통해 이러한 동일한 설정을 사용 하 여를 이제 작용할 수 있습니다. 

    * 예를 들어 초기 샘플링 비율을 변경 하려면 만들면의 응용 프로그램 설정: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` 고 값이 `100`합니다.

    * 지원 되는 적응 샘플링 원격 분석 프로세서 설정이 목록을 참조할 수 있습니다 합니다 [코드](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) 하 고 [관련 설명서](https://docs.microsoft.com/azure/azure-monitor/app/sampling)합니다.

## <a name="enable-agent-based-monitoring-net-core"></a>.NET Core 모니터링 에이전트 기반을 사용 하도록 설정

다음 버전의.NET Core 지원 됩니다. ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

.NET Core, 자체 포함된 배포 및 ASP.NET Core 3.0에서 전체 프레임 워크를 대상으로 하는 현재 **지원 되지 않습니다** 에이전트/확장 기반 모니터링을 사용 합니다. ([수동 계측이](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 코드를 통해 모든 이전 시나리오에서 작동 합니다.)

1. App Service의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

     > [!NOTE]
     > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거**됩니다. 

     ![웹앱 계측](./media/azure-web-apps/create-resource-01.png)

2. 사용 하는 리소스를 지정한 다음 원하는 응용 프로그램에 대 한 플랫폼별 데이터를 수집 하려면 Application Insights를 선택할 수 있습니다. .NET core 제공 **컬렉션을 권장** 또는 **비활성** .NET Core 2.0, 2.1 및 2.2에 대 한 합니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>클라이언트 쪽 모니터링.NET을 사용 하도록 설정

클라이언트 쪽 모니터링 하는 것은 ASP.NET에 대해 옵트인 합니다. 사용 하려면 클라이언트 쪽 모니터링:

* 선택 **설정을** > * * * * 응용 프로그램 설정 * * *
   * 응용 프로그램 설정에서 새로 추가 **앱 설정 이름은** 하 고 **값**:

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `true`

   * 설정을 **저장**하고 앱을 **다시 시작**합니다.

![응용 프로그램의 스크린 샷 설정 UI](./media/azure-web-apps/appinsights-javascript-enabled.png)

클라이언트 쪽 모니터링을 제거 하거나, 응용 프로그램 설정에서 연결 된 키-값 쌍을 사용 하지 않도록 설정 하거나 값을 false로 설정 합니다.

## <a name="enable-client-side-monitoring-net-core"></a>클라이언트 쪽 모니터링 하는.NET Core를 사용 하도록 설정

클라이언트 쪽 모니터링은 **기본적으로 사용 하도록 설정** 사용 하 여.NET Core 앱에 대 한 **컬렉션 권장**'APPINSIGHTS_JAVASCRIPT_ENABLED'를 설정 하는 앱이 있는지 여부에 관계 없이 합니다.

어떤 이유로 클라이언트 쪽 모니터링을 사용 하지 않도록 설정 하려는 경우:

* 선택 **설정을** > **응용 프로그램 설정**
   * 응용 프로그램 설정에서 새로 추가 **앱 설정 이름은** 하 고 **값**:

     name: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `false`

   * 설정을 **저장**하고 앱을 **다시 시작**합니다.

![응용 프로그램의 스크린 샷 설정 UI](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>모니터링 자동화

Application Insights로 원격 분석 수집을 사용 하도록 설정 하기 위해 응용 프로그램 설정에만 설정 해야 합니다.

   ![사용 가능한 Application Insights 설정 사용 하 여 app Service 응용 프로그램 설정](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>응용 프로그램 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어 하는 기본 확장입니다. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  기본 모드에 필수적인 기능 최적의 성능을 보장 하기 위해 사용 됩니다. | `default` 또는 `recommended`입니다. |
|InstrumentationEngine_EXTENSION_VERSION | 제어 하는 경우 이진 재작성 엔진 `InstrumentationEngine` 켜 집니다. 이 설정은 성능에 영향 및 콜드 시작/시작 시간에 영향을 줍니다. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL 및 Azure 테이블이 텍스트 컨트롤 종속성 호출이 함께 캡처됩니다. 성능 경고:이 설정을 사용 하려면를 `InstrumentationEngine`입니다. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 앱 서비스 응용 프로그램 설정

App Services에 대 한 응용 프로그램 설정을 관리 하 고 사용 하 여 구성할 수 있습니다 [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)합니다. Azure Resource Manager automation을 사용 하 여 또는 기존 리소스의 설정을 수정 하는 것에 대 한 새 App Service 리소스를 배포 하는 경우이 메서드를 사용할 수 있습니다.

App service의 응용 프로그램 설정 JSON의 기본 구조는 다음과 같습니다.

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Application Insights에 대해 구성 된 응용 프로그램 설정 사용 하 여 Azure Resource Manager 템플릿 예제에 대 한이 [템플릿을](https://github.com/Andrew-MSFT/BasicImageGallery) 유용할 수 있습니다, 터 섹션을 특히 [238 줄](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)합니다.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights 리소스 및 새로 만든된 App Service에 링크 생성을 자동화 합니다.

구성 된 모든 기본 Application Insights 설정을 사용 하 여 Azure Resource Manager 템플릿 만들기, 사용 하도록 설정 하는 Application Insights를 사용 하 여 새 웹 앱을 만드는 것 처럼 프로세스를 시작 합니다.

선택 **Automation 옵션**

   ![App Service 웹 앱 만들기 메뉴](./media/azure-web-apps/create-web-app.png)

이 옵션에는 구성 된 모든 필수 설정을 사용 하 여 최신 Azure Resource Manager 템플릿을 생성 합니다.

  ![App Service 웹 앱 템플릿](./media/azure-web-apps/arm-template.png)

다음은 샘플을 바꾸려면 `AppMonitoredSite` 사이트 이름:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> 서식 파일 "기본" 모드에서 응용 프로그램 설정을 생성 됩니다. 이 모드는 성능 최적화 원하는 어떤 기능을 활성화 하도록 템플릿을 수정할 수 있습니다.

### <a name="enabling-through-powershell"></a>PowerShell을 통해 사용 하도록 설정

PowerShell을 통해 모니터링 응용 프로그램을 사용 하도록 설정 하기 위해 기본 응용 프로그램 설정만 변경 해야 합니다. 다음은 샘플 응용 프로그램의 리소스 그룹 "AppMonitoredRG"에서 "AppMonitoredSite" 이라는 웹 사이트에 대 한 모니터링을 사용 하도록 설정할 하 고 "012345678-abcd-ef01-2345-6789abcd" 계측 키를 전송할 수 있도록 데이터를 구성 합니다.

```powershell
$app = Get-AzureRmWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzureRmWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>모니터링 확장/에이전트 업그레이드

### <a name="upgrading-from-versions-289-and-up"></a>및 2.8.9 버전에서 업그레이드

2.8.9 버전에서 업그레이드 하는 추가 작업 없이 자동으로 발생 합니다. 대상 app service 백그라운드에서 새 모니터링 비트 전달할지 및 응용 프로그램 다시 시작에는 픽업 됩니다.

방문을 실행 중인 확장의 버전을 확인 하려면 `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Url 경로의 스크린 샷 http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>버전 1.0.0-2.6.5에서 업그레이드

사전 설치 된 사이트 확장 2.8.9 버전부터 사용 됩니다. 이전 버전의 경우에 두 가지 방법 중 하나를 통해 업데이트할 수 있습니다.

* [포털을 통해 사용 하도록 설정 하면 업그레이드](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)합니다. (설치 하는 Azure App Service에 대 한 Application Insights 확장 해야 하는 경우에 UI 표시 **사용** 단추입니다. 내부적으로 개인 사이트 확장 제거 됩니다.)

* [PowerShell을 통해 업그레이드](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. 응용 프로그램 설정을 ApplicationInsightsAgent 미리 설치 된 사이트 확장을 사용 하도록 설정 합니다. 참조 [powershell을 통해 사용 하도록 설정 하면](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)합니다.
    2. Azure App Service에 대 한 Application Insights 확장 라는 개인 사이트 확장을 수동으로 제거 합니다.

2.5.1 이전 버전에서 업그레이드가 완료 되 면 응용 프로그램 bin 폴더에서 ApplicationInsigths dll가 제거 되었는지 확인 [문제 해결 단계를 참조 하세요.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)합니다.

## <a name="troubleshooting"></a>문제 해결

다음은.NET에 대 한 확장/에이전트 기반 모니터링에 대 한 단계별 문제 해결 가이드 및.NET Core 기반 Azure App Service에서 실행 하는 응용 프로그램입니다.

> [!NOTE]
> 수동 SDK 기반 계측을 통해 Azure App Service에서 Java 및 Node.js 응용 프로그램은 에서만 지원 하며 하므로 이러한 시나리오에는 아래 단계를 적용 되지 않습니다.

1. 응용 프로그램을 통해 모니터링 되는 확인 `ApplicationInsightsAgent`합니다.
    * 확인 ' ApplicationInsightsAgent_EXTENSION_AGENT 앱 설정 "~ 2"의 값으로 설정 됩니다.
2. 응용 프로그램 모니터링 요구 사항을 충족 하는지 확인 합니다.
    * 로 이동 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![스크린샷 https://yoursitename.scm.azurewebsites/applicationinsights 결과 페이지](./media/azure-web-apps/app-insights-sdk-status.png)

    * 확인 된 `Application Insights Extension Status` 됩니다 `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 를 실행 하지 않는 경우에 따라는 [지침을 모니터링 하는 Application Insights를 사용 하도록 설정](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * 상태 원본 있고 같습니다 있는지 확인 합니다. `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 유사한 값이 없는 경우 응용 프로그램이 실행 중이지 않거나 지원 되지 않는 의미 합니다. 응용 프로그램이 실행 되 고 있는지을 보장 하려면 수동으로 런타임 정보를 사용할 수 있는 응용 프로그램 url 및 응용 프로그램 끝점을 방문 하 해 보십시오.

    * 확인 `IKeyExists` 는 `true`
        * False 인 경우 추가 ' 응용 프로그램 설정에 ikey guid 사용 하 여 APPINSIGHTS_INSTRUMENTATIONKEY 합니다.

    * 에 대 한 항목이 있는지 확인 `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, 및 `AppContainsAspNetTelemetryCorrelationAssembly`합니다.
        * 이러한 항목이 없으면 응용 프로그램에서 다음 패키지를 제거 합니다. `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, 및 `Microsoft.AspNet.TelemetryCorrelation`합니다.

아래 표에서 이러한 값 의미의 자세한 설명, 해당 기본 관련 원인 및 수정 권장:

|문제 값|설명|해결
|---- |----|---|
| `AppAlreadyInstrumented:true` | 이 값은 확장 SDK의 일부 응용 프로그램에서 이미 하는 백오프 있음을 나타냅니다. 에 대 한 참조로 인해 하기란 `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, 또는 `Microsoft.ApplicationInsights`  | 참조를 제거 합니다. 이러한 참조의 일부 특정 Visual Studio 템플릿에서 기본적으로 추가 되 고 이전 버전의 Visual Studio에 대 한 참조를 추가할 수 있습니다 `Microsoft.ApplicationInsights`합니다.
|`AppAlreadyInstrumented:true` | 이 값은 이전 배포의 앱 폴더에서 위의 dll로 발생할 수 있습니다. | 이러한 dll 제거 되도록 하려면 앱 폴더를 정리 합니다.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 이 값이 확장에 대 한 참조가 있음을 나타냅니다 `Microsoft.AspNet.TelemetryCorrelation` 응용 프로그램에 백오프 하 고 있습니다. | 참조를 제거 합니다.
|`AppContainsDiagnosticSourceAssembly**:true`|이 값이 확장에 대 한 참조가 있음을 나타냅니다 `System.Diagnostics.DiagnosticSource` 응용 프로그램에 백오프 하 고 있습니다.| 참조를 제거 합니다.
|`IKeyExists:false`|이 값 계측 키를 AppSetting에 아님을 나타냅니다 `APPINSIGHTS_INSTRUMENTATIONKEY`합니다. 가능한 원인: 값을 실수로 제거 되었거나, 자동화 스크립트 등의 값을 설정 하지 않았습니다. | App Service 응용 프로그램 설정에서 설정 있는지 확인 합니다.

Application Insights 에이전트 확장에 대 한 최신 정보를 확인 합니다 [릴리스](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)합니다.

## <a name="next-steps"></a>다음 단계

* [라이브 앱에서 프로파일러를 실행합니다](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights로 Azure Functions 모니터링
* [Azure 진단을 사용](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../../azure-monitor/platform/data-collection.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../../azure-monitor/platform/alerts-overview.md)합니다.
* [가용성 웹 테스트를 설정](../../azure-monitor/app/monitor-web-app-availability.md) 합니다.