---
title: Azure 앱 서비스에서 .NET 앱에 대한 스냅샷 디버거 활성화 | 마이크로 소프트 문서
description: Azure 앱 서비스에서 .NET 앱에 대한 스냅숏 디버거 사용
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298266"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure 앱 서비스에서 .NET 앱에 대한 스냅숏 디버거 사용

스냅숏 디버거는 현재 Windows 서비스 계획에서 Azure 앱 서비스에서 실행되는 ASP.NET 및 ASP.NET 핵심 앱에서 작동합니다.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>스냅샷 디버거 사용
앱에 대한 스냅숏 디버거를 사용하려면 아래 지침을 따르십시오. 다른 유형의 Azure 서비스를 실행하는 경우 지원되는 다른 플랫폼에서 스냅숏 디버거를 사용하도록 설정하는 방법에 대한 지침은 다음과 같습니다.
* [Azure 클라우드 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 서비스 패브릭 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 또는 물리적 컴퓨터](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

.NET Core의 미리 보기 버전을 사용하는 경우 [먼저 다른 환경에 대한 스냅숏 디버거 사용](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 방법에 대한 지침을 따르십시오. [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 

응용 프로그램 인사이트 스냅샷 디버거는 앱 서비스 런타임의 일부로 사전 설치되어 있지만 앱 서비스 앱에 대한 스냅숏을 얻으려면 이 디버거를 켜야 합니다. 앱을 배포한 후에는 소스 코드에 Application Insights SDK를 포함하더라도 아래 단계를 수행하여 스냅샷 디버거를 활성화합니다.

1. Azure Portal에서 **App Services** 창으로 이동합니다.
2. **설정 > Application Insights** 창으로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 스냅숏 디버거에 대한 두 스위치가 **모두 켜졌는지**확인합니다.

   ![App Insights 사이트 확장 추가][Enablement UI]

4. 이제 앱 서비스 앱 설정을 사용하여 스냅숏 디버거를 사용할 수 있습니다.

    ![스냅샷 디버거에 대한 앱 설정][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>스냅샷 디버거 사용 안 함

**스냅숏 디버거 사용과**동일한 단계를 수행하지만 스냅숏 디버거에 대한 두 스위치를 **모두 꺼짐으로**전환합니다.
응용 프로그램 예외의 진단을 쉽게 하기 위해 모든 앱에서 스냅숏 디버거를 사용하도록 설정하는 것이 좋습니다.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

Azure 앱 서비스의 경우 Azure 리소스 관리자 템플릿에서 앱 설정을 설정하여 스냅숏 디버거 및 프로파일러를 활성화할 수 있습니다. 앱 설정을 포함하는 구성 리소스를 웹 사이트의 자식 리소스로 추가합니다.

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

- 예외를 트리거할 수 있는 응용 프로그램에 대한 트래픽을 생성합니다. 그런 다음 스냅숏이 Application Insights 인스턴스로 전송될 때까지 10~15분 동안 기다립니다.
- Azure 포털의 [스냅숏을](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) 참조하십시오.
- 스냅샷 디버거 문제 해결에 대한 도움말은 [스냅숏 디버거 문제 해결을](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)참조하십시오.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

