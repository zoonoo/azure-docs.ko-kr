---
title: Azure App Service에서 .NET 앱용 스냅샷 디버거 사용하도록 설정 | Microsoft Docs
description: Azure App Service에서 .NET 앱용 스냅샷 디버거 사용하도록 설정
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 26538f48213d025c6fe71fb55abb17a025a23b45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025682"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service에서 .NET 앱용 스냅샷 디버거 사용하도록 설정

현재 스냅샷 디버거는 Windows 서비스 계획의 Azure App Service에서 실행되는 ASP.NET과 ASP.NET Core 앱을 지원합니다.

스냅샷 디버거를 사용할 때 기본 서비스 계층 이상에서 애플리케이션을 실행하는 것이 좋습니다.

대부분의 애플리케이션에서 무료 및 공유 서비스 계층에는 스냅샷을 저장할 충분한 메모리나 디스크 공간이 없습니다.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> 스냅샷 디버거 사용하도록 설정
앱에 대해 스냅샷 디버거를 사용하도록 설정하려면 아래의 지침을 따르세요.

다른 유형의 Azure 서비스를 실행하는 경우 지원되는 다른 플랫폼에서 스냅샷 디버거를 사용하도록 설정하는 지침은 다음과 같습니다.
* [Azure 함수](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 또는 물리적 컴퓨터](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> .NET Core의 미리 보기 버전을 사용하도록 설정하고 있거나 애플리케이션이 직접 또는 종속 어셈블리를 통해 간접적으로 Application Insights SDK를 참조하는 경우 [다른 환경의 스냅샷 디버거를 사용하도록 설정](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)하여 애플리케이션과 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 포함하기 위한 지침을 따르고 아래 나머지 지침을 따르세요. 
>
> Application Insights 스냅샷 디버거의 코드 없는 설치는 .NET Core 지원 정책을 따릅니다.
> 지원되는 런타임에 대한 자세한 내용은 [.NET Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 참조하세요.

스냅샷 디버거는 App Services 런타임의 일부로 미리 설치되어 있지만 App Service 앱에 대한 스냅샷을 가져오도록 해당 기능을 켜야 합니다.

앱을 배포한 후에는 아래 단계를 따라 스냅샷 디버거를 사용하도록 설정합니다.

1. App Service에 대한 Azure 제어판으로 이동합니다.
2. **설정 > Application Insights** 페이지로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 페이지의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 스냅샷 디버거의 스위치 두 개가 모두 **켜져 있는지** 확인합니다.

   ![App Insights 사이트 확장 추가][Enablement UI]

4. 이제 스냅샷 디버거가 App Services 앱 설정을 사용하여 활성화됩니다.

    ![스냅샷 디버거에 대한 앱 설정][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>다른 클라우드의 스냅샷 디버거를 사용하도록 설정

현재 엔드포인트 수정이 필요한 유일한 지역은 Application Insights 연결 문자열을 통한 [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights)와 [Azure 중국](/azure/china/resources-developer-guide)입니다.

|연결 문자열 속성    | 미국 정부 클라우드: | 중국 클라우드 |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

다른 연결 재정의에 대한 자세한 내용은 [Application Insights 설명서](./sdk-connection-string.md?tabs=net#connection-string-with-explicit-endpoint-overrides)를 참조하세요.

## <a name="disable-snapshot-debugger"></a>스냅샷 디버거 사용 안 함

**스냅샷 디버거 사용하도록 설정** 과 동일한 단계를 따르되 스냅샷 디버거의 스위치를 둘 다 **끕니다**.

애플리케이션 예외를 쉽게 진단하려면 모든 앱에서 스냅샷 디버거를 사용하도록 설정하는 것이 좋습니다.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

Azure App Service에서 스냅샷 디버거와 Profiler를 사용하도록 Azure Resource Manager 템플릿 내 앱 설정을 설정할 수 있습니다. 아래 템플릿 코드 조각을 참조하세요.

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

- 애플리케이션에 예외를 트리거할 수 있는 트래픽을 생성합니다. 그런 다음, 스냅샷을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [스냅샷](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)을 참조하세요.
- 스냅샷 디버거 문제 해결에 도움이 필요한 경우 [스냅샷 디버거 문제 해결](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png