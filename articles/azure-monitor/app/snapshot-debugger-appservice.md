---
title: Azure App Service에서 .NET 앱에 대 한 스냅숏 디버거 사용 Microsoft Docs
description: Azure App Service에서 .NET 앱에 대 한 스냅숏 디버거 사용
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671429"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service에서 .NET 앱에 대 한 스냅숏 디버거 사용

스냅숏 디버거 현재 Windows 서비스 계획에서 Azure App Service 실행 되는 ASP.NET 및 ASP.NET Core 앱에 대해 작동 합니다.

## <a id="installation"></a>스냅숏 디버거 사용
앱에 대 한 스냅숏 디버거를 사용 하도록 설정 하려면 아래 지침을 따르세요. 다른 유형의 Azure 서비스를 실행 하는 경우 지원 되는 다른 플랫폼에서 스냅숏 디버거를 사용 하도록 설정 하는 지침은 다음과 같습니다.
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 또는 물리적 컴퓨터](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

.NET Core의 미리 보기 버전을 사용 하는 경우 [다른 환경에 대 한 스냅숏 디버거 사용](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 에 대 한 지침에 따라 먼저 응용 프로그램과 함께 [microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 포함 하 고 아래 지침의 나머지를 완료 하세요. 

Application Insights 스냅숏 디버거 App Services 런타임의 일부로 미리 설치 되지만 App Service 앱에 대 한 스냅숏을 가져오려면 설정 해야 합니다. 앱을 배포한 후에는 소스 코드에 Application Insights SDK를 포함 했더라도 스냅숏 디버거를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **App Services** 창으로 이동합니다.
2. **설정 > Application Insights** 창으로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 스냅숏 디버거에 대 한 두 스위치가 모두 **켜져**있는지 확인 합니다.

   ![App Insights 사이트 확장 추가][Enablement UI]

4. 이제 App Services 앱 설정을 사용 하 여 스냅숏 디버거를 사용할 수 있습니다.

    ![스냅숏 디버거에 대 한 앱 설정][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>스냅숏 디버거 사용 안 함

**스냅숏 디버거를 사용 하도록 설정**하는 것과 동일한 단계를 수행 하 되 스냅숏 디버거에 대 한 스위치를 모두 **Off**로 전환 합니다.
응용 프로그램 예외를 쉽게 진단 하기 위해 모든 앱에서 스냅숏 디버거 사용 하도록 설정 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 예외를 트리거할 수 있는 응용 프로그램에 대 한 트래픽을 생성 합니다. 그런 다음 스냅숏이 Application Insights 인스턴스로 전송 될 때까지 10 ~ 15 분 정도 기다립니다.
- Azure Portal의 [스냅숏](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) 을 참조 하십시오.
- 스냅숏 디버거 문제를 해결 하는 데 도움이 필요 하면 [스냅숏 디버거 문제 해결](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)을 참조 하세요.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

