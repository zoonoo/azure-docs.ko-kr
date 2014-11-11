<properties title="Diagnose performance issues on a running website" pageTitle="Diagnose performance issues on a running website" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

<!-- Required end of Sprint 69 - AUX48 -->

# 라이브 웹 사이트에서 현재 성능 모니터링

*Application Insights는 미리 보기 모드로 제공됩니다.*

오작동하는 웹 응용 프로그램이 있습니까? 다시 빌드하거나 다시 배포하지 않고 예외 및 성능 문제를 빠르게 진단할 수 있습니다. 서버에 Application Insights 에이전트를 설치하여 성능 병목을 찾아내고 모든 예외에 대한 스택 추적을 받으세요.


#### 언제 이 방법을 사용하여 Application Insights를 설정합니까?

이 접근 방식은 라이브 IIS 웹 사이트에서 빠르게 성능 문제를 진단할 때 주로 사용합니다.

서버에 에이전트를 설치하기만 하면 Application Insights에 대한 성능 데이터를 볼 수 있습니다.

- IIS 서버에 호스트되는 ASP.NET 앱에 이 방법을 적용할 수 있습니다.

- 데이터를 보려면 [Microsoft Azure 계정][Microsoft Azure 계정]이 있어야 합니다.

- 웹앱이 실행되는 서버에 대한 관리자 액세스 권한이 있어야 합니다.

- 웹앱의 코드는 필요하지 *않으며* 앱을 다시 빌드하거나 다시 배포할 필요가 없습니다.

- 이 방법은 해당 웹앱을 계측합니다. 추적 또는 로그 코드를 삽입하지 않습니다. 그러나 원하는 경우 나중에 이렇게 할 수 있습니다.

로깅 또는 진단 추적을 삽입하려는 경우 여기에서 더 진행하지 마세요. 대신 [기존][프로젝트에 Application Insights를 추가하고] 다시 배포하세요.

## 웹 서버에 Application Insights 에이전트 설치

1. 웹 서버에서 관리자 자격 증명으로 로그인합니다.

2. 버전 5.0 이상의 [웹 플랫폼 설치 관리자][웹 플랫폼 설치 관리자]가 있는지 확인합니다.
3. 웹 플랫폼 설치 관리자를 사용하여 Application Insights 에이전트를 설치합니다.

    ![][0]
4. 설치 마법사에서 Microsoft Azure에 로그인합니다.

    ![][1]
5. 모니터할 설치되어 있는 웹 응용 프로그램 또는 웹 사이트를 선택한 다음, Application Insights 포털의 결과를 볼 이름을 구성합니다. 마지막으로 '추가' 단추를 클릭합니다.

    ![][2]

    일반적으로, 새 리소스를 만들도록 선택해야 합니다.

    예를 들어, 사이트에 대한 [웹 테스트][웹 테스트]를 이미 설정한 경우 기존 리소스를 사용할 수 있습니다.

6. ApplicationInsights.config가 모니터할 웹 사이트에 삽입되어 있습니다.

    ![][3]

web.config에 대한 변경 내용이 몇 가지 있습니다.

### 나중에 다시 구성하시겠습니까?

마법사를 완료한 다음 원할 때마다 에이전트를 다시 구성할 수 있습니다. 에이전트를 설치했지만 초기 설정과 몇 가지 문제가 있는 경우에도 이를 사용할 수 있습니다.

![작업 표시줄의 Application Insights 아이콘 클릭][작업 표시줄의 Application Insights 아이콘 클릭]

## 데이터 보기

Azure에서 계정을 열고 Application Insights를 찾아보고 생성된 리소스를 엽니다.

![][4]

응용 프로그램 상태에서 데이터를 봅니다.

![][5]

#### 데이터가 없습니까?

  * 사이트를 사용하여 일부 데이터를 생성합니다.
  * 몇 분 정도 기다리면 데이터가 들어옵니다.
  * 서버 방화벽에서 포트 443을 통해 dc.services.visualstudio.com으로 나가는 트래픽을 허용하는지 확인합니다.


## <a name="next"></a> 다음 단계


[데이터 보기][데이터 보기]

[진단 로그 검색][진단 로그 검색]

[웹 테스트][웹 테스트]

[사용량 모니터링 설정][사용량 모니터링 설정]

[문제 해결][문제 해결]


## 자세한 정보

* [Application Insights][Application Insights]
* [프로젝트에 Application Insights 추가][사용량 모니터링 설정]
* [현재 라이브 웹 서버 모니터링][현재 라이브 웹 서버 모니터링]
* [Application Insights의 메트릭 탐색][데이터 보기]
* [진단 로그 검색][진단 로그 검색]
* [웹 테스트로 가용성 추적][웹 테스트]
* [이벤트 및 메트릭으로 사용량 추적][이벤트 및 메트릭으로 사용량 추적]
* [질문 및 답변과 문제 해결][문제 해결]


<!--Link references-->

[Microsoft Azure 계정]: http://azure.com
[웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx
[0]: ./media/appinsights/appinsights-031-wpi.png
[1]: ./media/appinsights/appinsights-035-signin.png
[2]: ./media/appinsights/appinsights-036-configAIC.png
[웹 테스트]: ../app-insights-monitor-web-app-availability/
[3]: ./media/appinsights/appinsights-034-aiconfig.png
[작업 표시줄의 Application Insights 아이콘 클릭]: ./media/appinsights/appinsights-033-aicRunning.png
[4]: ./media/appinsights/appinsights-08openApp.png
[5]: ./media/appinsights/appinsights-037-results.png
[데이터 보기]: ../app-insights-explore-metrics/
[진단 로그 검색]: ../app-insights-search-diagnostic-logs/
[사용량 모니터링 설정]: ../app-insights-monitor-application-health-usage/
[문제 해결]: ../app-insights-troubleshoot-faq/
[Application Insights]: ../app-insights-get-started/
[현재 라이브 웹 서버 모니터링]: ../app-insights-monitor-performance-live-website-now/
[이벤트 및 메트릭으로 사용량 추적]: ../app-insights-track-usage-custom-events-metrics/
