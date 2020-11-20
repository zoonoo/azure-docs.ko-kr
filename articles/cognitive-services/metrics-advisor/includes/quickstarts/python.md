---
title: Metrics Advisor Python 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2c79773d6697ae9fb62e2b7515da60178243fe40
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523753"
---
[참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [패키지(PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor 리소스를 생성"  target="_blank">Metrics Advisor 리소스를 생성<span class="docon docon-navigate-external x-hidden-focus"></span></a>하여 Metrics Advisor 인스턴스를 배포합니다.  
* 시계열 데이터가 포함된 사용자 고유의 SQL 데이터베이스.
  
> [!TIP]
> * [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples)에서 Python Metrics Advisor 샘플을 찾을 수 있습니다.
> * Metrics Advisor 리소스에서 사용할 서비스 인스턴스를 배포하는 데 10~30분 정도 걸릴 수 있습니다. 성공적으로 배포되면 **리소스로 이동** 을 클릭합니다. 배포 후에는 웹 포털과 REST API 모두를 사용하여 Metrics Advisor 인스턴스 사용을 시작할 수 있습니다.
> * 리소스의 **개요** 섹션에서 Azure Portal의 REST API에 대한 URL을 찾을 수 있습니다. 다음과 같이 표시됩니다.
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

```python
import os
import datetime
```

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Metrics Advisor 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. <br><br>API 키를 검색하려면 [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net)으로 이동해야 합니다. 적절한 항목을 선택합니다. 리소스에 대한 **디렉터리**, **구독** 및 **작업 영역** 을 선택하고 **시작** 을 선택합니다. 그러면 [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key)에서 API 키를 검색할 수 있습니다.   
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) 문서를 참조하세요.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>개체 모델

Metrics Advisor Python SDK의 주요 기능 중 일부를 처리하는 클래스는 다음과 같습니다.

|이름|설명|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **용도**: <br> - 인시던트 나열 <br> - 인시던트의 근본 원인 나열 <br> - 서비스에 의해 보강된 원래 시계열 데이터와 시계열 데이터를 검색합니다. <br> - 경고 나열 <br> - 모델 조정을 위한 피드백 추가 |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **다음을 수행할 수 있습니다.** <br> - 데이터 피드 관리 <br> - 변칙 검색 구성 생성, 구성, 검색, 나열 및 삭제 <br> - 변칙 경고 구성 생성, 구성, 검색, 나열 및 삭제 <br> - 후크 관리  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Metrics Advisor가 데이터 원본에서 수집하는 항목. `DataFeed`에는**  행이 포함됩니다. <br> - 타임스탬프 <br> - 0개 이상의 차원 <br> - 하나 이상의 측정값  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | `DataFeedMetric`은 특정 비즈니스 프로세스의 상태를 모니터링하고 평가하는 데 사용되는 정량 측정값입니다. 차원으로 나눈 여러 개의 시계열 값의 조합일 수 있습니다. 예를 들어 웹 상태 메트릭은 사용자 수 및 en-us 시장의 차원을 포함할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 Python용 Metrics Advisor 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [데이터 피드 추가](#add-a-data-feed)
* [수집 상태 확인](#check-the-ingestion-status)
* [검색 구성 및 경고 구성 설정](#configure-anomaly-detection)
* [경고 구성 만들기](#create-an-alert-configuration)
* [변칙 검색 결과 쿼리](#query-the-alert)

## <a name="authenticate-the-client"></a>클라이언트 인증

이 예제의 클라이언트는 엔드포인트를 사용하는 `MetricsAdvisorAdministrationClient` 개체와 키를 포함하는 `MetricsAdvisorKeyCredential` 개체입니다. 이 코드 샘플은 복사할 필요가 없습니다. 나중에 만드는 메서드는 클라이언트를 인스턴스화합니다. 대체 클라이언트를 `MetricsAdvisorClient`라고 하며 이 클라이언트에 대한 자세한 내용은 [참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient)에서 확인할 수 있습니다.

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>데이터 피드 추가

새 메서드에서 아래 예제와 같이 import 문을 만듭니다. `sql_server_connection_string`을 사용자 고유의 SQL 서버 연결 문자열로 바꾸고 `query`를 단일 타임스탬프에서 데이터를 반환하는 쿼리로 바꿉니다. 사용자 지정 데이터를 기반으로 `DataFeedmetric` 및 `DataFeedDimension` 값도 조정해야 합니다.

> [!IMPORTANT]
> 각 타임스탬프에서 쿼리는 각 차원 조합에 대해 최대 하나의 레코드를 반환해야 합니다. 그리고 쿼리에서 반환된 모든 레코드의 타임스탬프는 동일해야 합니다. 메트릭 관리자는 각 타임스탬프에 대해 이 쿼리를 실행하여 데이터를 수집합니다. 자세한 정보와 예는 [쿼리에 대한 FAQ 섹션](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)을 참조하세요. 

키와 엔드포인트를 사용하여 클라이언트를 만들고 `client.create_data_feed()`를 사용하여 이름, 원본, 세분성 및 스키마를 구성합니다. 수집 시간, 롤업 설정 등을 설정할 수도 있습니다.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>수집 상태 확인

새 메서드에서 아래 예제와 같이 import 문을 만듭니다. `data_feed_id`를 사용자가 만든 데이터 피드의 ID로 바꿉니다. 키와 엔드포인트를 사용하여 클라이언트를 만들고 `client.list_data_feed_ingestion_status()`를 사용하여 수집 진행률을 가져옵니다. 마지막 활성 및 성공한 타임스탬프와 같은 세부 정보를 출력합니다.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>변칙 검색 구성

새 메서드에서 아래 예제와 같이 import 문을 만듭니다. `metric_id`를 구성하려는 메트릭의 ID로 바꿉니다. 키 및 엔드포인트를 사용하여 클라이언트를 만들고 `client.create_detection_configuration`을 사용하여 새 검색 구성을 만듭니다. 임계값 조건은 변칙 검색에 대한 매개 변수를 지정합니다.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>후크 만들기

새 메서드에서 아래 예제와 같이 import 문을 만듭니다. 키와 엔드포인트를 사용하여 클라이언트를 만들고 `client.create_hook()`를 사용하여 후크를 만듭니다. 설명, 경고를 보낼 이메일 목록 및 경고를 받기 위한 외부 링크를 입력합니다.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>경고 구성 만들기

새 메서드에서 아래 예제와 같이 import 문을 만듭니다. `detection_configuration_id`를 변칙 검색 구성의 ID로 바꾸고 `hook_id`를 이전에 만든 후크로 바꿉니다. 키와 엔드포인트를 사용하여 클라이언트를 만들고 `client.create_alert_configuration()`을 사용하여 경고 구성을 만듭니다. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>경고 쿼리

새 메서드에서 아래 예제와 같이 import 문을 만듭니다. `alert_id`를 경고 ID로 바꾸고 `alert_config_id`를 경고 구성 ID로 바꿉니다. 키와 엔드포인트를 사용하여 클라이언트를 만들고 `client.list_anomalies`를 사용하여 경고에 대한 변칙을 나열합니다. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `python` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```