---
title: Azure App Services 성능 모니터링 | Microsoft Docs
description: Azure App Services에 대한 애플리케이션 성능 모니터링입니다. 차트 로드 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d2134e059a446c18108e8dd16bcc74504b42b15a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437181"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service 성능 모니터링

[Azure App 서비스에서](https://docs.microsoft.com/azure/app-service/) 실행되는 ASP.NET 및 ASP.NET Core 기반 웹 응용 프로그램에 대한 모니터링을 그 어느 때보다 쉽게 사용할 수 있습니다. 이전에는 사이트 확장을 수동으로 설치해야 했지만 이제 최신 확장/에이전트가 기본적으로 앱 서비스 이미지에 기본으로 빌드됩니다. 이 문서에서는 응용 프로그램 인사이트 모니터링을 사용하도록 설정하는 방법뿐만 아니라 대규모 배포를 위한 프로세스 자동화에 대한 예비 지침을 제공합니다.

> [!NOTE]
> **개발 도구** > 확장을 통해 응용 프로그램 인사이트 사이트 확장을 수동으로**추가하는** 것은 더 이상 사용되지 않습니다. 이 확장 설치 방법은 각 새 버전에 대한 수동 업데이트에 따라 달라집니다. 확장의 최신 안정적인 릴리스는 이제 앱 서비스 이미지의 일부로 [사전 설치됩니다.](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) 파일은 에 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` 있으며 각 안정 릴리스와 함께 자동으로 업데이트됩니다. 아래 모니터링을 활성화하기 위해 에이전트 기반 지침을 따르는 경우 더 이상 사용되지 않습니다.

## <a name="enable-application-insights"></a>Application Insights 사용

Azure App Services 호스팅 응용 프로그램에 대한 응용 프로그램 모니터링을 사용하도록 설정하는 방법에는 두 가지가 있습니다.

* **에이전트 기반 응용 프로그램 모니터링(응용** 프로그램 인사이트에이전트).  
    * 이 방법을 사용하면 가장 쉽게 사용할 수 있으며 고급 구성이 필요하지 않습니다. 이를 "런타임" 모니터링이라고도 합니다. Azure App 서비스의 경우 최소한 이 수준의 모니터링을 사용하도록 설정하는 것이 좋습니다.

* 응용 프로그램 인사이트 SDK를 설치하여 코드를 통해 응용 프로그램을 **수동으로 계측합니다.**

    * 이 방법은 훨씬 더 사용자 정의할 수 있지만 [응용 프로그램 인사이트 SDK NuGet 패키지에 대한 종속성을 추가해야 합니다.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 이 방법은 최신 버전의 패키지에 대한 업데이트를 직접 관리해야 한다는 의미이기도 합니다.

    * 에이전트 기반 모니터링을 사용하여 기본적으로 캡처되지 않은 이벤트/종속성을 추적하기 위해 사용자 지정 API 호출을 수행해야 하는 경우 이 메서드를 사용해야 합니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭 문서에 대한 API를](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 확인하십시오. 이것은 또한 현재 리눅스 기반 워크로드에 대 한 유일한 지원 되는 옵션.

> [!NOTE]
> 에이전트 기반 모니터링 및 수동 SDK 기반 계측이 모두 감지되면 수동 계측 설정만 적용됩니다. 이는 중복 된 데이터가 전송되는 것을 방지하기위한 것입니다. 이에 대해 자세히 알아보려면 아래 [문제 해결 섹션을](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) 확인하십시오.

## <a name="enable-agent-based-monitoring"></a>에이전트 기반 모니터링 사용

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED url압축의 조합은 지원되지 않습니다. 자세한 내용은 문제 해결 [섹션의](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)설명을 참조하십시오.


1. App Service의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

     > [!NOTE]
     > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거**됩니다. 

     ![웹앱 계측](./media/azure-web-apps/create-resource-01.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. ASP.NET 앱 모니터링은 기본적으로 두 가지 수준의 컬렉션으로 설정됩니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options-new.png)

   * .NET **Basic 컬렉션** 수준은 필수적인 단일 인스턴스 APM 기능을 제공합니다.

   * .NET **추천 수집** 수준은 다음과 같습니다.
       * CPU, 메모리 및 I/O 사용량 추세를 추가합니다.
       * 요청/종속성 경계 간에 마이크로 서비스를 상호 연결합니다.
       * 사용량 추세를 수집하고, 가용성 결과와 트랜잭션의 상관 관계를 사용하도록 설정합니다.
       * 호스트 프로세스에서 처리되지 않은 예외를 수집합니다.
       * 샘플링을 사용하는 경우 부하 상태에서 APM 메트릭 정확도가 향상됩니다.

3. 이전에 applicationinsights.config 파일을 통해 제어할 수 있는 샘플링과 같은 설정을 구성하려면 이제 해당 접두사가 있는 응용 프로그램 설정을 통해 동일한 설정과 상호 작용할 수 있습니다. 

    * 예를 들어 초기 샘플링 백분율을 변경하려면 응용 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` 프로그램 설정과 `100`의 값을 만들 수 있습니다.

    * 지원되는 적응형 샘플링 원격 분석 프로세서 설정 목록은 [코드](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) 및 [관련 설명서를](https://docs.microsoft.com/azure/azure-monitor/app/sampling)참조할 수 있습니다.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

다음 버전의 .NET 코어는 ASP.NET 코어 2.0, ASP.NET 코어 2.1, ASP.NET 코어 2.2, ASP.NET 코어 3.0입니다.

.NET Core, 독립형 배포 및 Linux 기반 응용 프로그램에서 전체 프레임워크를 대상으로 하는 것은 현재 에이전트/확장 기반 모니터링으로 **지원되지 않습니다.** (코드를 통한[수동 계측은](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 이전의 모든 시나리오에서 작동합니다.)

1. App Service의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

     > [!NOTE]
     > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거**됩니다. 

     ![웹앱 계측](./media/azure-web-apps/create-resource-01.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 응용 프로그램의 플랫폼당 데이터를 수집하는 방법을 선택할 수 있습니다. .NET Core는 .NET Core 2.0, 2.1, 2.2 및 3.0에 대해 **권장 컬렉션** 또는 **사용 안 함기능을** 제공합니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

**설정에서** > 앱 서비스 웹 앱 내에서 응용 프로그램 > 인사이트**사용 을****선택합니다.** Node.js 에이전트 기반 모니터링은 현재 미리 보기 상태입니다.

# <a name="java"></a>[Java](#tab/java)

Java 앱 서비스 기반 웹 응용 프로그램은 현재 자동 에이전트/확장 기반 모니터링을 지원하지 않습니다. Java 응용 프로그램에 대한 모니터링을 사용하려면 [응용 프로그램을 수동으로 계측해야](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)합니다.

# <a name="python"></a>[Python](#tab/python)

파이썬 앱 서비스 기반 웹 응용 프로그램은 현재 자동 에이전트 / 확장 기반 모니터링을 지원하지 않습니다. Python 응용 프로그램에 대한 모니터링을 사용하려면 [응용 프로그램을 수동으로 계측해야](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)합니다.

---

## <a name="enable-client-side-monitoring"></a>클라이언트 쪽 모니터링 사용

# <a name="net"></a>[.NET](#tab/net)

클라이언트 측 모니터링은 ASP.NET 위해 옵트인됩니다. 클라이언트 측 모니터링을 사용하려면 다음을 수행하십시오.

* **설정** >** **응용 프로그램 설정****
   * 응용 프로그램 설정에서 새 **앱 설정 이름과** **값을**추가합니다.

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `true`

   * 설정을 **저장**하고 앱을 **다시 시작**합니다.

![응용 프로그램 설정 UI의 스크린 샷](./media/azure-web-apps/appinsights-javascript-enabled.png)

클라이언트 측 모니터링을 사용하지 않도록 설정하려면 응용 프로그램 설정에서 연결된 키 값 쌍을 제거하거나 값을 false로 설정합니다.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

클라이언트 측 모니터링은 앱 설정 'APPINSIGHTS_JAVASCRIPT_ENABLED'가 있는지 여부에 관계없이 **권장 컬렉션이**있는 .NET Core 앱에 **대해 기본적으로 활성화됩니다.**

어떤 이유로 클라이언트 측 모니터링을 사용하지 않으려면 다음을 수행하십시오.

* **설정** > **응용 프로그램 설정** 선택
   * 응용 프로그램 설정에서 새 **앱 설정 이름과** **값을**추가합니다.

     이름:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `false`

   * 설정을 **저장**하고 앱을 **다시 시작**합니다.

![응용 프로그램 설정 UI의 스크린 샷](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Node.js 응용 프로그램에 대한 클라이언트 측 모니터링을 사용하려면 [응용 프로그램에 클라이언트 측 JavaScript SDK를 수동으로 추가해야 합니다.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="java"></a>[Java](#tab/java)

Java 응용 프로그램에 대한 클라이언트 측 모니터링을 사용하려면 [응용 프로그램에 클라이언트 측 JavaScript SDK를 수동으로 추가해야 합니다.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="python"></a>[Python](#tab/python)

Python 응용 프로그램에 대한 클라이언트 측 모니터링을 사용하려면 [응용 프로그램에 클라이언트 측 JavaScript SDK를 수동으로 추가해야합니다.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

---

## <a name="automate-monitoring"></a>모니터링 자동화

응용 프로그램 인사이트를 사용하여 원격 분석 컬렉션을 사용하려면 응용 프로그램 설정만 설정하면 됩니다.

   ![사용 가능한 응용 프로그램 인사이트 설정이 있는 앱 서비스 응용 프로그램 설정](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>응용 프로그램 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어하는 주 확장입니다. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  기본 모드에서만 최적의 성능을 보장하기 위해 필수 기능이 활성화됩니다. | `default` 또는 `recommended`입니다. |
|InstrumentationEngine_EXTENSION_VERSION | 이진 다시 쓰기 엔진이 켜져 있는지 제어합니다. `InstrumentationEngine` 이 설정은 성능에 영향을 미치며 콜드 시작/시작 시간에 영향을 미칩니다. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL & Azure 테이블 텍스트가 종속성 호출과 함께 캡처될지 제어합니다. 성능 경고: 응용 프로그램 콜드 시작 시간이 영향을 받습니다. 이 설정에는 `InstrumentationEngine`이 설정이 필요합니다. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure 리소스 관리자를 사용 하 고 응용 프로그램 응용 프로그램 설정

앱 서비스에 대한 응용 프로그램 설정을 [Azure 리소스 관리자 템플릿으로](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)관리하고 구성할 수 있습니다. 이 메서드는 Azure 리소스 관리자 자동화를 사용 하 고 새 앱 서비스 리소스를 배포하거나 기존 리소스의 설정을 수정하는 데 사용할 수 있습니다.

앱 서비스에 대한 응용 프로그램 설정 JSON의 기본 구조는 다음과 같습니다.

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

응용 프로그램 인사이트를 위해 구성된 응용 프로그램 설정이 있는 Azure 리소스 관리자 템플릿의 예로, 이 [템플릿은](https://github.com/Andrew-MSFT/BasicImageGallery) 특히 [238 줄에서](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)시작하는 섹션에 도움이 될 수 있습니다.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>응용 프로그램 인사이트 리소스 만들기를 자동화하고 새로 만든 앱 서비스에 연결합니다.

모든 기본 응용 프로그램 인사이트 설정이 구성된 Azure 리소스 관리자 템플릿을 만들려면 응용 프로그램 인사이트가 활성화된 새 웹 앱을 만들려는 것처럼 프로세스를 시작합니다.

**자동화 옵션** 선택

   ![앱 서비스 웹 앱 생성 메뉴](./media/azure-web-apps/create-web-app.png)

이 옵션은 모든 필수 설정이 구성된 최신 Azure 리소스 관리자 템플릿을 생성합니다.

  ![앱 서비스 웹 앱 템플릿](./media/azure-web-apps/arm-template.png)

다음은 모든 인스턴스를 `AppMonitoredSite` 사이트 이름으로 바꾸는 샘플입니다.

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
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
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
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>파워쉘을 통한 사용

PowerShell을 통해 응용 프로그램 모니터링을 활성화하려면 기본 응용 프로그램 설정만 변경하면 됩니다. 다음은 리소스 그룹 "AppMonitoredRG"에서 "AppMonitoredSite"라는 웹 사이트에 대한 응용 프로그램 모니터링을 가능하게하고 "012345678-abcd-ef01-2345-6789abcd"계측 키로 전송할 데이터를 구성하는 샘플입니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>업그레이드 모니터링 확장/에이전트

### <a name="upgrading-from-versions-289-and-up"></a>버전 2.8.9 이상에서 업그레이드

버전 2.8.9에서 업그레이드는 추가 작업 없이 자동으로 수행됩니다. 새 모니터링 비트는 백그라운드에서 대상 앱 서비스에 전달되며 응용 프로그램을 다시 시작하면 선택됩니다.

방문 중인 확장 버전을 확인하려면`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![URL 경로의 스크린샷http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>버전 1.0.0 - 2.6.5에서 업그레이드

버전 2.8.9부터 사전 설치된 사이트 확장이 사용됩니다. 이전 버전인 경우 다음 두 가지 방법 중 하나를 통해 업데이트할 수 있습니다.

* [포털을 통해 활성화하여 업그레이드합니다.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) Azure 앱 서비스에 대한 응용 프로그램 인사이트 확장이 설치되어 있더라도 UI에는 **사용** 버튼만 표시됩니다. 뒤에서 이전 개인 사이트 확장이 제거됩니다.)

* [파워 쉘을 통해 업그레이드](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. 사전 설치된 사이트 확장 ApplicationInsightsAgent를 사용하도록 응용 프로그램 설정을 설정합니다. [전원 셸을 통해 활성화 를](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)참조하십시오.
    2. Azure 앱 서비스에 대한 응용 프로그램 인사이트 확장이라는 개인 사이트 확장을 수동으로 제거합니다.

2.5.1 이전 버전에서 업그레이드가 수행된 경우 응용 프로그램 Bin 폴더에서 ApplicationInsigths dlls가 제거되었는지 확인하여 [문제 해결 단계를 확인합니다.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)

## <a name="troubleshooting"></a>문제 해결

다음은 Azure App 서비스에서 실행되는 .NET 및 .NET Core 기반 응용 프로그램에 대한 확장/에이전트 기반 모니터링에 대한 단계별 문제 해결 가이드입니다.

> [!NOTE]
> Java 응용 프로그램은 수동 SDK 기반 계측을 통해서만 Azure App 서비스에서 지원되므로 아래 단계는 이러한 시나리오에 적용되지 않습니다.

1. 을 통해 응용 프로그램이 `ApplicationInsightsAgent`모니터링되는지 확인합니다.
    * 앱 `ApplicationInsightsAgent_EXTENSION_VERSION` 설정이 "~2"의 값으로 설정되어 있는지 확인합니다.
2. 응용 프로그램이 모니터링할 요구 사항을 충족하는지 확인합니다.
    * `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`으로 이동

    ![https://yoursitename.scm.azurewebsites/applicationinsights 결과 페이지의 스크린샷](./media/azure-web-apps/app-insights-sdk-status.png)

    * 있는지 `Application Insights Extension Status` 확인합니다.`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 실행 중이 아닌 경우 [응용 프로그램 인사이트 모니터링 지침 에](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) 따라

    * 상태 원본이 존재하고 다음과 같은지 확인합니다.`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 비슷한 값이 없는 경우 응용 프로그램이 현재 실행 중이거나 지원되지 않음을 의미합니다. 응용 프로그램이 실행되고 있는지 확인하려면 런타임 정보를 사용할 수 있도록 응용 프로그램 URL/응용 프로그램 끝점을 수동으로 방문해 보십시오.

    * 있는지 `IKeyExists` 확인합니다.`true`
        * `false`이 경우 응용 프로그램 설정에 ikey guid를 추가합니다. `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING`

    * 에 `AppAlreadyInstrumented` `AppContainsDiagnosticSourceAssembly`대한 항목이 없는지 `AppContainsAspNetTelemetryCorrelationAssembly`확인합니다.
        * 이러한 항목이 있는 경우 응용 프로그램에서 다음 패키지를 `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource`제거합니다. `Microsoft.AspNet.TelemetryCorrelation`

아래 표는 이러한 값의 의미, 근본 원인 및 권장 수정 에 대한 자세한 설명을 제공합니다.

|문제 값|설명|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | 이 값은 확장이 SDK의 일부 측면이 응용 프로그램에 이미 있고 백오프되는 것을 감지했음을 나타냅니다. 에 대한 참조 `System.Diagnostics.DiagnosticSource` `Microsoft.AspNet.TelemetryCorrelation`때문일 수 있습니다.`Microsoft.ApplicationInsights`  | 참조를 제거합니다. 이러한 참조 중 일부는 기본적으로 특정 Visual Studio 템플릿에서 추가되며 이전 버전의 `Microsoft.ApplicationInsights`Visual Studio에서는 에 대한 참조를 추가할 수 있습니다.
|`AppAlreadyInstrumented:true` | 응용 프로그램이 .NET Core 2.1 또는 2.2를 대상으로 하고 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 메타 패키지를 참조하는 경우 응용 프로그램 인사이트를 가져오고 확장이 백오프됩니다. | .NET 코어 2.1,2.2의 고객은 대신 Microsoft.AspNetCore.App 메타 패키지를 사용하는 [것이 좋습니다.](https://github.com/aspnet/Announcements/issues/287)|
|`AppAlreadyInstrumented:true` | 이 값은 이전 배포에서 앱 폴더에 위의 dlls가 있으면 발생할 수도 있습니다. | 이러한 dlls가 제거되었는지 확인하기 위해 앱 폴더를 청소합니다. 로컬 앱의 bin 디렉터리와 앱 서비스의 wwwroot 디렉터리를 모두 확인합니다. (앱 서비스 웹 앱의 wwwroot 디렉토리를 확인하려면: 고급 도구 (쿠두) > 디버그 콘솔 > CMD > 홈\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 이 값은 확장이 응용 `Microsoft.AspNet.TelemetryCorrelation` 프로그램에서 참조를 검색했음을 나타내며 백오프됩니다. | 참조를 제거합니다.
|`AppContainsDiagnosticSourceAssembly**:true`|이 값은 확장이 응용 `System.Diagnostics.DiagnosticSource` 프로그램에서 참조를 검색했음을 나타내며 백오프됩니다.| 참조를 제거합니다.
|`IKeyExists:false`|이 값은 계측 키가 AppSetting에 없는 `APPINSIGHTS_INSTRUMENTATIONKEY`것을 나타냅니다. 가능한 원인: 값이 실수로 제거되었어도, 자동화 스크립트에서 값을 설정하는 것을 잊어버렸을 수 있습니다. | 앱 서비스 응용 프로그램 설정에 설정이 있는지 확인합니다.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED 및 url압축이 지원되지 않음

콘텐츠가 인코딩되는 경우 APPINSIGHTS_JAVASCRIPT_ENABLED=true를 사용하는 경우 다음과 같은 오류가 발생할 수 있습니다. 

- 500 URL 다시 쓰기 오류
- 500.53 HTTP 응답의 내용을 인코딩할 때 메시지 아웃바운드 재작성 규칙이 있는 500.53 URL 재작성 모듈 오류는 적용할 수 없습니다('gzip'). 

이는 APPINSIGHTS_JAVASCRIPT_ENABLED 응용 프로그램 설정이 true로 설정되고 콘텐츠 인코딩이 동시에 존재하기 때문입니다. 이 시나리오는 아직 지원되지 않습니다. 해결 방법은 응용 프로그램 설정에서 APPINSIGHTS_JAVASCRIPT_ENABLED 제거하는 것입니다. 안타깝게도 클라이언트/브라우저 측 JavaScript 계측이 여전히 필요한 경우 웹 페이지에 수동 SDK 참조가 필요합니다. 자바 스크립트 SDK와 수동 계측에 대한 [지침을](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) 따르십시오.

응용 프로그램 인사이트 에이전트/확장에 대한 최신 정보는 [릴리스 정보를](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)확인하십시오.

### <a name="php-and-wordpress-are-not-supported"></a>PHP와 워드 프레스는 지원되지 않습니다

PHP 및 워드 프레스 사이트는 지원되지 않습니다. 현재 이러한 워크로드의 서버 측 모니터링을 위해 공식적으로 지원되는 SDK/에이전트는 없습니다. 그러나 웹 페이지에 클라이언트 측 자바 스크립트를 추가하여 PHP 또는 워드 프레스 사이트에서 클라이언트 측 트랜잭션을 수동으로 계측하는 [것은 JavaScript SDK를](https://docs.microsoft.com/azure/azure-monitor/app/javascript)사용하여 수행 할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
* [라이브 앱에서 프로파일러를 실행합니다](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights로 Azure Functions 모니터링
* [Azure 진단을 사용](../platform/diagnostics-extension-to-application-insights.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../platform/data-platform.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../platform/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.
