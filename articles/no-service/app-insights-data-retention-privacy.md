<properties 
	pageTitle="Data retention and storage in Application Insights" 
	description="Retention and privacy policy statement" 
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
	ms.date="11/14/2014" 
	ms.author="awills"/>

# Application Insights 데이터 보존 및 저장소 

*Application Insights는 미리 보기 상태입니다.*


이 문서에서는 응용 프로그램이 Application Insights에 보내는 원격 분석을 Microsoft에서 저장하는 방법에 대한 질문에 대답합니다.

Application Insights는 미국 내의 Microsoft Azure 서버에 데이터를 저장합니다. Microsoft Azure 정책에 대한 자세한 내용을 보려면 [보안, 개인정보취급방침 및 규정 준수 백서](http://go.microsoft.com/fwlink/?LinkId=392408)(영문)를 다운로드하세요. Application Insights에 관한 몇 가지 질문에 대한 대답이 아래에 나와 있습니다. 

#### 데이터가 얼마나 저장되나요? 

Application Insights가 이 무료 미리 보기 단계인 동안에는 각 계정에 대해 다음 항목을 저장합니다. 

* 초당 최대 500개의 원격 분석 메시지(또는 분당 30K) 

* 매월 최대 10M 페이지 보기 또는 이벤트 

*한도를 초과하면 어떻게 되나요?* 

* 짧은 한도를 초과하면 해당 한도를 계산한 후 일부 메시지를 삭제합니다. 진단 검색에 해당 메시지가 표시되지 않습니다. 이벤트 수가 올바르게 됩니다. 평균 메트릭은 유지되는 이벤트를 기반으로 하고 유효해야 합니다. 

* 월 한도를 초과하면 이벤트 기록이 중지되므로 일부 날짜 이후의 메트릭은 0으로 표시됩니다. 

####원격 분석은 얼마 동안 유지되나요? 

* 인스턴스 데이터의 경우 진단 검색에 7일간 표시됩니다. 개별 페이지 보기, 이벤트, 로그 메시지, 추적 및 예외가 여기에 해당합니다. 

* 집계 데이터의 경우 메트릭 탐색기에 13개월간 표시됩니다. 메트릭, 이벤트 및 예외에 대한 통계(유형 수, 실패한 함수 등)는 30일간 1분(또는 그 이하) 단위로 보존되고 13개월간 1시간 단위로 보존됩니다. 

####데이터가 저장되는 위치는 어디인가요? 

* 미국입니다. 

유럽 또는 그 밖의 국가에 저장하는 옵션이 있나요? 

* 아직 없습니다. 

####원격 분석 데이터는 어느 정도 안전한가요? 

데이터는 Microsoft Azure 서버에 저장되며, Azure 미리 보기 포털 계정의 경우 계정 제한 사항이 Azure 보안, 개인정보취급방침 및 규정 준수 문서에 설명되어 있습니다. Visual Studio Online 포털 계정의 경우 VS Online 데이터 보호 문서가 적용됩니다. 

Microsoft 직원의 사용자 데이터에 대한 액세스는 제한되어 있습니다. Microsoft에서는 사용자가 허락한 경우에만, 그리고 Application Insights 사용을 지원하는 데 필요한 경우에 사용자 데이터에 액세스합니다. 

####데이터는 Application Insights 서버에서 암호화되나요? 

현재는 암호화되지 않습니다. 

####PII(개인 식별이 가능한 정보)가 Application Insights에 전송될 수 있나요? 

예. 사용자 지정 원격 분석의 코드에 의해 Application Insights로 전송된 PII와 표준 원격 분석에 포함된 PII 둘 다 전송될 수 있습니다. Azure 개인정보취급방침이 Application Insights에 적용됩니다. 

데이터는 여러 가지 방법으로 포털에 전송될 수 있습니다. 그런 다음 진단 검색에 표시될 수 있습니다. 조직의 구성원이 데이터를 내보내고 다운로드할 수도 있습니다. 

* 상태 모니터를 설치하거나 프로젝트에 Application Insights를 추가할 경우 예외 또는 성능 경고가 발생할 때 스택 추적이 캡처됩니다. 여기에는 실제 매개 변수 데이터(예: SQL 데이터)가 포함될 수 있습니다. 

* 코드에 TrackEvent 같은 원격 분석 호출을 삽입하거나 로깅 프레임워크 메시지를 캡처하는 경우 속성에 PII가 포함될 수 있습니다. Microsoft Online Services 약관도 적용됩니다. 특히, Application Insights의 경우 개인정보취급방침 및 데이터 수집에 적용되는 법률 및 규정을 준수해야 합니다. 정보를 수집하기 전에 알림 또는 기타 동의가 필요한지 여부를 확인해야 합니다. 


####Application Insights 사용 시 전송되는 원격 분석은 무엇인가요? 

설치할 수 있는 여러 가지 구성 요소가 있습니다 ([Application Insights - 시작][start] 참조). 

<table>
<tr><th>설치할 기능</th><th>포털에 전송되는 원격 분석</th><th>중요한 데이터 포함 가능 여부</th></tr>
<tr><td><a href="../app-insights-start-monitoring-app-health-usage/">웹 프로젝트에 Application Insights 추가</a></td>
  <td>메트릭: 서버 요청 수, 서버 응답 시간</td>
  <td>아니요</td></tr>
<tr><td></td>
  <td>서버의 예외 보고서</td><td>스택 덤프가 매개 변수 값을 포함할 수 있음</td></tr>
<tr><td><a href="../app-insights-web-track-usage-custom-events-metrics/">사용자 지정 이벤트 및 메트릭 추적</a></td>
  <td>코드에 의해 연결된 속성이 포함된 이벤트</td>
  <td>예(코드가 속성 또는 제목에 PII를 포함해서 보내는 경우)</td></tr>
<tr><td><a href="../app-insights-search-diagnostic-logs/#trace">로그 및 추적 원격 분석</a></td><td>로그 및 추적 메시지</td><td>로그 메시지가 PII를 포함할 수 있음</td></tr>
<tr><td><a href="../app-insights-web-track-usage/">웹 페이지에 AI 스크립트 삽입</a></td>
  <td>익명으로 된 사용자 및 계정 ID</td><td>아니요</td></tr>
<tr><td></td><td>익명 사용자 세션 시작 및 종료</td><td>아니요</td></tr>
<tr><td></td><td>페이지 URI, 로드 시간, 세션 타이밍</td><td>아니요</td></tr>
<tr><td><a href="../app-insights-monitor-performance-live-website-now/">서버에 상태 모니터 설치</a></td>
  <td>종속성 호출 및 기간</td>
  <td>호출 데이터가 매개 변수(예: SQL 필드)를 포함할 수 있음</td></tr>
<tr><td></td><td>CPU, 메모리, 네트워크 및 기타 리소스 카운터</td><td>아니요</td></tr>
<tr><td><a href="../app-insights-monitor-web-app-availability/">웹 테스트</a></td><td>웹에서 보았을 때 가용성 및 응답 시간</td><td>아니요</td></tr>
</table>

## <a name="video"></a>비디오

#### 소개

> [AZURE.VIDEO application-insights-introduction]

#### 시작

> [AZURE.VIDEO getting-started-with-application-insights]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=35.2-->
 
