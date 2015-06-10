<properties 
	pageTitle="Application Insights에서 경고 설정" 
	description="충돌, 예외, 메트릭 변경 사항에 대한 전자 메일을 받습니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="awills"/>
 
# Application Insights에서 경고 설정

[Visual Studio Application Insights][start]는 앱의 성능 및 사용 메트릭이 변경되면 사용자에게 경고할 수 있습니다.

Application Insights는 [다양한 플랫폼][platforms]에서 라이브 앱을 모니터링하여 성능 문제를 진단하고 사용 패턴을 이해할 수 있습니다.

경고의 종류는 두 가지입니다.
 
* **웹 테스트**는 인터넷에서 사이트를 사용할 수 없거나 응답 속도가 느려지면 그 사실을 알려줍니다. [자세히 알아봅니다][availability].
* **메트릭 경고**는 어떤 메트릭이 실패 수, 메모리, 페이지 보기 등의 임계값을 일정 기간 동안 초과하면 그 사실을 알려줍니다. 

[웹 테스트에 대한 별도의 페이지][availability]를 준비해 두었으니 지금은 메트릭 경고를 집중적으로 살펴보겠습니다.

## 메트릭 경고

앱에 대해 Application Insights를 설정하지 않았다면 일단 [설정 작업][start]부터 수행하세요.

메트릭이 임계값을 초과할 때 전자 메일을 받으려면 메트릭 탐색기 또는 개요 블레이드의 경고 규칙 타일에서 시작합니다.

![경고 규칙 블레이드에서 경고 추가를 선택합니다. 측정할 리소스로 앱을 설정하고, 경고의 이름을 입력하고, 메트릭을 선택합니다.](./media/app-insights-alerts/01-set-metric.png)

다른 속성에 앞서 리소스를 설정합니다. 성능 또는 사용 메트릭에 대한 경고를 설정하려면 webtest 리소스를 선택하지 마세요.

임계값을 입력하라는 단위에 주의하세요.

*경고 추가 단추가 보이지 않습니다.* - 조직 계정을 사용 중이신가요? 이 응용 프로그램 리소스에 소유자 또는 참가자 액세스가 가능하면 경고를 설정할 수 있습니다. 설정 -> 사용자를 살펴보세요. [액세스 제어에 대해 자세히 알아보세요][roles].

## 경고 보기

경고는 전자 메일을 통해 제공됩니다.

또한 작업 이벤트 로그에도 표시됩니다.

![하단 근처의 개요 블레이드에서 '지난 주의 이벤트'를 클릭합니다.](./media/app-insights-metrics-explorer/09-alerts.png)

*이러한 "이벤트"가 원격 분석 이벤트 또는 사용자 지정 이벤트와 관련이 있나요?*

* 아니요. 이러한 작업 이벤트는 이 응용 프로그램 리소스에 발생한 일에 대한 기록일 뿐입니다. 

## 가용성 경고

전세계 지점에서 모든 웹사이트를 테스트하도록 웹 테스트를 설정할 수 있습니다. [자세히 알아봅니다][availability].

## 어떤 경고를 설정하면 좋은가요?

응용 프로그램에 따라 다릅니다. 처음에는 경고를 너무 많이 설정하지 않는 것이 좋습니다. 일단 앱이 실행되는 동안 메트릭 차트를 살펴보면서 정상적으로 작동할 때의 상태를 숙지합니다. 이렇게 하면 성능을 개선하는 방법을 찾는 데 도움이 됩니다. 그런 다음 메트릭이 정상 영역을 벗어나면 알려주는 경고를 설정합니다.

다음은 많은 사람들이 사용하는 경고입니다.

* [웹 테스트][availability] - 응용 프로그램이 공용 인터넷에 표시되는 웹사이트 또는 웹 서비스인 경우에 중요합니다. 사이트가 중단되거나 응답 속도가 느려지면 사용자에게 알립니다. 앱이 아니라 통신 회사 문제인 경우에도 사용자에게 알립니다. 하지만 가상 테스트이기 때문에 사용자의 실제 경험을 측정하지는 않습니다.
* [브라우저 메트릭][client] - 특히 브라우저 페이지 로드 시간은 웹 응용 프로그램에 매우 유용합니다. 페이지에 스크립트가 많으면 브라우저 예외를 살펴보아야 합니다. 이러한 메트릭 및 경고를 가져오려면 [웹 페이지 모니터링][client]을 설정해야 합니다.
* 서버 응답 시간 및 서버 쪽 웹 응용 프로그램에 대한 실패한 요청  경고 설정 외에도 이러한 메트릭을 주시하여 메트릭이 불균형적으로 변하고 요청 속도가 빠른지 살펴보는 것이 좋습니다. 앱 리소스가 부족하다는 의미일 수 있습니다.



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

<!---HONumber=58-->