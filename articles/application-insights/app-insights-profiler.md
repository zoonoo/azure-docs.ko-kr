---
title: Application Insights를 사용하여 라이브 Azure Web Apps 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하여 Azure에서 라이브 웹앱 프로파일링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 9aae08aa5906f341a890ac15e30d2863109d83a2
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140011"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure Web Apps 프로파일링

Profiler는 현재 Web Apps에서 실행 중인 ASP.NET 및 ASP.NET Core 웹앱에서 작동합니다. Profiler를 사용하려면 기본 서비스 계층 이상이 필요합니다.

## <a id="installation"></a> Web Apps에 Profiler 사용
웹앱에 대해 Profiler를 사용하려면 아래 지침을 따르세요. 다른 유형의 Azure 서비스를 실행하는 경우 지원되는 다른 플랫폼에서 Profiler를 사용하도록 설정하는 지침은 다음과 같습니다.
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 응용 프로그램](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


Application Insights Profiler는 Application Insights 사이트 확장과 함께 설치됩니다. 사이트 확장을 설치하고 Azure Web Apps에 대한 프로필을 가져오도록 구성해야 합니다. 웹앱을 배포했으면, 소스 코드에 Application Insights SDK를 포함한 경우에도 아래 단계에 따라 프로파일러를 사용하도록 설정합니다.

1. Azure Portal에서 **App Services** 창으로 이동합니다.
1. **설정 > 모니터링** 창으로 이동합니다.

   ![App Services 포털에서 App Insights를 사용하도록 설정](./media/app-insights-profiler/AppInsights-AppServices.png)

1. 창의 지침에 따라 새 리소스를 만들거나 기존 App Insights 리소스를 선택하여 웹앱을 모니터링합니다. 모든 기본 옵션을 수락합니다. **코드 수준 진단**은 기본적으로 켜져 있고 Profiler를 사용하도록 설정합니다.

   ![App Insights 사이트 확장 추가][Enablement UI]

1. Profiler는 이제 App Insights 사이트 확장을 사용하여 설치되고 App Services 앱 설정을 사용하여 활성화됩니다.

    ![Profiler에 대한 앱 설정][profiler-app-setting]

## <a name="disable-profiler"></a>Profiler 사용 안 함

개별 웹앱 인스턴스에 대해 Profiler를 중지하거나 다시 시작하려면 **웹 작업**에서 Web Apps 리소스로 이동합니다. Profiler를 삭제하려면 **확장**으로 이동합니다.

![웹 작업에 대한 Profiler 사용 안 함][disable-profiler-webjob]

성능 문제를 가능한 한 빨리 검색하려면 모든 웹앱에서 Profiler를 사용하도록 설정하는 것이 좋습니다.

WebDeploy를 사용하여 웹 응용 프로그램에 변경 내용을 배포하는 경우 배포 중에 App_Data 폴더가 삭제되지 않도록 제외해야 합니다. 그렇지 않으면 다음에 Azure에 웹 응용 프로그램을 배포할 때 Profiler 확장의 파일이 삭제됩니다.



## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 Application Insights로 작업](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

