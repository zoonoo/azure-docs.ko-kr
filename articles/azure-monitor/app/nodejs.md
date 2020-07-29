---
title: Azure Application Insights를 사용하여 Node.js 서비스 모니터링 | Microsoft Docs
description: Application Insights를 사용하여 Node.js 서비스의 성능을 모니터링하고 문제를 진단합니다.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: c6a1a030829f128c4369e99efcd56a416390afc6
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371620"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Application Insights를 사용하여 Node.js 서비스 및 앱 모니터링

[Application Insights](./app-insights-overview.md) 는 배포 후에 백 엔드 서비스 및 구성 요소를 모니터링 하 여 성능 및 기타 문제를 검색 하 고 신속 하 게 진단할 수 있도록 합니다. 데이터 센터, Azure Vm 및 웹 앱에서 호스트 되는 Node.js 서비스와 다른 공용 클라우드에서도 Application Insights를 사용할 수 있습니다.

모니터링 데이터를 수신, 저장 및 탐색하려면 코드에 SDK를 포함하고 Azure에서 해당 Application Insights 리소스를 설정합니다. SDK는 추가 분석 및 탐색을 위해 해당 리소스로 데이터를 보냅니다.

Node.js SDK는 들어오고 나가는 HTTP 요청, 예외 및 여러 시스템 메트릭을 자동으로 모니터링할 수 있습니다. 버전 0.20부터 SDK는 MongoDB, MySQL, Redis 등의 몇 가지 일반적인 [타사 패키지](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)를 모니터링할 수도 있습니다. 들어오는 HTTP 요청과 관련된 모든 이벤트는 좀 더 빠른 문제 해결을 위해 상호 관계가 지정됩니다.

TelemetryClient API를 사용하여 앱 및 시스템의 추가적인 여러 측면을 수동으로 계측하고 모니터링 할 수 있습니다. TelemetryClient API는 이 문서의 뒷부분에 더 자세히 설명합니다.

## <a name="get-started"></a>시작하기

앱 또는 서비스에 대한 모니터링을 설정하려면 다음 작업을 완료합니다.

### <a name="prerequisites"></a>전제 조건

시작하기 전에 Azure 구독이 있는지 확인하여 없는 경우 [무료 계정을 새로 만듭니다][azure-free-offer]. 조직에 이미 Azure 구독이 있으면 관리자가 [다음 지침][add-aad-user]에 따라 사용자를 구독에 추가할 수 있습니다.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a>Application Insights 리소스 설정

