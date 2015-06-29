<properties 
	pageTitle="Application Insights 데이터 보존 및 저장소" 
	description="보존 및 개인 정보 취급 방침" 
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
	ms.date="05/11/2015" 
	ms.author="awills"/>

# Application Insights 데이터 수집, 보존 및 저장소 

*Application Insights는 미리 보기 상태입니다.*

## 개요

이 문서에서는 [Visual Studio Application Insights][start]가 수집하는 데이터와 처리 및 저장 방법에 대한 질문에 대답합니다.

Application Insights는 미리 보기 상태의 Azure 서비스입니다. 미리 보기 상태에서는 [Azure 보안, 개인 정보 및 규정 준수 백서](http://go.microsoft.com/fwlink/?linkid=392408)에 설명된 정책에 따라 데이터를 보호하기 위해 노력합니다.


## 컬렉션

#### Application Insights는 어떻게 데이터를 수집하나요?

응용 프로그램과 결합하는 Application Insights SDK 및 에이전트가 Application Insights 서비스로 데이터를 보냅니다. 서비스에서 데이터를 처리하여 보고서, 경고 및 기타 기능을 제공합니다. 여기에는 속성 및 사용자 지정 이벤트와 같은 API를 사용하여 캡처하도록 선택한 데이터가 포함될 수 있습니다.

#### 얼마나 많은 데이터를 캡처할 수 있나요? 

**초당**: 현재 계측 키마다(즉, 응용 프로그램마다) 초당 최대 500개 데이터 요소입니다. 무료 [가격 책정 계층][pricing]의 경우, 100 dp/s로 제한됩니다.

**월별**: [계획 가격](http://azure.microsoft.com/pricing/details/application-insights/)에 따라 각 달력 월에서 5와 1500만 사이의 데이터 요소. 무료 [가격 책정 계층][pricing]을 제외하고, 제한에 도달한 경우 추가 용량을 구입할 수 있습니다.


*데이터 요소*는 다음과 같은 원격 분석 항목입니다.

* API `Track...` 호출(예: `TrackEvent` 또는 `trackPageView`).
* 예를 들어 요청 또는 충돌을 보고하기 위해 SDK 모듈에서 보내는 원격 분석 항목입니다.
* 성능 카운터 데이터 - 각 측정마다 요소 1개

*내 앱이 보내는 데이터 요소 수를 어떻게 알 수 있나요?*

* 데이터 볼륨 차트를 보려면 설정/할당량 및 가격을 엽니다.
* 또는 메트릭 탐색기에서 새 차트를 추가하고 **데이터 요소 볼륨**을 메트릭으로 선택합니다. 그룹화로 전환한 다음 **데이터 형식**을 기준으로 그룹화합니다.


#### 데이터가 얼마 동안 보존되나요? 

[계획 가격](http://azure.microsoft.com/pricing/details/application-insights/)에 따라 다릅니다.

원시 데이터 요소(즉, 진단 검색에서 검사할 수 있는 항목): 7일에서 30일 사이입니다.

집계 데이터(즉, 메트릭 탐색기에 표시되는 개수, 평균 및 기타 통계 데이터)는 30일 동안 1분 단위로 보존되고, 13개월 동안 형식에 따라 1시간 또는 1일 단위로 보존됩니다.

#### 각 데이터 형식에 어떤 제한 사항이 있나요?

1.	응용 프로그램에는 최대 200개의 고유한 메트릭 이름과 200개의 고유한 속성 이름이 허용됩니다. 메트릭은 TrackMetric을 통해 전송된 데이터와 이벤트 같은 기타 데이터 형식의 측정을 포함합니다. [메트릭 및 속성 이름][api]의 범위는 데이터 형식으로 한정되지 않고 계측 키마다 전역적입니다.
2.	[속성][apiproperties]은 필터링 및 그룹화에만 사용할 수 있으며 각 속성에 허용되는 고유한 값은 100개 미만입니다. 고유한 값이 100개를 초과할 경우 해당 속성을 검색 및 필터링에는 계속 사용할 수 있지만 필터에는 더 이상 사용할 수 없습니다.
3.	요청 이름 및 페이지 URL 같은 표준 속성은 일주일에 고유한 값 1000개로 제한됩니다. 고유한 값이 1000개를 초과할 경우 초과하는 값이 "기타 값"으로 표시됩니다. 원래 값을 전체 텍스트 검색 및 필터링에 계속 사용할 수 있습니다.


## Access

#### 누가 데이터를 볼 수 있나요?

데이터는 사용자 및 조직 계정이 있는 경우 팀 멤버에게 표시됩니다.

사용자와 팀 멤버는 데이터를 내보낼 수 있으며, 다른 위치로 복사하고 다른 사람에게 전달할 수 있습니다.

#### Microsoft는 내 앱이 Application Insights로 보내는 정보로 무엇을 하나요?

Microsoft는 서비스를 제공하기 위한 목적으로만 데이터를 사용합니다.


## 위치

#### 데이터가 저장되는 위치는 어디인가요? 

* 미국입니다. 

#### 유럽 등의 다른 곳에 저장할 수 있나요? 

* 아직 없습니다. 

## 보안 

#### 내 데이터는 어느 정도 안전한가요? 

데이터는 Microsoft Azure 서버에 저장됩니다. Azure Preview 포털 계정의 경우 [Azure 보안, 개인 정보 및 규정 준수 문서](http://go.microsoft.com/fwlink/?linkid=392408)에 계정 제한 사항이 설명되어 있습니다. Visual Studio Online 포털 계정의 경우 [Visual Studio Online 데이터 보호](http://download.microsoft.com/download/8/E/E/8EE6A61C-44C2-4F81-B870-A267F1DF978C/MicrosoftVisualStudioOnlineDataProtection.pdf) 문서가 적용됩니다.

Microsoft 직원의 사용자 데이터에 대한 액세스는 제한되어 있습니다. Microsoft에서는 사용자가 허락한 경우에만, 그리고 Application Insights 사용을 지원하는 데 필요한 경우에 사용자 데이터에 액세스합니다.

모든 고객의 응용 프로그램에 대한 집계 데이터(예: 데이터 속도 및 평균 추적 크기)는 Application Insights를 개선하는 데 사용됩니다.

#### 다른 사람의 원격 분석이 내 Application Insights 데이터에 방해가 될 수 있나요?

웹 페이지의 코드에서 찾을 수 있는 계측 키를 사용하여 계정에 추가 원격 분석을 보낼 수 있습니다. 추가 데이터가 충분하면 메트릭이 앱의 성능 및 사용을 올바르게 나타냅니다.

다른 프로젝트와 코드를 공유하는 경우 계측 키를 제거해야 합니다.

## 암호화

#### 데이터는 Application Insights 서버에서 암호화되나요? 

현재 서버 내에서 암호화되지 않습니다.

모든 데이터는 데이터 센터 간에 이동할 때 암호화됩니다.

#### 내 응용 프로그램에서 Application Insights 서버로 전송 중에 데이터가 암호화되나요?

예. 웹 서버, 장치 및 HTTPS 웹 페이지를 포함하여 거의 모든 SDK에서 https를 사용하여 포털로 데이터를 보냅니다. 유일한 예외는 일반 HTTP 웹 페이지에서 전송된 데이터입니다.

## 개인 식별이 가능한 정보

#### PII(개인 식별이 가능한 정보)가 Application Insights에 전송될 수 있나요? 

예.

일반 지침:

* 대부분의 표준 원격 분석(즉, 코드를 작성하지 않고 전송된 원격 분석)은 명시적 PII를 포함하지 않습니다. 그러나 이벤트 컬렉션에서 유추하여 개인을 식별할 수 있습니다.
* 예외 및 추적 메시지는 PII를 포함할 수 있습니다.
* 사용자 지정 원격 분석(즉, API 또는 로그 추적을 사용하여 코드로 작성하는 TrackEvent와 같은 호출)은 선택한 모든 데이터를 포함할 수 있습니다.


이 문서의 끝에 있는 표에 수집되는 데이터에 대한 자세한 설명이 포함되어 있습니다.



#### PII와 관련된 법률 및 규정을 준수해야 하나요?

예. 데이터의 수집 및 사용은 법률 및 규정과 Microsoft Online Services 사용 약관을 준수해야 합니다.

응용 프로그램이 수집하는 데이터 및 데이터 사용 방법에 대해 고객에게 적절하게 알려야 합니다.

#### 내 사용자가 Application Insights를 끌 수 있나요?

직접 끌 수는 없습니다. 사용자가 Application Insights를 끄기 위해 작동할 수 있는 스위치는 제공되지 않습니다.

그러나 응용 프로그램에서 이러한 기능을 구현할 수 있습니다. 모든 SDK에는 원격 분석 수집을 끄는 API 설정이 포함되어 있습니다.

#### 내 응용 프로그램이 의도하지 않게 중요한 정보를 수집하고 있습니다. Application Insights에서 이 데이터가 보존되지 않도록 삭제할 수 있나요?

Application Insights는 데이터를 필터링하거나 삭제하지 않습니다. 데이터를 적절하게 관리하여 이러한 데이터가 Application Insights로 전송되지 않도록 해야 합니다.



## Application Insights에서 보내는 데이터

SDK는 플랫폼마다 다르며, 설치할 수 있는 여러 구성 요소가 있습니다. [Application Insights - 시작][start]을 참조하세요. 각 구성 요소마다 다른 데이터를 보냅니다.

#### 다양한 시나리오에서 전송되는 데이터 클래스

사용자 작업 | 수집되는 데이터 클래스(다음 표 참조)
---|---
[.NET 웹 프로젝트에 Application Insights SDK 추가][greenbrown] | ServerContext<br/>유추<br/>성능 카운터<br/>요청<br/>**예외**<br/>세션<br/>사용자
[IIS에 상태 모니터 설치][redfield]<br/>[Azure VM 또는 웹앱에 AI 확장 추가][azure]|종속성<br/>ServerContext<br/>유추<br/>성능 카운터
[Java 웹앱에 Application Insights SDK 추가][java]|ServerContext<br/>유추<br/>요청<br/>세션<br/>사용자
[웹 페이지에 JavaScript SDK 추가][client]|ClientContext<br/>유추<br/>페이지<br/>ClientPerf
[Windows 스토어 앱에 SDK 추가][windows]|DeviceContext<br/>사용자<br/>충돌 데이터
[기본 속성 정의][apiproperties]|모든 표준 및 사용자 지정 이벤트의 **속성**
[호출 TrackMetric][api]|숫자 값<br/>**속성**
[호출 추적*][api]|이벤트 이름<br/>**속성**
[호출 TrackException][api]|**예외**<br/>스택 덤프<br/>**속성**
SDK는 데이터를 수집할 수 없습니다. 예: <br/> - 성능 카운터에 액세스할 수 없음<br/> - 원격 분석 이니셜라이저 예외 | SDK 진단
 

[다른 플랫폼에 대한 SDK][platforms]의 경우 해당 문서를 참조하세요.



#### 수집되는 데이터 클래스

수집되는 데이터 클래스 | 포함(전체 목록 아님) 
---|---
**속성**|**임의 데이터 - 코드에 의해 결정됨**
DeviceContext |ID, IP, 로캘, 장치 모델, 네트워크, 네트워크 종류, OEM 이름, 화면 해상도, 역할 인스턴스, 역할 이름, 장치 유형
ClientContext |OS, 로캘, 언어, 네트워크, 창 해상도
세션 | 세션 ID
ServerContext |컴퓨터 이름, 로캘, OS, 장치, 사용자 세션, 사용자 컨텍스트, 작업 
유추 |IP 주소, 타임스탬프, OS, 브라우저에서 지리적 위치 유추
메트릭 | 메트릭 이름 및 값
이벤트 | 이벤트 이름 및 값
PageViews | URL 및 페이지 이름이나 화면 이름
클라이언트 성능 | URL/페이지 이름, 브라우저 로드 시간
요청 |URL, 기간, 응답 코드
종속성|유형(SQL, HTTP,...), 연결 문자열 또는 URI, 동기/비동기, 기간, 성공, SQL 문(상태 모니터 사용)
**예외** | 유형, **메시지**, 호출 스택, 원본 파일 및 줄 번호, 스레드 ID
충돌 | 프로세스 ID, 부모 프로세스 ID, 충돌 스레드 ID; 응용 프로그램 패치, ID, 빌드; 예외 유형, 주소, 이유; 난독 처리된 기호 및 레지스터, 이진 시작 및 끝 주소, 이진 이름 및 경로, CPU 종류
추적 | **메시지** 및 심각도 수준
성능 카운터 | 프로세서 시간, 사용 가능한 메모리, 요청 속도, 예외 속도, 프로세스 전용 바이트, IO 속도, 요청 기간, 요청 큐 길이
Availability | 웹 테스트 응답 코드, 각 테스트 단계의 기간
SDK 진단 | 추적 메시지 또는 예외 

[ApplicationInsights.config를 편집하여 일부 데이터를 해제][config]할 수 있습니다.


## 제작진

이 제품에는 MaxMind에서 작성된 GeoLite2 데이터를 포함하며 [http://www.maxmind.com](http://www.maxmind.com)에 있습니다.

## <a name="video"></a>동영상

#### 소개

> [AZURE.VIDEO application-insights-introduction]

#### 시작

> [AZURE.VIDEO getting-started-with-application-insights]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=58_postMigration-->