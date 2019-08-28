---
title: Azure App Service에서.NET 앱에 대 한 스냅숏 디버거를 사용 하도록 설정 | Microsoft Docs
description: Azure App Service에서.NET 앱에 대 한 스냅숏 디버거를 사용 하도록 설정
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 3e8ce3c2eff7b1f7184bb37f141e62563d4fe714
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612679"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service에서.NET 앱에 대 한 스냅숏 디버거를 사용 하도록 설정

스냅숏 디버거는 현재 Windows 서비스 계획에 Azure App Service에서 실행 중인 ASP.NET 및 ASP.NET Core 앱에 대 한 작동 합니다.

## <a id="installation"></a> 스냅숏 디버거를 사용 하도록 설정
앱에 대 한 스냅숏 디버거를 사용 하려면 아래 지침을 따릅니다. 다른 유형의 Azure 서비스를 실행 하는 경우 지원 되는 다른 플랫폼에서 스냅숏 디버거를 사용 하도록 설정 하는 것에 대 한 지침은 다음과 같습니다.
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 서비스](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines 및 Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [온-프레미스 가상 또는 물리적 컴퓨터](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

미리 보기 버전의.NET Core를 사용 하는 경우에 대 한 지침을 따르세요 [다른 환경에 대해 스냅숏 디버거 사용](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 포함 하려면 먼저는 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 응용 프로그램을 패키지 하 고 아래 지침의 나머지 부분을 완료 합니다. 

Application Insights 스냅숏 디버거 App Services 런타임의 일부로 미리 설치 되어 있지만 App Service 앱에 대 한 get 스냅숏을에 설정 해야 합니다. 배포한 후 앱에 Application Insights SDK 소스 코드에 포함 된 경우에, 스냅숏 디버거를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **App Services** 창으로 이동합니다.
2. **설정 > Application Insights** 창으로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 스냅숏 디버거에 대 한 두 스위치가 있는지 확인 **에서**합니다.

   ![App Insights 사이트 확장 추가][Enablement UI]

4. 스냅숏 디버거는 앱 서비스 앱 설정을 사용 하 여 활성화 되었습니다.

    ![스냅숏 디버거에 대 한 앱 설정][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>스냅숏 디버거를 사용 하지 않도록 설정

와 동일한 단계를 수행 **스냅숏 디버거 사용**를 스냅숏 디버거에 대 한 두 스위치를 전환 하지만 **해제**합니다.
스냅숏 디버거 진단 응용 프로그램 예외를 쉽게 하려면 모든 앱에서 사용 하도록 설정 해야 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- 예외를 트리거할 수 있는 응용 프로그램에 대 한 트래픽을 생성 합니다. 그런 다음 Application Insights 인스턴스를 전송할 수 있도록 스냅숏 10 ~ 15 분을 기다립니다.
- 참조 [스냅숏을](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) Azure portal에서 합니다.
- 스냅숏 디버거 문제 해결 도움말을 참조 하세요 [Snapshot Debugger 문제 해결](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)합니다.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

