<properties 
	pageTitle="Application Insights를 사용하여 Windows 스토어 및 Phone 앱에서 충돌 감지 및 진단" 
	description="Application Insights를 사용하여 Windows 장치 앱의 성능 문제를 분석합니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2015" 
	ms.author="awills"/>

# Application Insights를 사용하여 Windows 스토어 및 Phone 앱에서 충돌 감지 및 진단

*Application Insights는 미리 보기 상태입니다.*

사용자가 앱에서 충돌을 경험하는 경우, 이에 대해 신속하게 파악하고 세부 정보를 알고 싶습니다. Application Insights를 사용하여 충돌 발생 빈도, 발생 시 경고 얻기 및 개별 인시던트 보고서 조사에 대해 모니터링할 수 있습니다.

"충돌"은 응용 프로그램이 확인할 수 없는 예외 때문에 종료하는 것을 의미합니다. 앱이 예외를 catch하는 경우 [TrackException API][apiexceptions]로 해당 예외를 보고하지만 계속 실행할 수 있습니다. 이 경우에 충돌로 로깅되지 않습니다.


## 충돌 빈도 모니터링

아직 실행하지 않은 경우 [앱 프로젝트에 Application Insights][windows]를 추가하고 다시 게시합니다.

[Application Insights 포털][portal]의 응용 프로그램 개요 블레이드에 충돌이 표시됩니다.

![](./media/app-insights-windows-crashes/appinsights-d018-oview.png)

차트에 표시 되는 시간 범위를 편집할 수 있습니다.


## 충돌을 감지하도록 경고 설정

![충돌 차트에서 경고 규칙 다음에 경고 추가를 클릭합니다.](./media/app-insights-windows-crashes/appinsights-d023-alert.png)

## 충돌 진단

일부 앱 버전이 다른 버전보다 충돌이 더 많은지 알아보려면 충돌 차트를 클릭하여 응용 프로그램 버전별로 구분합니다.

![](./media/app-insights-windows-crashes/appinsights-d26crashSegment.png)


충돌을 유발하는 예외 사항을 발견하려면 진단 검색을 엽니다. 예외 사항에 초점을 맞추기 위해 다른 유형의 원격 분석을 제거하고자 할 수 있습니다.

![](./media/app-insights-windows-crashes/appinsights-d26crashExceptions.png)

[진단 검색 필터링에 대해 자세히 알아봅니다][diagnostic].
 

관련 속성 및 스택 추적을 비롯한 세부 정보를 보려면 예외 사항을 클릭합니다.

![](./media/app-insights-windows-crashes/appinsights-d26crash.png)

해당 예외 사항 가까이 발생한 다른 예외 사항 및 이벤트를 확인합니다.


![](./media/app-insights-windows-crashes/appinsights-d26crashRelated.png)

## 추적 로그 및 이벤트 삽입

[추적 호출을 삽입하고 Application Insights에서 로그를 검색][diagnostic]하여 문제를 진단할 수 있습니다.

## <a name="debug"></a>디버그 및 릴리스 모드 비교

#### 디버그

디버그 모드에서 빌드하는 경우 이벤트는 생성되자마자 전송됩니다. 인터넷 연결이 손실되고 다시 연결되기 전에 앱을 종료하는 경우 오프라인 원격 분석이 삭제됩니다.

#### 릴리스

릴리스 구성에서 빌드하는 경우 이벤트는 장치에 저장되고 응용 프로그램이 다시 시작하면 전송됩니다. 데이터는 처음 사용하는 응용 프로그램에서도 전송됩니다. 시작 시 인터넷에 연결이 없는 경우 현재 수명 주기에 대한 원격 분석뿐 아니라 이전 원격 분석도 저장되며 다음 다시 시작 시 전송됩니다.

## <a name="next"></a>다음 단계

[Application Insights를 사용하여 문제 감지, 심사 및 진단][detect]

[Application Insights API][api]

[진단 로그 캡처][trace]

[문제 해결](app-insights-windows-troubleshoot.md)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->