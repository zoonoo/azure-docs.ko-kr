<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Application Insights - 시작

*Application Insights는 미리 보기 모드로 제공됩니다.*

Application Insights를 통해 라이브 응용 프로그램의 가용성, 성능 및 사용량을 모니터할 수 있습니다. Microsoft Azure 응용 프로그램일 필요는 없습니다. 구성은 매우 간단하며 몇 분 내에 결과를 볼 수 있습니다.

* **가용성** - 웹앱이 사용 가능하며 반응하고 있는지 확인합니다. 전 세계에서 여러 위치에서 몇 분마다 URL을 테스트하므로, 문제가 있으면 바로 알려줄 수 있습니다.
* **성능** - 웹 서비스의 성능 문제 또는 예외를 진단합니다. 응답 시간이 요청 수에 따라 어떻게 달라지는지 확인하고, CPU 또는 기타 리소스가 확장되는지 알아보고, 예외에서 스택 추적을 가져오고, 로그 추적을 통해 쉽게 검색합니다.
* **사용량** - 가장 유용한 개발 작업에 중점을 둘 수 있도록 사용자가 앱으로 수행하는 작업을 알아봅니다. 현재, 웹앱, Windows 스토어 및 Windows Phone 앱을 모니터할 수 있습니다.

## 시작

시작하는 방법은 두 가지입니다.

* [Visual Studio의 프로젝트에 Application Insights 추가][Visual Studio의 프로젝트에 Application Insights 추가]

    프로젝트에 Application Insights를 추가하여 사용량, 성능 및 가용성을 추적하고 진단 로그를 분석할 수 있습니다. 디버그 모드에서 몇 분 내에 데이터를 본 다음 프로젝트를 배포하여 라이브 데이터를 가져올 수 있습니다.

    프로젝트를 업데이트하거나 만드는 경우 이 옵션을 사용하세요.

    [프로젝트에 Application Insights를 추가하여 시작][Visual Studio의 프로젝트에 Application Insights 추가]

* [이제 라이브 웹 서비스의 문제 진단][이제 라이브 웹 서비스의 문제 진단]

    IIS 서버에 Application Insights 에이전트를 설치하고 몇 분 내에 성능 데이터를 확인합니다. 요청 수, 응답 시간, 리소스 부하를 확인하고 예외 추적을 가져옵니다.

    지금 웹 서버에서 진행되는 상황을 이해해야 하는 경우 이 옵션을 사용하세요. 코드 다시 배포와 관련되지는 않습니다. 그러나 서버에 대한 관리자 액세스 권한 및 Microsoft Azure 계정이 있어야 합니다.

    언제든 가용성 모니터링을 추가할 수 있습니다.

    나중에 다른 옵션을 사용하여 프로젝트에 Application Insights를 추가해서 진단 로그 및 추적 사용량을 분석할 수 있습니다.

    [웹 서버에 Application Insights를 설치하여 시작][이제 라이브 웹 서비스의 문제 진단]

> [WACOM.NOTE] Visual Studio Online에 [이전 버전의 Application Insights][이전 버전의 Application Insights]가 있습니다. Microsoft에서는 Microsoft Azure의 일부로 처음부터 다시 빌드하고 있으며 여기에서 보게 될 새로운 버전이 될 것입니다.

![Application Insights의 예제 응용 프로그램 모니터][Application Insights의 예제 응용 프로그램 모니터]

## 자세한 정보

* [Application Insights][Application Insights]
* [프로젝트에 Application Insights 추가][Visual Studio의 프로젝트에 Application Insights 추가]
* [현재 라이브 웹 서버 모니터링][이제 라이브 웹 서비스의 문제 진단]
* [Application Insights의 메트릭 탐색][Application Insights의 메트릭 탐색]
* [진단 로그 검색][진단 로그 검색]
* [웹 테스트로 가용성 추적][웹 테스트로 가용성 추적]
* [이벤트 및 메트릭으로 사용량 추적][이벤트 및 메트릭으로 사용량 추적]
* [질문 및 답변과 문제 해결][질문 및 답변과 문제 해결]


<!--Link references-->

[Visual Studio의 프로젝트에 Application Insights 추가]: ../app-insights-monitor-application-health-usage/
[이제 라이브 웹 서비스의 문제 진단]: ../app-insights-monitor-performance-live-website-now/
[이전 버전의 Application Insights]: http://msdn.microsoft.com/ko--kr/library/dn481095.aspx
[Application Insights의 예제 응용 프로그램 모니터]: ./media/appinsights/appinsights-00-appblade.png
[Application Insights]: ../app-insights-get-started/
[Application Insights의 메트릭 탐색]: ../app-insights-explore-metrics/
[진단 로그 검색]: ../app-insights-search-diagnostic-logs/
[웹 테스트로 가용성 추적]: ../app-insights-monitor-web-app-availability/
[이벤트 및 메트릭으로 사용량 추적]: ../app-insights-web-track-usage-custom-events-metrics/
[질문 및 답변과 문제 해결]: ../app-insights-troubleshoot-faq/
