---
title: "개발자 분석을 사용하여 모바일 웹앱에 대 한 성능 모니터링 | Microsoft Docs"
description: "모바일 앱 개발자를 위한 응용 프로그램 성능 및 사용 현황 모니터링입니다. HockeyApp 및 Application Insights를 사용하여 데스크톱, 웹 서비스 및 백 엔드 앱을 모니터링합니다."
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 4d394ff22e57d162c5adf39c492ca76f3d1f9d3a


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>HockeyApp 및 Application Insights를 사용한 모바일 분석
[HockeyApp](https://hockeyapp.net/)을 사용하여 베타 테스트 및 배포된 모바일 및 데스크톱 앱의 성능 및 사용 현황을 모니터링합니다. [Azure Application Insights](app-insights-overview.md)를 사용하여 지원되는 웹 서비스 및 백 엔드 앱을 모니터링합니다. Analytics에서 클라이언트 및 서버 앱의 데이터를 분석하고 Azure 대시보드에 나란히 차트를 표시합니다.

Microsoft 개발자 분석은 응용 프로그램 성능 모니터링을 위한 두 가지 솔루션을 제공합니다.

* **HockeyApp** - 모바일 및 데스크톱 앱
  * 선택한 사용자에게 테스트 버전 배포
  * 충돌 분석
  * 사용 현황 분석에 대한 사용자 지정 원격 분석
* **Application Insights** - 웹 사이트 및 서비스와 백 엔드 응용 프로그램
  * 성능 및 사용 현황 메트릭 및 경고
  * 예외 보고 및 진단 추적
  * 필터링 및 관련 원격 분석 링크를 통한 진단 검색

두 솔루션 모두 다음을 제공합니다.

* 진단 및 분석을 위한 강력한 **[분석 쿼리 언어](app-insights-analytics.md)**
* 사용자 고유의 저장소로 **[데이터 내보내기](app-insights-export-telemetry.md)**
* 분석 차트 및 테이블의 **통합 대시보드** 표시

## <a name="monitor-your-app-components"></a>앱 구성 요소 모니터링
이러한 페이지의 지침에 따라 코드에 SDK를 설치하고 앱 추적을 시작하세요.

### <a name="web-apps---application-insights"></a>웹앱 - Application Insights
* [ASP.NET 웹앱](app-insights-asp-net.md) 
* [Java 웹앱](app-insights-java-get-started.md)
* [Node.js 웹앱](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure 클라우드 서비스](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>모바일 앱 - HockeyApp
* [iOS 앱](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X 앱](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android 앱](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [유니버설 Windows 앱](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 및 8.1 앱](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation 앱](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Windows 데스크톱 앱에는 HockeyApp을 사용하는 것이 좋습니다. 그러나 [Windows 앱에서 Application Insights로 원격 분석을 전송](app-insights-windows-desktop.md)할 수도 있습니다. Application Insights를 사용하여 이를 실험해 볼 수 있습니다.

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>HockeyApp 원격 분석의 분석 및 내보내기
[브리지를 설정](app-insights-hockeyapp-bridge-app.md)하여 Application Insights의 분석 및 연속 내보내기 기능을 사용하는 HockeyApp 사용자 지정 및 로그 원격 분석을 조사할 수 있습니다.




<!--HONumber=Nov16_HO3-->


