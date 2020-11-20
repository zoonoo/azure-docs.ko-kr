---
title: Metrics Advisor JavaScript 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 121f6a876507b84de7a4613fe2158c1953e2d250
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523875"
---
[참조 설명서](https://docs.microsoft.com/javascript/api/overview/azure/ai-metrics-advisor-readme-pre?view=azure-node-preview&preserve-view=true) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md) | [패키지(npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 현재 버전의 [Node.js](https://nodejs.org/)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor 리소스를 생성"  target="_blank">Metrics Advisor 리소스를 생성<span class="docon docon-navigate-external x-hidden-focus"></span></a>하여 Metrics Advisor 인스턴스를 배포합니다.  
* 시계열 데이터가 포함된 사용자 고유의 SQL 데이터베이스.
  
> [!TIP]
> * [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)에서 JavaScript Metrics Advisor 샘플을 찾을 수 있습니다.
> * Metrics Advisor 리소스에서 사용할 서비스 인스턴스를 배포하는 데 10~30분 정도 걸릴 수 있습니다. 성공적으로 배포되면 **리소스로 이동** 을 클릭합니다. 배포 후에는 웹 포털과 REST API 모두를 사용하여 Metrics Advisor 인스턴스 사용을 시작할 수 있습니다. 
> * 리소스의 **개요** 섹션에서 Azure Portal의 REST API에 대한 URL을 찾을 수 있습니다. 다음과 같이 표시됩니다.
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`@azure/ai-metrics-advisor` NPM 패키지를 설치합니다.

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

파일 `index.js`를 만들고 다음 라이브러리를 가져옵니다.

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Metrics Advisor 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. <br><br>API 키를 검색하려면 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)으로 이동해야 합니다. 적절한 항목을 선택합니다. 리소스에 대한 **디렉터리**, **구독** 및 **작업 영역** 을 선택하고 **시작** 을 선택합니다. 그러면 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key)에서 API 키를 검색할 수 있습니다.   
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) 문서를 참조하세요.

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>개체 모델

Metrics Advisor JavaScript SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|이름|설명|
|---|---|
| MetricsAdvisorClient | **용도**: <br> - 인시던트 나열 <br> - 인시던트의 근본 원인 나열 <br> - 서비스에 의해 보강된 원래 시계열 데이터와 시계열 데이터를 검색합니다. <br> - 경고 나열 <br> - 모델 조정을 위한 피드백 추가 |
| MetricsAdvisorAdministrationClient | **다음을 수행할 수 있습니다.** <br> - 데이터 피드 관리 <br> - 변칙 경고 구성 생성, 구성, 검색, 나열 및 삭제 <br> - 후크 관리  |
| DataFeed | **Metrics Advisor가 데이터 원본에서 수집하는 항목. `DataFeed`에는**  행이 포함됩니다. <br> - 타임스탬프 <br> - 0개 이상의 차원 <br> - 하나 이상의 측정값  |
| DataFeedMetric | `DataFeedMetric`은 특정 비즈니스 프로세스의 상태를 모니터링하고 평가하는 데 사용되는 정량 측정값입니다. 차원으로 나눈 여러 개의 시계열 값의 조합일 수 있습니다. 예를 들어 웹 상태 메트릭은 사용자 수 및 en-us 시장의 차원을 포함할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 JavaScript용 Metrics Advisor 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [데이터 피드 추가](#add-a-data-feed)
* [수집 상태 확인](#check-ingestion-status)
* [변칙 검색 구성](#configure-anomaly-detection)
* [후크 만들기](#create-a-hook)
* [경고 구성 만들기](#create-an-alert-configuration)
* [경고 쿼리](#query-the-alert)

## <a name="authenticate-the-client"></a>클라이언트 인증

두 개의 키와 엔드포인트 주소가 있으면 다음과 같이 MetricsAdvisorKeyCredential 클래스를 사용하여 클라이언트를 인증할 수 있습니다.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>데이터 피드 추가

Metrics Advisor는 다양한 유형의 데이터 원본 연결을 지원합니다. SQL Server에서 데이터를 수집하는 샘플은 다음과 같습니다.

`sql_server_connection_string`을 사용자 고유의 SQL 서버 연결 문자열로 바꾸고 `query`를 단일 타임스탬프에서 데이터를 반환하는 쿼리로 바꿉니다. 사용자 지정 데이터를 기반으로 `metric` 및 `dimension` 값도 조정해야 합니다.

> [!IMPORTANT]
> 각 타임스탬프에서 쿼리는 각 차원 조합에 대해 최대 하나의 레코드를 반환해야 합니다. 그리고 쿼리에서 반환된 모든 레코드의 타임스탬프는 동일해야 합니다. 메트릭 관리자는 각 타임스탬프에 대해 이 쿼리를 실행하여 데이터를 수집합니다. 자세한 정보와 예는 [쿼리에 대한 FAQ 섹션](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)을 참조하세요. 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>수집 상태 확인

데이터 수집을 시작한 후 수집 상태를 확인할 수 있습니다.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>변칙 검색 구성

시계열의 지점이 비정상인지 여부를 확인하려면 변칙 검색 구성이 필요합니다. 기본 검색 구성이 각 메트릭에 자동으로 적용되지만 사용자 지정 변칙 검색 구성을 만들어 데이터에서 사용되는 검색 모드를 튜닝할 수 있습니다.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>후크 만들기

후크를 사용하여 실시간 경고를 구독합니다. 이 예제에서는 경고를 POST할 Metrics Advisor 서비스에 대한 웹후크를 만듭니다.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>경고 구성 만들기

이 샘플에서는 경고를 트리거해야 하는 조건을 구성하는 방법 및 경고의 대상으로 사용할 후크를 보여줍니다.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>경고 쿼리

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```