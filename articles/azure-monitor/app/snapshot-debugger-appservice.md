---
title: Azure App Service에서 .NET 앱에 대 한 스냅숏 디버거 사용 Microsoft Docs
description: Azure App Service에서 .NET 앱에 대 한 스냅숏 디버거 사용
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 26538f48213d025c6fe71fb55abb17a025a23b45
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025682"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service에서 .NET 앱에 대 한 스냅숏 디버거 사용

스냅숏 디버거는 현재 Windows 서비스 계획에서 Azure App Service 실행 되는 ASP.NET 및 ASP.NET Core 앱을 지원 합니다.

스냅숏 디버거를 사용 하는 경우 기본 서비스 계층에서 응용 프로그램을 실행 하는 것이 좋습니다.

대부분의 응용 프로그램의 경우 무료 및 공유 서비스 계층에는 스냅숏을 저장할 수 있는 충분 한 메모리 나 디스크 공간이 없습니다.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> 스냅숏 디버거 사용
앱에 대 한 스냅숏 디버거를 사용 하도록 설정 하려면 아래 지침을 따르세요.

다른 유형의 Azure 서비스를 실행 하는 경우 지원 되는 다른 플랫폼에서 스냅숏 디버거를 사용 하도록 설정 하는 지침은 다음과 같습니다.
* [Azure 함수](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 또는 물리적 컴퓨터](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> .NET Core의 미리 보기 버전을 사용 하거나 응용 프로그램이 종속 어셈블리를 사용 하 여 직접 또는 간접적으로 Application Insights SDK를 참조 하는 경우 [다른 환경에 대 한 스냅숏 디버거 사용](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 에 대 한 지침에 따라 응용 프로그램과 함께 [microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 포함 하 고 아래 지침의 나머지를 완료 합니다. 
>
> Application Insights 스냅숏 디버거의 코드 없는 설치는 .NET Core 지원 정책을 따릅니다.
> 지원 되는 런타임에 대 한 자세한 내용은 [.Net Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조 하세요.

스냅숏 디버거 App Services 런타임의 일부로 미리 설치 되지만 App Service 앱에 대 한 스냅숏을 가져오려면 설정 해야 합니다.

앱을 배포한 후에는 다음 단계에 따라 스냅숏 디버거를 사용 하도록 설정 합니다.

1. App Service에 대 한 Azure 제어판으로 이동 합니다.
2. **설정 > Application Insights** 페이지로 이동 합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 페이지의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택 하 여 앱을 모니터링 합니다. 또한 스냅숏 디버거에 대 한 두 스위치가 모두 **켜져** 있는지 확인 합니다.

   ![App Insights 사이트 확장 추가][Enablement UI]

4. 이제 App Services 앱 설정을 사용 하 여 스냅숏 디버거를 사용할 수 있습니다.

    ![스냅숏 디버거에 대 한 앱 설정][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>다른 클라우드에 대 한 스냅숏 디버거 사용

현재는 끝점을 수정 해야 하는 유일한 지역은 [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) 하 고 Application Insights 연결 문자열을 통해 [Azure 중국](/azure/china/resources-developer-guide) 에 있습니다.

|연결 문자열 속성    | 미국 정부 클라우드 | 중국 클라우드 |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

다른 연결 재정의에 대 한 자세한 내용은 [Application Insights 설명서](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides)를 참조 하세요.

## <a name="disable-snapshot-debugger"></a>스냅숏 디버거 사용 안 함

**스냅숏 디버거를 사용 하도록 설정** 하는 것과 동일한 단계를 수행 하 되 스냅숏 디버거에 대 한 스위치를 모두 **Off** 로 전환 합니다.

응용 프로그램 예외를 쉽게 진단 하기 위해 모든 앱에서 스냅숏 디버거 사용 하도록 설정 하는 것이 좋습니다.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

Azure App Service 스냅숏 디버거 및 프로파일러를 사용 하도록 설정 하려면 Azure Resource Manager 템플릿 내에서 앱 설정을 지정할 수 있습니다. 다음 템플릿 코드 조각을 참조 하세요.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>다음 단계

- 예외를 트리거할 수 있는 응용 프로그램에 대 한 트래픽을 생성 합니다. 그런 다음 스냅숏이 Application Insights 인스턴스로 전송 될 때까지 10 ~ 15 분 정도 기다립니다.
- Azure Portal의 [스냅숏](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) 을 참조 하십시오.
- 스냅숏 디버거 문제를 해결 하는 데 도움이 필요 하면 [스냅숏 디버거 문제 해결](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)을 참조 하세요.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png