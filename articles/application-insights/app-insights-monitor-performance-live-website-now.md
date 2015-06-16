<properties 
	pageTitle="실행 중인 웹 사이트에서 성능 문제 진단" 
	description="다시 배포하지 않고 웹 사이트의 성능을 모니터링합니다. 독립 실행형 또는 Application Insights SDK 사용" 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-23" 
	ms.author="awills"/>
 

# 라이브 웹 사이트에서 현재 성능 모니터링

*Application Insights는 미리 보기 상태입니다.*

오작동하는 웹 응용 프로그램이 있나요? 다시 빌드하거나 다시 배포하지 않고 예외 및 성능 문제를 빠르게 진단할 수 있습니다. 서버에 Application Insights 에이전트를 설치하여 성능 병목을 찾아내고 모든 예외에 대한 스택 추적을 받으세요. 


#### 언제 이 방법을 사용하여 Application Insights를 설정하나요?

이 접근 방식은 라이브 IIS 웹 사이트에서 빠르게 성능 문제를 진단할 때 주로 사용합니다.

서버에 에이전트를 설치하기만 하면 Application Insights에 대한 성능 데이터를 볼 수 있습니다.

- IIS 서버에 호스트되는 ASP.NET 앱에 이 방법을 적용할 수 있습니다.

- 데이터를 보려면 [Microsoft Azure 계정](http://azure.com)이 있어야 합니다.

- 웹 앱이 실행되는 서버에 대한 관리자 액세스 권한이 있어야 합니다. 

- 웹 앱의 코드는 필요하지 *않으며* 앱을 다시 빌드하거나 다시 배포할 필요가 없습니다. 

- 이 방법은 해당 웹 앱을 계측합니다. 추적 또는 로그 코드를 삽입하지 않습니다. 그러나 원하는 경우 나중에 이렇게 할 수 있습니다.

로깅 또는 진단 추적을 삽입하려는 경우 여기에서 더 진행하지 마세요. 대신 [프로젝트에 Application Insights를 추가하고][greenbrown] 다시 배포하세요. 옵션의 전체 범위를 보려면 [Application Insights - 시작][start]을 읽어보세요.

## 웹 서버에 Application Insights 에이전트 설치

1. 웹 서버에서 관리자 자격 증명으로 로그인합니다.

2. 버전 5.0 이상의 [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)가 있는지 확인합니다.
3. 웹 플랫폼 설치 관리자를 사용하여 Application Insights 에이전트를 설치합니다.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-031-wpi.png)
4. 설치 마법사에서 Microsoft Azure에 로그인합니다.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)
5. 모니터링할 설치되어 있는 웹 응용 프로그램 또는 웹 사이트를 선택한 다음, Application Insights 포털의 결과를 볼 이름을 구성합니다. 마지막으로 '추가' 단추를 클릭합니다.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    일반적으로, 새 리소스를 만들도록 선택해야 합니다.

    예를 들어, 사이트에 대한 [웹 테스트][availability]를 이미 설정한 경우 기존 리소스를 사용할 수 있습니다.  

6. ApplicationInsights.config가 모니터링할 웹 사이트에 삽입되어 있습니다.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   web.config에 대한 변경 내용이 몇 가지 있습니다.

### 나중에 다시 구성하시겠어요?

마법사를 완료한 다음 원할 때마다 에이전트를 다시 구성할 수 있습니다. 에이전트를 설치했지만 초기 설정과 몇 가지 문제가 있는 경우에도 이를 사용할 수 있습니다.

![Click the Application Insights icon on the task bar](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## 데이터 보기

Azure에서 계정을 열고 Application Insights를 찾아보고 생성된 리소스를 엽니다.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

응용 프로그램 상태에서 데이터를 봅니다.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-037-results.png)

#### 데이터가 없나요?

  * 사이트를 사용하여 일부 데이터를 생성합니다.
  * 몇 분 정도 기다리면 데이터가 들어옵니다.
  * 서버 방화벽에서 포트 443을 통해 dc.services.visualstudio.com으로 나가는 트래픽을 허용하는지 확인합니다. 

#### 응용 프로그램 상태 보고서로 어떤 작업을 수행할 수 있나요?
 * [데이터를 이해하고 타일을 구성합니다.][perf]

## <a name="next"></a>다음 단계


[데이터 보기][perf]

[진단 로그 검색][diagnostic]

[웹 테스트][availability]

[사용량 모니터링 설정][start]

[문제 해결][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




<!--HONumber=46--> 
 