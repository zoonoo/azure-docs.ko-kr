<properties 
	pageTitle="Application Insights 로깅 어댑터에 대한 릴리스 정보" 
	description="최신 업데이트입니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# .NET의 로깅 어댑터에 대한 릴리스 정보

log4net, NLog 또는 System.Diagnostics.Trace 같은 로깅 프레임워크를 이미 사용 중인 경우 이러한 로그를 캡처하여 [Application Insights](http://azure.microsoft.com/services/application-insights/)로 전송할 수 있습니다. 여기서 로그 추적을 사용자 작업, 예외 및 다른 이벤트와 상관 관계를 지정할 수 있습니다.


#### 시작하기:

[Application Insights에서 ASP.NET에 대한 로그, 예외 및 사용자 지정 진단](app-insights-search-diagnostic-logs.md)을 참조하세요.

## 버전 1.2.6

- 버그 수정
- log4Net: 사용자 지정 속성으로 log4net 속성을 수집합니다. UserName은 더 이상 사용자 지정 속성이 아닙니다. telemetry.Context.User.Id로 수집됩니다. Timestamp는 더 이상 사용자 지정 속성이 아닙니다.
- NLog: 사용자 지정 속성으로 NLog 속성을 수집합니다. SequenceID는 더 이상 사용자 지정 속성이 아닙니다. telemetry.Sequence로 수집됩니다. Timestamp는 더 이상 사용자 지정 속성이 아닙니다. 

## 버전 1.2.5
- 처음 오픈 소스 버전: Application Insights API 버전 1.2.3 이상을 참조합니다.

<!---HONumber=AcomDC_0107_2016-->