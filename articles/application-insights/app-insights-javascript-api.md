<properties 
	pageTitle="Application Insights SDK JavaScript API" 
	description="참조 문서" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# Application Insights SDK JavaScript API

[Application Insights](https://azure.microsoft.com/services/application-insights/)에서 [웹 페이지 추적](app-insights-javascript.md)을 설정할 때 JavaScript SDK가 웹 페이지로 로드됩니다.

[API 개요 및 예제](app-insights-api-custom-events-metrics.md)

## AppInsights 클래스

원격 분석을 Application Insights에 전송하는 SDK의 가장 바깥쪽 부분입니다.

[웹 페이지 추적](app-insights-javascript.md)을 설정한 웹 페이지에서 `appInsights` 인스턴스를 사용할 수 있습니다. 예:
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

페이지 또는 유사한 컨테이너가 사용자에게 표시된 기록입니다.

 | | 
---|---|---
`name` | `? string` | 포털에서 페이지를 식별하는 데 사용되는 이름입니다. 기본 문서 제목입니다.
`url` | `? string` | 페이지 또는 유사한 항목을 식별하는 상대 또는 절대 URL입니다. 기본 창 위치입니다.
`properties` | `? {[string]:string}` | 포털에서 페이지 및 메트릭을 필터링하는 데 사용되는 추가 데이터입니다. 기본적으로 비어 있습니다.
`measurements` | `? {[string]:number}` | 이 페이지와 연결된 메트릭으로, 포털의 메트릭 탐색기에 표시됩니다. 기본적으로 비어 있습니다.


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

사용자 작업 또는 기타 항목을 기록합니다.

포털에서 이름별로 이벤트를 선택한 후 [계산하거나 연결된 측정값을 표시하는 차트를 표시](app-insights-metrics-explorer.md)할 수 있습니다.

또한 [개별 이벤트를 검색 및 표시](app-insights-diagnostic-search.md)할 수도 있습니다.

 | | 
---|---|---
 `name` | `string` | 이벤트를 식별합니다. 이름이 같은 이벤트를 계산하여 [메트릭 탐색기](app-insights-metrics-explorer.md)에 차트로 표시할 수 있습니다.
`properties` | `? {[string]:string}` | 포털에서 페이지 및 메트릭을 필터링하는 데 사용되는 추가 데이터입니다. 기본적으로 비어 있습니다.
`measurements` | `? {[string]:number}` | 이 페이지와 연결된 메트릭으로, 포털의 메트릭 탐색기에 표시됩니다. 기본적으로 비어 있습니다.


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


특정 이벤트와 연결되지 않은 숫자 값을 기록합니다. 일반적으로 성과 지표에 대한 정기 보고서를 보내는 데 사용됩니다.

포털에서 이름별로 메트릭을 선택하여 [시간 경과에 따라 값을 차트로 표시](app-insights-metrics-explorer.md)할 수 있습니다. 개별 trackMetric 호출을 검색하거나 볼 수는 없습니다.

단일 측정값을 보내려면 처음 두 매개 변수를 사용하면 됩니다. 자주 측정하는 경우는 여러 측정값을 집계하여 정해진 간격으로 결과 평균값을 보내면 원격 분석 대역폭을 줄일 수 있습니다.

 | | 
---|---|---
`name` | `string` | 메트릭을 식별하는 문자열입니다. 포털에서 이름별로 표시할 메트릭을 선택할 수 있습니다.
`average` | ` number` | 단일 측정값 또는 여러 측정값의 평균을 입력합니다.
`sampleCount` | `? number` | 평균으로 표시된 측정값의 수입니다. 기본값은 1입니다.
`min` | `? number` | 샘플에서 가장 작은 측정값입니다. 기본값은 평균입니다.
`max` | `? number` | 샘플에서 가장 큰 측정값입니다. 기본값은 평균입니다.

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

발생한 예외를 기록합니다. (브라우저에 의해 발생한 예외도 기록됩니다.)

포털에서 [예외 유형을 검색](app-insights-diagnostic-search.md)하고 개별 인스턴스의 유형, 메시지 및 스택 추적을 볼 수 있습니다.

 | | 
---|---|---
`exception` | `Error` | catch 절에서 발생한 오류입니다.  
`handledAt` | `? string` | 기본값은 "처리되지 않음"입니다.
`properties` | `? {[string]:string}` | 포털에서 페이지 및 메트릭을 필터링하는 데 사용되는 추가 데이터입니다. 기본적으로 비어 있습니다.
`measurements` | `? {[string]:number}` | 이 페이지와 연결된 메트릭으로, 포털의 메트릭 탐색기에 표시됩니다. 기본적으로 비어 있습니다.

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

메서드를 출입 같은 진단 이벤트를 기록합니다.

포털에서 메시지 내용을 검색하고 [개별 trackTrace 이벤트를 표시](app-insights-diagnostic-search.md)할 수 있습니다. (`trackEvent`와 달리, 포털에서 메시지 내용을 필터링할 수 없습니다.)

 | | 
---|---|---
`message` | `string` | 진단 데이터. 이름보다 훨씬 길어질 수 있습니다.

### flush

    flush()

대기 중인 모든 원격 분석 데이터를 즉시 전송합니다.

창을 닫을 때 사용합니다.


### config

    config: IConfig

원격 분석 데이터를 전송하는 방법을 제어하는 값입니다.

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

웹 페이지에 삽입하는 [코드 조각](app-insights-javascript-api.md)에서 이러한 값을 설정합니다. 다음 행을 찾아서 항목을 더 추가합니다.

    })({
      instrumentationKey: "000...000"
    });

### context

    context: TelemetryContext

SDK가 장치, 위치 및 사용자에 대해 환경에서 추출하려고 하는 정보입니다.


## TelemetryContext 클래스




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## 공개 소스 코드

[SDK용 코드](https://github.com/Microsoft/ApplicationInsights-js)를 읽거나 참여합니다.

<!---HONumber=August15_HO6-->