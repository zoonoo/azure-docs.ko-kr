---
title: Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하여 Azure App Service에 라이브 앱을 프로파일링합니다.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: f3ec10a970406cbb1bb6a1a52ffa8508e37fc516
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414170"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure App Service 앱 프로파일링

Profiler는 현재 Azure App Service에서 실행 중인 ASP.NET 및 ASP.NET Core 앱에서 작동합니다. Profiler를 사용하려면 기본 서비스 계층 이상이 필요합니다. Linux에서 Profiler를 사용하도록 설정하려면 반드시 [이 방법](profiler-aspnetcore-linux.md)을 사용해야 합니다.

## <a id="installation"></a> 앱에 대해 Profiler 사용
앱에 대해 Profiler를 사용하도록 설정하려면 아래 지침을 따르세요. 다른 유형의 Azure 서비스를 실행하는 경우 지원되는 다른 플랫폼에서 Profiler를 사용하도록 설정하는 지침은 다음과 같습니다.
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 애플리케이션](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler는 App Services 런타임의 일부로 미리 설치되어 있지만 App Service 앱에 대한 프로필을 가져오도록 해당 기능을 켜야 합니다. 앱을 배포했으면, 소스 코드에 App Insights SDK를 포함한 경우에도 아래 단계에 따라 프로파일러를 사용하도록 설정합니다.

1. Azure Portal에서 **App Services** 창으로 이동합니다.
2. **설정 > Application Insights** 창으로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/profiler/AppInsights-AppServices.png)

3. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 앱을 모니터링합니다. 또한 Profiler가 **켜기** 상태인지 확인합니다.

   ![App Insights 사이트 확장 추가][Enablement UI]

4. 이제 Profiler가 App Services 앱 설정을 사용하여 활성화됩니다.

    ![Profiler에 대한 앱 설정][profiler-app-setting]

## <a name="disable-profiler"></a>Profiler 사용 안 함

개별 앱의 인스턴스에 대해 Profiler를 중지하거나 다시 시작하려면 **Web Jobs**에서 앱 리소스로 이동합니다. Profiler를 삭제하려면 **확장**으로 이동합니다.

![웹 작업에 대한 Profiler 사용 안 함][disable-profiler-webjob]

성능 문제를 가능한 한 빨리 검색하려면 모든 앱에서 Profiler를 사용하도록 설정하는 것이 좋습니다.

WebDeploy를 사용하여 웹 애플리케이션에 변경 내용을 배포하는 경우 배포 중에 App_Data 폴더가 삭제되지 않도록 제외해야 합니다. 그렇지 않으면 다음에 Azure에 웹 애플리케이션을 배포할 때 Profiler 확장의 파일이 삭제됩니다.



## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 Application Insights로 작업](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