1. [Azure Portal][portal]에 로그인합니다.
2. [Application Insights 리소스 만들기](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Node.js SDK 설정

데이터를 수집할 수 있도록 앱에 SDK를 포함합니다.

1. 새로 만든 리소스에서 리소스의 계측 키 ( *ikey*라고도 함)를 복사 합니다. Application Insights는 ikey를 사용하여 Azure 리소스에 데이터를 매핑합니다. SDK가 ikey를 사용하려면 먼저 환경 변수 또는 코드에 ikey를 지정해야 합니다.  

   ![계측 키 복사](./media/nodejs/instrumentation-key-001.png)

2. package.json을 통해 Node.js SDK 라이브러리를 앱의 종속성에 추가합니다. 앱의 루트 폴더에서 다음을 실행합니다.

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > TypeScript를 사용 하는 경우 별도의 "유형" 패키지를 설치 하지 마십시오. 이 NPM 패키지에는 기본 제공 입력 항목이 포함되어 있습니다.

3. 코드에서 라이브러리를 명시적으로 로드합니다. SDK는 여러 다른 라이브러리에 계측 값을 삽입합니다. 따라서 최대한 신속하게, 심지어 다른 `require` 문보다도 먼저 라이브러리를 로드합니다.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  `APPINSIGHTS_INSTRUMENTATIONKEY`또는에 수동으로 전달 하는 대신 환경 변수를 통해 ikey를 제공할 수도 있습니다 `setup()` `new appInsights.TelemetryClient()` . 이렇게 하면 ikey가 커밋된 소스 코드의 영향을 받지 않으며 다른 환경에 다른 ikey를 지정할 수 있습니다. 수동으로를 호출 하려면를 설정 `appInsights.setup('[your ikey]');` 합니다.

    추가 구성 옵션의 경우 다음 섹션을 참조하세요.

    `appInsights.defaultClient.config.disableAppInsights = true`를 설정하면 원격 분석을 전송하지 않고 SDK를 사용해 볼 수 있습니다.

5. 을 호출 하 여 데이터를 자동으로 수집 하 고 전송 하기 시작 `appInsights.start();` 합니다.

### <a name="monitor-your-app"></a><a name="monitor"></a>앱 모니터링

SDK는 Node.js 런타임 및 일부 일반적인 타사 모듈에 대 한 원격 분석을 자동으로 수집 합니다. 애플리케이션을 사용하여 이 데이터를 생성합니다.

그런 다음, [Azure Portal][portal]에서 이전에 만든 Application Insights 리소스로 이동합니다. **타임라인 개요**에서 먼저 몇 가지 데이터 요소를 찾습니다. 더 자세한 데이터를 보려면 차트에서 다른 구성 요소를 선택합니다.

앱에 대해 검색 된 토폴로지를 보려면 [응용 프로그램 맵을](app-map.md)사용할 수 있습니다.

#### <a name="no-data"></a>데이터 없음

SDK는 제출할 데이터를 일괄 처리하기 때문에 항목이 포털에 표시될 때까지 지연될 수 있습니다. 리소스에 데이터가 보이지 않으면 다음 해결 방법 중 몇 가지를 시도해 보세요.

* 애플리케이션을 계속 사용합니다. 더 많은 작업을 수행하여 더 많은 원격 분석을 생성합니다.
* 포털 리소스 보기에서 **새로 고침**을 클릭합니다. 차트는 자체에서 주기적으로 새로 고치지만, 수동으로 새로 고침하면 즉시 새로 고쳐집니다.
* [필요한 발신 포트](./ip-addresses.md)가 열려 있는지 확인합니다.
* [검색](./diagnostic-search.md)을 사용하여 특정 이벤트를 찾습니다.
* [FAQ][FAQ]를 확인 합니다.

## <a name="basic-usage"></a>기본 사용

HTTP 요청, 인기 있는 타사 라이브러리 이벤트, 처리 되지 않은 예외 및 시스템 메트릭에 대 한 기본 컬렉션은 다음과 같습니다.

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> 환경 변수에 계측 키를 설정 하는 경우 `APPINSIGHTS_INSTRUMENTATIONKEY` `.setup()` 인수 없이를 호출할 수 있습니다. 따라서 환경 마다 다른 ikey를 쉽게 사용할 수 있습니다.

`require("applicationinsights")`다른 패키지를 로드 하기 전에 스크립트에서 가능한 한 빨리 Application Insights 라이브러리를 로드 합니다. 이는 Application Insights 라이브러리가 추적을 위해 나중에 패키지를 준비할 수 있도록 하기 위해 필요 합니다. 비슷한 준비를 수행 하는 다른 라이브러리와 충돌이 발생 하는 경우에는 Application Insights 라이브러리를 로드 해 보십시오.

JavaScript가 콜백을 처리 하는 방식 때문에 외부 종속성 및 이후 콜백에서 요청을 추적 하려면 추가 작업이 필요 합니다. 기본적으로이 추가 추적을 사용할 수 있습니다. `setAutoDependencyCorrelation(false)`아래 [구성](#sdk-configuration) 섹션에 설명 된 대로를 호출 하 여 사용 하지 않도록 설정 합니다.

## <a name="migrating-from-versions-prior-to-022"></a>0.22 이전 버전에서 마이그레이션

버전 0.22 이전의 릴리스 사이에는 주요 변경 내용이 있습니다. 이러한 변경 내용은 다른 Application Insights Sdk와 일관성을 유지 하 고 향후 확장성을 허용 하도록 설계 되었습니다.

일반적으로 다음과 같이 마이그레이션할 수 있습니다.

- 참조를로 `appInsights.client` 바꿉니다 `appInsights.defaultClient` .
- 참조 `appInsights.getClient()` 를로 바꾸기`new appInsights.TelemetryClient()`
- 인수를 명명 된 속성을 포함 하는 단일 개체를 사용 하 여 모든 인수를 client. track * 메서드로 바꿉니다. 각 원격 분석 형식에 대 한 예외 개체에 대 한 IDE의 기본 제공 형식 힌트 또는 [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) 를 참조 하세요.

에 연결 하지 않고 SDK 구성 기능에 액세스 하는 경우 `appInsights.setup()` 이제에서 이러한 함수를 찾을 수 있습니다 `appInsights.Configurations` (예: `appInsights.Configuration.setAutoCollectDependencies(true)` ). 다음 섹션의 기본 구성에 대 한 변경 내용을 검토 합니다.

## <a name="sdk-configuration"></a>SDK 구성

`appInsights`개체는 다양 한 구성 방법을 제공 합니다. 이러한 값은 다음 코드 조각에 기본값을 사용 하 여 나열 됩니다.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

서비스에서 이벤트의 상관 관계를 완전하게 지정하려면 `.setAutoDependencyCorrelation(true)`을 설정합니다. 이 옵션을 설정하면 SDK가 Node.js의 비동기 콜백에서 컨텍스트를 추적할 수 있습니다.

IDE의 기본 제공 형식 힌트 또는 [applicationinsights](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) 에서 해당 컨트롤에 대 한 자세한 내용 및 선택적 보조 인수에 대 한 설명을 검토 합니다.

> [!NOTE]
>  기본적으로 `setAutoCollectConsole` 는 *exclude* `console.log` (및 기타 콘솔 메서드)에 대 한 호출을 제외 하도록 구성 됩니다. 지원 되는 타사로 거 (예: winston 및 bunyan)에 대 한 호출만 수집 됩니다. 을 사용 하 여 메서드에 대 한 호출을 포함 하도록이 동작을 변경할 수 있습니다 `console` `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>샘플링

기본적으로 SDK는 수집 된 모든 데이터를 Application Insights 서비스로 보냅니다. 많은 데이터를 수집 하는 경우에는 샘플링을 사용 하 여 전송 되는 데이터의 양을 줄일 수 있습니다. `samplingPercentage`클라이언트의 개체에 대 한 필드를 설정 `config` 하 여이를 수행 합니다. `samplingPercentage`를 100 (기본값)으로 설정 하면 모든 데이터가 전송 되 고 0은 아무 것도 전송 되지 않습니다.

자동 상관 관계를 사용 하는 경우 단일 요청과 연결 된 모든 데이터가 하나의 단위로 포함 되거나 제외 됩니다.

샘플링을 사용 하도록 설정 하는 다음과 같은 코드를 추가 합니다.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>다중 구성 요소 응용 프로그램에 대 한 여러 역할

응용 프로그램이 동일한 계측 키를 사용 하 여 계측 하려는 여러 구성 요소로 구성 되어 있으며, 별도의 계측 키를 사용 하는 것 처럼 포털에서 이러한 구성 요소를 별도의 단위로 표시 하는 경우 (예: 응용 프로그램 맵의 개별 노드로) RoleName 필드를 수동으로 구성 하 여 데이터를 Application Insights 리소스로 전송 하는 다른 구성 요소와 동일한 구성 요소의 원격 분석을 구분 해야 합니다.

다음을 사용 하 여 RoleName 필드를 설정 합니다.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>자동 타사 계측

비동기 호출에서 컨텍스트를 추적 하려면 MongoDB 및 Redis와 같은 타사 라이브러리에 몇 가지 변경이 필요 합니다. 기본적으로 Application Insights는를 사용 [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) 하 여 이러한 라이브러리 중 일부를 원숭이 패치 합니다. 환경 변수를 설정 하 여이 기능을 사용 하지 않도록 설정할 수 있습니다 `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` .

> [!NOTE]
> 해당 환경 변수를 설정 하 여 이벤트가 더 이상 올바른 작업에 연결 되지 않을 수 있습니다.

 개별 원숭이- `APPLICATION_INSIGHTS_NO_PATCH_MODULES` 환경 변수를 비활성화할 패키지 (예:)를 쉼표로 구분 된 패키지 목록으로 설정 하 여 패치를 비활성화할 수 있습니다 `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` `console` `redis` .

현재는,,,,,,, `bunyan` `console` `mongodb` `mongodb-core` `mysql` `redis` `winston` `pg` 및와 `pg-pool` 같은 9 개의 패키지가 있습니다. 패치가 적용 되는 패키지의 버전에 대 한 자세한 내용은 [진단-채널-게시자의 추가](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) 정보를 참조 하세요.

`bunyan`, `winston` 및 패치는 `console` 가 사용 되는지 여부에 따라 Application Insights 추적 이벤트를 생성 합니다 `setAutoCollectConsole` . Rest는가 사용 되는지 여부에 따라 Application Insights 종속성 이벤트를 생성 합니다 `setAutoCollectDependencies` .

### <a name="live-metrics"></a>라이브 메트릭

앱에서 Azure로 라이브 메트릭을 전송 하려면를 사용 `setSendLiveMetrics(true)` 합니다. 포털에서 라이브 메트릭의 필터링은 현재 지원 되지 않습니다.

### <a name="extended-metrics"></a>확장 메트릭

> [!NOTE]
> 확장 된 네이티브 메트릭을 전송 하는 기능이 버전 1.4.0에 추가 되었습니다.

앱에서 Azure로 확장 된 네이티브 메트릭을 보낼 수 있도록 하려면 별도의 기본 메트릭 패키지를 설치 합니다. SDK가 설치 되 면 자동으로 로드 되 고 Node.js 기본 메트릭 수집을 시작 합니다.

```bash
npm install applicationinsights-native-metrics
```

현재 기본 메트릭 패키지는 가비지 수집 CPU 시간, 이벤트 루프 틱 및 힙 사용의 autocollection을 수행 합니다.

- **가비지 수집**: 각 가비지 수집 형식에 소요 된 CPU 시간과 각 형식의 발생 횟수입니다.
- **이벤트 루프**: 발생 한 틱 수 및 총 CPU 시간 (%)
- 힙 **및 비 힙**: 응용 프로그램의 메모리 사용량은 힙 또는 비 힙에 있습니다.

### <a name="distributed-tracing-modes"></a>분산 추적 모드

기본적으로 SDK는 Application Insights SDK를 사용 하 여 계측 된 다른 응용 프로그램/서비스에서 인식 하는 헤더를 전송 합니다. 필요에 따라 기존 AI 헤더 외에 [W3C 추적 컨텍스트](https://github.com/w3c/trace-context) 헤더를 보내고 받을 수 있으므로 기존 레거시 서비스와의 상관 관계를 중단할 수 없습니다. W3C 헤더를 사용 하도록 설정 하면 앱이 Application Insights를 사용 하 여 계측 되지 않은 다른 서비스와의 상관 관계를 지정할 수 있지만이 W3C 표준은 채택 합니다.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient API

TelemetryClient API에 대한 전체 설명은 [사용자 지정 이벤트 및 메트릭용 Application Insights API](./api-custom-events-metrics.md)를 참조하세요.

Application Insights Node.js SDK를 사용하여 모든 요청, 이벤트, 메트릭 또는 예외를 추적할 수 있습니다. 다음 코드 예제에는 사용할 수 있는 몇 가지 API가 나와 있습니다.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>종속성 추적

다음 코드를 사용하여 종속성을 추적합니다.

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

`trackMetric`이벤트 루프 예약 시간을 측정 하는 데 사용 하는 예제 유틸리티는 다음과 같습니다.  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>모든 이벤트에 사용자 지정 속성 추가

다음 코드를 사용하여 모든 이벤트에 사용자 지정 속성을 추가합니다.

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET 요청 추적

다음 코드를 사용 하 여 HTTP GET 요청을 수동으로 추적 합니다.

> [!NOTE]
> 모든 요청은 기본적으로 추적 됩니다. 자동 수집을 사용 하지 않도록 설정 하려면 start ()를 호출 하기 전에 setAutoCollectRequests (false)를 호출 합니다.

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

또는 메서드를 사용 하 여 요청을 추적할 수 있습니다 `trackNodeHttpRequest` .

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>서버 시작 시간 추적

다음 코드를 사용하여 서버 시작 시간을 추적합니다.

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="preprocess-data-with-telemetry-processors"></a>원격 분석 프로세서를 사용 하 여 데이터 전처리

수집 된 데이터는 *원격 분석 프로세서*를 사용 하 여 보존을 위해 전송 되기 전에 처리 하 고 필터링 할 수 있습니다. 원격 분석 항목을 클라우드로 보내기 전에 추가 된 순서 대로 원격 분석 프로세서를 하나씩 호출 합니다.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

원격 분석 프로세서가 false를 반환 하는 경우 해당 원격 분석 항목이 전송 되지 않습니다.

모든 원격 분석 프로세서는 검사 하 고 수정할 수 있도록 원격 분석 데이터 및 봉투 (envelope)를 수신 합니다. 또한 컨텍스트 개체를 수신 합니다. 이 개체의 내용은 `contextObjects` 수동으로 추적 된 원격 분석을 위해 track 메서드를 호출할 때 매개 변수에 의해 정의 됩니다. 자동으로 수집 된 원격 분석의 경우이 개체는에서 제공 하는 사용 가능한 요청 정보 및 영구 요청 내용으로 채워집니다 `appInsights.getCorrelationContext()` (자동 종속성 상관 관계를 사용 하는 경우).

원격 분석 프로세서의 TypeScript 형식은 다음과 같습니다.

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

예를 들어 예외에서 스택 추적 데이터를 제거 하는 프로세서를 작성 하 고 다음과 같이 추가할 수 있습니다.

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>여러 계측 키 사용

여러 Application Insights 리소스를 만들고 각각의 계측 키 ("ikey")를 사용 하 여 서로 다른 데이터를 보낼 수 있습니다.

 예를 들면 다음과 같습니다.

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>고급 구성 옵션

클라이언트 개체에는 `config` 고급 시나리오에 대 한 다양 한 옵션 설정이 포함 된 속성이 있습니다. 이러한 작업은 다음과 같이 설정할 수 있습니다.

```javascript
client.config.PROPERTYNAME = VALUE;
```

이러한 속성은 클라이언트 마다 고유 하므로 `appInsights.defaultClient` 를 사용 하 여 만든 클라이언트와 별도로 구성할 수 있습니다 `new appInsights.TelemetryClient()` .

| 속성                        | 설명                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Application Insights 리소스의 식별자입니다.                                                      |
| endpointUrl                     | 원격 분석 페이로드를 보낼 수집 끝점입니다.                                                      |
| quickPulseHost                  | 라이브 메트릭 원격 분석을 보낼 라이브 메트릭 스트림 호스트입니다.                                            |
| proxyHttpUrl                    | SDK HTTP 트래픽에 대 한 프록시 서버 (선택 사항, 기본값은 `http_proxy` 환경 변수에서 끌어옵니다.)     |
| proxyHttpsUrl                   | SDK HTTPS 트래픽에 대 한 프록시 서버 (선택 사항, 기본값은 `https_proxy` 환경 변수에서 끌어옵니다.)   |
| httpAgent                       | Http입니다. SDK HTTP 트래픽에 사용할 에이전트 (선택 사항, 기본값은 정의 되지 않음)입니다.                                   |
| httpsAgent                      | Https. SDK HTTPS 트래픽에 사용할 에이전트 (선택 사항, 기본값은 정의 되지 않음)입니다.                                 |
| maxBatchSize                    | 수집 끝점에 대 한 페이로드에 포함 될 원격 분석 항목의 최대 수 (기본값 `250` )입니다.   |
| maxBatchIntervalMs              | 페이로드가 maxBatchSize에 도달할 때까지 대기 하는 최대 시간 (기본값 `15000` )입니다.               |
| disableAppInsights              | 원격 분석 전송을 사용 하지 않도록 설정 (기본값) 하는지 여부를 나타내는 플래그 `false` 입니다.                                 |
| samplingPercentage              | 전송 되어야 하는 원격 분석 항목의 비율 (기본값 `100` )입니다.                      |
| correlationIdRetryIntervalMs    | 구성 요소 간 상관 관계에 대 한 ID 검색을 다시 시도 하기 전까지 대기 하는 시간입니다 (기본값 `30000` ).     |
| correlationHeaderExcludedDomains| 구성 요소 간 상관 관계 헤더 삽입에서 제외할 도메인 목록입니다 (기본값은 [Config.xml](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)참조).|

## <a name="next-steps"></a>다음 단계

* [포털에서 원격 분석 모니터링](./overview-dashboard.md)
* [원격 분석에 분석 쿼리 작성](../log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md

