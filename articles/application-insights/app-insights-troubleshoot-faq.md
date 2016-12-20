---
title: "Application Insights에 대한 문제 해결 및 질문"
description: "Azure Application Insights에서 무엇인가 명확하지 않거나 작동하지 않나요? 여기를 참조하세요."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 0d860856b4c871c2c8e3622597ba3753bf62cf4f


---
# <a name="questions---application-insights-for-aspnet"></a>질문 - ASP.NET용 Application Insights
## <a name="configuration-problems"></a>구성 문제
*다음을 설정하는 데 문제가 있습니다.*

* [.NET 앱](app-insights-asp-net-troubleshoot-no-data.md)
* [이미 실행 중인 앱 모니터링](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Azure 진단](app-insights-azure-diagnostics.md)
* [Java 웹앱](app-insights-java-troubleshoot.md)
* [기타 플랫폼](app-insights-platforms.md)

*내 서버에서 데이터를 가져오지 않습니다.*

* [방화벽 예외 설정](app-insights-ip-addresses.md)
* [ASP.NET 서버 설정](app-insights-monitor-performance-live-website-now.md)
* [Java 서버 설정](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Application Insights와 같이 사용할 수 있나요...?
[플랫폼 참조][플랫폼]

## <a name="is-it-free"></a>무료입니까?
* 예, 무료 [가격 책정 계층](app-insights-pricing.md)을 선택하는 경우. 대부분의 기능 및 많은 데이터 할당량을 받습니다.
* Microsoft Azure에 등록하려면 사용자의 신용 카드 데이터를 제공해야 하지만 다른 유료 Azure 서비스를 사용하거나 명시적으로 유료 계층으로 업그레이드하지 않는 한 요금이 부과되지 않습니다.
* 사용자의 앱을 무료 계층에 대해 월간 할당량보다 더 많이 보내면 기록이 중지됩니다. 이 경우 지불 시작을 선택하거나 월말에 할당량이 다시 설정될 때까지 기다릴 수 있습니다.
* 기본 사용량 및 세션 데이터에는 할당량이 적용되지 않습니다.
* 무료로 유료 기능을 받을 수 있는 무료 30일 평가판도 있습니다.
* 각 응용 프로그램 리소스에는 별도 할당량이 있으며 해당 가격 책정 계층을 다른 리소스와 독립적으로 설정합니다.

#### <a name="what-do-i-get-if-i-pay"></a>지불을 할 경우 받는 혜택은 무엇입니까?
* 더 큰 [월간 데이터 할당량](https://azure.microsoft.com/pricing/details/application-insights/).
* 월간 할당량을 초과해도 데이터를 계속 수집하고 '초과분'에 대해 요금을 지불하는 옵션입니다. 사용자의 데이터가 할당량을 초과하면 Mb당 요금이 청구됩니다.
* [연속 내보내기](app-insights-export-telemetry.md).

## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights에서 내 프로젝트를 어떻게 수정하나요?
세부 정보는 프로젝트의 유형에 따라 달라집니다. 웹 응용 프로그램의 경우:

* 프로젝트에 다음 파일이 추가됩니다.
  
  * ApplicationInsights.config
  * ai.js
* 다음 NuGet 패키지가 설치됩니다.
  
  * *Application Insights API* - 핵심 API
  * *웹 응용 프로그램용 Application Insights API* - 서버에서 원격 분석을 보내는 데 사용
  * *JavaScript 응용 프로그램용 Application Insights API* - 클라이언트에서 원격 분석을 보내는 데 사용
    
    패키지에는 다음 어셈블리가 포함됩니다.
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 항목 삽입 위치:
  
  * Web.config
  * packages.config
* (새 프로젝트에만 해당 - [기존 프로젝트에 Application Insights를 추가][시작]한 경우 이를 수동으로 수행해야 합니다.) 클라이언트 및 서버 코드에 코드 조각을 삽입하여 Application Insights 리소스 ID로 해당 코드를 초기화합니다. 예를 들어 MVC 앱에서 코드를 마스터 페이지 Views/Shared/_Layout.cshtml에 삽입합니다.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>이전 SDK 버전에서 업그레이드하려면 어떻게 해야 합니까?
사용자의 응용 프로그램 유형에 적합한 SDK는 [릴리스 정보](app-insights-release-notes.md) 를 참조하세요.

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?
솔루션 탐색기에서 `ApplicationInsights.config` 를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에서 기존 또는 새 리소스로 데이터를 보낼 수 있습니다. 업데이트 마법사는 서버 SDK에서 데이터를 전송하는 위치를 결정하는 ApplicationInsights.config의 계측 키를 변경합니다. "모두 업데이트"를 선택 취소하지 않은 한, 웹 페이지에 표시되는 키도 변경됩니다.

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?
[데이터 보존 및 개인 정보][데이터]를 살펴보세요.

## <a name="logging"></a>로깅
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>진단 검색에서 POST 데이터를 어떻게 확인하나요?
POST 데이터를 자동으로 기록 하지 않지만 TrackTrace 호출을 사용할 수 있습니다. 메시지 매개 변수에 데이터를 배치합니다. 이것은 필터링할 수 없지만 문자열 속성에 대한 제한보다 긴 제한이 있습니다.

## <a name="security"></a>보안
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>내 데이터는 포털에서 안전하나요? 얼마나 오래 유지되나요?
[데이터 보존 및 개인 정보][데이터]를 참조하세요.

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Application Insights의 모든 기능을 사용하도록 어떻게 설정하나요?
| 표시 내용 | 시작 방법 | 원하는 이유 |
| --- | --- | --- |
| 가용성 차트 |[웹 테스트](app-insights-monitor-web-app-availability.md) |웹 앱이 작동 중인지 확인 |
| 서버 앱 성능: 응답시간. ... |[프로젝트에 Application Insights 추가](app-insights-asp-net.md) 또는 [서버에 AI 상태 모니터 설치](app-insights-monitor-performance-live-website-now.md)(또는 [종속성 추적](app-insights-api-custom-events-metrics.md#track-dependency)을 위한 자체 코드 작성) |성능 문제 검색 |
| 종속성 원격 분석 |[서버에 AI 상태 모니터 설치](app-insights-monitor-performance-live-website-now.md) |데이터베이스 또는 다른 외부 구성 요소의 문제 진단 |
| 예외에서 스택 추적 가져오기 |[TrackException 호출을 코드에 삽입](app-insights-search-diagnostic-logs.md#exceptions)(하지만 일부는 자동으로 보고됨) |예외 감지 및 진단 |
| 로그 추적 검색 |[로깅 어댑터 추가](app-insights-search-diagnostic-logs.md) |예외, 성능 문제 진단 |
| 클라이언트 사용 기본 사항: 페이지 보기, 세션,... |[웹 페이지의 JavaScript 이니셜라이저](app-insights-javascript.md) |사용 현황 분석 |
| 클라이언트 사용자 지정 메트릭 |[웹 페이지에서 추적 호출](app-insights-api-custom-events-metrics.md) |사용자 환경 향상 |
| 서버 사용자 지정 메트릭 |[서버에서 호출 추적](app-insights-api-custom-events-metrics.md) |비즈니스 인텔리전스 |

## <a name="automation"></a>Automation
Application Insights 리소스를 만들고 업데이트하는 [PowerShell 스크립트를 작성](app-insights-powershell.md) 할 수 있습니다.

## <a name="more-answers"></a>추가 대답
* [Application Insights 포럼](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[데이터]: app-insights-data-retention-privacy.md
[플랫폼]: app-insights-platforms.md
[시작]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Nov16_HO3-->


