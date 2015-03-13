<properties 
	pageTitle="Application Insights를 시작합니다." 
	description="Application Insights를 사용하여 온-프레미스 또는 Microsoft Azure 웹 응용 프로그램의 사용량, 가용성 및 성능을 분석합니다.." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-10" 
	ms.author="awills"/>

# Application Insights - 앱의 상태 및 사용 현황 모니터링 시작

*Application Insights는 미리 보기 상태입니다.*

Application Insights에서는 라이브 응용 프로그램에 대한 다음 항목을 모니터링할 수 있습니다.

* **가용성** - 전 세계 어디든지 사용자의 URL을 몇 분마다 테스트합니다.
* **성능** - 성능 문제와 예외를 검색하고 진단합니다.
* **사용 현황** - 앱을 보다 효율적으로 개선하기 위해 사용자가 앱을 통해 수행하는 작업을 확인합니다.

구성은 매우 간단하며 결과는 몇 분 내에 확인할 수 있습니다. 현재는 사용자의 자체 서버나 Azure에 설치된 ASP.NET 웹 앱이 지원됩니다.


## 시작

순서에 관계 없이 이 차트 왼쪽의 시작 위치를 조합해서 시작할 수 있습니다. 본인에게 적합한 경로를 선택하세요. ASP.NET 웹 앱을 개발하는 경우는 먼저 웹 프로젝트에 Application Insights를 추가합니다. 다른 항목은 나중에 쉽게 추가할 수 있습니다.

[Microsoft Azure](http://azure.com) 계정이 필요합니다(VSO 버전을 사용하지 않는 경우).

<table >
<tr valign="top"><th>필요한 항목</th><th colspan="2">수행할 작업</th><th>결과</th></tr>
<tr valign="top"><td>내 ASP.NET 앱의 성능 및 사용 현황 분석</td><td colspan="2"><a href="../app-insights-start-monitoring-app-health-usage/">웹 프로젝트에 Application Insights 추가</a></td><td>성능 메트릭: 로드 수, 응답 시간...</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">서버 코드에서 이벤트 및 메트릭 보내기</a></td><td>사용자 지정 비즈니스 분석</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">서버에서 추적 및 예외 원격 분석을 보내거나 타사 로그 데이터를 캡처합니다.</td><td>서버 앱 진단. 로그 데이터를 검색 및 필터링합니다.</a></td></tr>
<tr valign="top"><td>모든 플랫폼의 웹 페이지에 대한 사용 분석 가져오기</td><td colspan="2"><a href="../app-insights-web-track-usage/">웹 페이지에 AI 스크립트 삽입</a></td><td>사용 현황 분석: 페이지 뷰, 복귀 사용자, 세션 수</td></tr>
<tr valign="top"><td></td><td>&nbsp;&nbsp;</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">웹 페이지 스크립트에 이벤트 및 메트릭 호출 쓰기</a></td><td>사용자 지정 사용자 환경 분석</td></tr>
<tr valign="top"><td></td><td></td><td><a href="../app-insights-search-diagnostic-logs/">웹 페이지 스크립트에 추적 및 진단 호출 쓰기</a></td><td>로그 데이터를 검색 및 필터링합니다.</td></tr>
<tr valign="top"><td>웹 서버에서 이미 실행 중인 ASP.NET 앱의 문제 진단</td><td colspan="2"><a href="../app-insights-monitor-performance-live-website-now/">웹 서버에 상태 모니터 설치</a></td><td>종속성 호출 기간 및 수, CPU, 메모리 및 네트워크 카운터, 로드 수, 응답 시간</td></tr>
<tr valign="top"><td>웹 페이지의 가용성 모니터링</td><td colspan="2"><a href="../app-insights-monitor-web-app-availability/">Application Insights에 웹 테스트 설정</a></td><td>가용성 모니터 및 경고</td></tr>
<tr valign="top"><td>Windows Phone 앱, Windows 스토어 앱 또는 Java 웹 사이트에 대한 성능 및 사용 현황 분석 가져오기</td><td colspan="2"><a href="http://msdn.microsoft.com/library/dn481095.aspx">이제 이전 VSO 버전의 Application Insights 사용</a></td><td>사용 현황 및 성능 분석. <a href="http://msdn.microsoft.com/library/dn793604.aspx">Azure 버전에서 기능을 점진적으로 구축하고 있습니다.</a></td></tr>
</table>


## <a name="video"></a>비디오

#### 소개

> [AZURE.VIDEO application-insights-introduction]

#### 시작

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 
