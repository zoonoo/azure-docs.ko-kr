---
title: '자습서: 코드를 한 줄도 쓰지 않고 Azure Data Explorer에서 진단 및 활동 로그 데이터 수집'
description: 이 자습서에서는 코드를 한 줄도 작성하지 않고 Azure Data Explorer에 데이터를 수집하고 해당 데이터를 쿼리하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/29/2019
ms.openlocfilehash: 187aa4b02e389c485b24ad7de256422d1880182b
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872596"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>자습서: 코드를 한 줄도 쓰지 않고 Azure Data Explorer에서 데이터 수집

이 자습서에서는 코드를 작성하지 않고 진단 및 활동 로그 데이터를 Azure Data Explorer 클러스터에 수집하는 방법을 설명합니다. 이 간단한 수집 메서드를 사용하면 데이터 분석을 위해 Azure Data Explorer에 대한 쿼리를 신속하게 시작할 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Data Explorer 데이터베이스에서 테이블 및 수집 매핑을 만듭니다.
> * 업데이트 정책을 사용하여 수집된 데이터의 형식을 지정합니다.
> * [이벤트 허브](/azure/event-hubs/event-hubs-about)를 만들어서 Azure Data Explorer에 연결합니다.
> * [Azure Monitor 진단 로그](/azure/azure-monitor/platform/diagnostic-logs-overview)와 [Azure Monitor 활동 로그](/azure/azure-monitor/platform/activity-logs-overview)에서 이벤트 허브로 데이터를 스트리밍합니다.
> * Azure Data Explorer를 사용하여 수집된 데이터를 쿼리합니다.

> [!NOTE]
> 모든 리소스를 동일한 Azure 위치나 지역에 만듭니다. 이것은 Azure Monitor 진단 로그에 대한 요구 사항입니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md). 이 자습서에서 데이터베이스 이름은 *TestDatabase*입니다.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Azure Monitor 데이터 공급자: 진단 및 활동 로그

아래 Azure Monitor 진단 및 활동 로그에서 제공된 데이터를 확인하고 이해합니다. 이러한 데이터 스키마를 기반으로 수집 파이프라인을 만듭니다. 로그의 각 이벤트에는 레코드 배열이 있습니다. 이 레코드 배열은 나중에 자습서에서 분할됩니다.

### <a name="diagnostic-logs-example"></a>진단 로그 예제

Azure 진단 로그는 Azure 서비스가 생성한 메트릭이며, 해당 서비스의 작업에 대한 데이터를 제공합니다. 데이터는 1분의 시간 단위로 집계됩니다. 다음은 쿼리 기간에 Azure Data Explorer 메트릭 이벤트 스키마의 예입니다.

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

### <a name="activity-logs-example"></a>활동 로그 예제

Azure 활동 로그는 구독에 있는 리소스에 대해 수행되는 작업에 대한 인사이트를 제공하는 구독 수준 로그입니다. 다음은 액세스 확인을 위한 활동 로그 이벤트의 예입니다.

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Azure Data Explorer에서 수집 파이프라인 설정

Azure Data Explorer 파이프라인을 설정하려면 [테이블 생성 및 데이터 수집](/azure/data-explorer/ingest-sample-data#ingest-data)과 같은 여러 단계가 필요합니다. 데이터를 조작, 매핑 및 업데이트할 수도 있습니다.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Azure Data Explorer 웹 UI에 연결

Azure Data Explorer *TestDatabase* 데이터베이스에서 **쿼리**를 선택하여 Azure Data Explorer 웹 UI를 엽니다.

![쿼리 페이지](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>대상 테이블 만들기

Azure Monitor 로그의 구조는 테이블 형식이 아닙니다. 데이터를 조작하고 각 이벤트를 하나 이상의 레코드로 확장합니다. 원시 데이터는 활동 로그의 경우 *ActivityLogsRawRecords*, 진단 로그의 경우 *DiagnosticLogsRawRecords*라는 중간 테이블로 수집됩니다. 이 때 데이터가 조작되고 확장됩니다. 그런 다음, 업데이트 정책을 사용하여, 확장된 데이터는 활동 로그의 *ActivityLogsRecords* 테이블 및 진단 로그의 *DiagnosticLogsRecords*로 수집됩니다. 따라서 활동 로그 수집용으로 두 개의 개별 테이블을 만들고 진단 로그 수집용으로 두 개의 개별 테이블을 만들어야 합니다.

Azure Data Explorer 웹 UI를 사용하여 Azure Data Explorer 데이터베이스에 대상 테이블을 만듭니다.

#### <a name="the-diagnostic-logs-table"></a>진단 로그 테이블

1. *TestDatabase* 데이터베이스에 진단 로그 레코드를 저장할 *DiagnosticLogsRecords*라는 테이블을 만듭니다. 다음과 같은 `.create table` 제어 명령을 사용합니다.

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. **실행**을 선택하여 테이블을 만듭니다.

    ![쿼리 실행](media/ingest-data-no-code/run-query.png)

1. 다음 쿼리를 사용하여 데이터 조작을 위해 *TestDatabase* 데이터베이스에 *DiagnosticLogsRawRecords*라는 중간 데이터 테이블을 만듭니다. **실행**을 선택하여 테이블을 만듭니다.

    ```kusto
    .create table DiagnosticLogsRawRecords (Records:dynamic)
    ```

#### <a name="the-activity-logs-tables"></a>활동 로그 테이블

1. 활동 로그 레코드를 받을 *TestDatabase* 데이터베이스에 *ActivityLogsRecords*라는 테이블을 만듭니다. 테이블을 만들려면 다음 Azure Data Explorer 쿼리를 실행합니다.

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. 데이터 조작을 위해 *TestDatabase* 데이터베이스에 *ActivityLogsRawRecords*라는 중간 데이터 테이블을 만듭니다.

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>테이블 매핑 만들기

 데이터 형식이 `json`이기 때문에 데이터 매핑이 필요합니다. `json` 매핑은 각 json 경로를 테이블 열 이름에 매핑합니다.

#### <a name="table-mapping-for-diagnostic-logs"></a>진단 로그에 대한 테이블 매핑

진단 로그 데이터를 테이블에 매핑하려면 다음 쿼리를 사용합니다.

```kusto
.create table DiagnosticLogsRawRecords ingestion json mapping 'DiagnosticLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>활동 로그에 대한 테이블 매핑

활동 로그 데이터를 테이블에 매핑하려면 다음 쿼리를 사용합니다.

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-log-data"></a>로그 데이터에 대한 업데이트 정책 만들기

#### <a name="activity-log-data-update-policy"></a>활동 로그 데이터 업데이트 정책

1. 컬렉션의 각 값이 별도의 행을 받도록 활동 로그 레코드 컬렉션을 확장하는 [함수](/azure/kusto/management/functions)를 만듭니다. [`mv-expand`](/azure/kusto/query/mvexpandoperator) 연산자를 사용합니다.

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. 대상 테이블에 [업데이트 정책](/azure/kusto/concepts/updatepolicy)을 추가합니다. 이 정책은 *ActivityLogsRawRecords* 중간 데이터 테이블의 새로 수집된 데이터에 대해 자동으로 쿼리를 실행하고 그 결과를 *ActivityLogsRecords* 테이블에 수집합니다.

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

#### <a name="diagnostic-log-data-update-policy"></a>진단 로그 데이터 업데이트 정책

1. 컬렉션의 각 값이 별도의 행을 받도록 진단 로그 레코드 컬렉션을 확장하는 [함수](/azure/kusto/management/functions)를 만듭니다. [`mv-expand`](/azure/kusto/query/mvexpandoperator) 연산자를 사용합니다.
     ```kusto
    .create function DiagnosticLogRecordsExpand() {
        DiagnosticLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            MetricName = tostring(events["metricName"]),
            Count = toint(events["count"]),
            Total = todouble(events["total"]),
            Minimum = todouble(events["minimum"]),
            Maximum = todouble(events["maximum"]),
            Average = todouble(events["average"]),
            TimeGrain = tostring(events["timeGrain"])
    }
    ```

2. 대상 테이블에 [업데이트 정책](/azure/kusto/concepts/updatepolicy)을 추가합니다. 이 정책은 *DiagnosticLogsRawRecords* 중간 데이터 테이블의 새로 수집된 데이터에 대해 자동으로 쿼리를 실행하고 그 결과를 *DiagnosticLogsRecords* 테이블에 수집합니다.

    ```kusto
    .alter table DiagnosticLogsRecords policy update @'[{"Source": "DiagnosticLogsRawRecords", "Query": "DiagnosticLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs 네임스페이스 만들기

Azure 진단 로그를 사용하면 스토리지 계정 또는 이벤트 허브에 메트릭을 내보낼 수 있습니다. 이 자습서에서는 이벤트 허브를 통해 메트릭을 라우팅합니다. 다음 단계에서는 진단 로그에 대해 Event Hubs 네임스페이스 및 이벤트 허브를 작성합니다. Azure Monitor는 활동 로그에 대해 이벤트 허브 *insights-operational-logs*를 만듭니다.

1. Azure Portal에서 Azure Resource Manager 템플릿을 사용하여 이벤트 허브를 만듭니다. 이 문서의 나머지 단계를 수행하려면, **Azure에 배포** 단추를 마우스 오른쪽 단추로 클릭하고 **새 창에서 열기**를 선택합니다. **Azure에 배포** 단추를 선택하면 Azure Portal로 이동합니다.

    [![Azure에 배포 단추](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Event Hubs 네임스페이스 및 진단 로그에 대한 이벤트 허브를 만듭니다.

    ![이벤트 허브 만들기](media/ingest-data-no-code/event-hub.png)

1. 다음 정보로 양식을 작성합니다. 다음 표에 나와 있지 않은 설정에는 기본값을 사용하세요.

    **설정** | **제안 값** | **설명**
    |---|---|---|
    | **구독** | *구독* | 이벤트 허브에 사용할 Azure 구독을 선택합니다.|
    | **리소스 그룹** | *test-resource-group* | 새 리소스 그룹을 만듭니다. |
    | **위치**: | 요구에 가장 적합한 지역을 선택합니다. | 다른 리소스와 동일한 위치에 Event Hubs 네임스페이스를 만듭니다.
    | **네임스페이스 이름** | *AzureMonitoringData* | 네임스페이스를 식별하는 고유한 이름을 선택합니다.
    | **이벤트 허브 이름** | *DiagnosticLogsData* | 이벤트 허브는 고유한 범위 지정 컨테이너 역할을 하는 네임스페이스 아래에 배치됩니다. |
    | **소비자 그룹 이름** | *adxpipeline* | 소비자 그룹 이름을 만듭니다. 소비자 그룹을 사용하면 각기 별도의 이벤트 스트림 보기가 표시되는 여러 애플리케이션을 사용할 수 있습니다. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Azure Monitor 로그를 이벤트 허브에 연결

이제 진단 로그와 활동 로그를 이벤트 허브에 연결해야 합니다.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>이벤트 허브에 진단 로그 연결

메트릭을 내보낼 리소스를 선택합니다. Event Hubs 네임스페이스, Azure Key Vault, Azure IoT Hub 및 Azure Data Explorer 클러스터를 비롯한 여러 가지 리소스 유형이 진단 로그 내보내기를 지원합니다. 이 자습서에서는 Azure Data Explorer 클러스터를 리소스로 사용합니다.

1. Azure Portal에서 Kusto 클러스터를 선택합니다.
1. **진단 설정**을 선택한 다음, **진단 켜기** 링크를 선택합니다. 

    ![진단 설정](media/ingest-data-no-code/diagnostic-settings.png)

1. **진단 설정** 창이 열립니다. 다음과 같은 단계를 수행합니다.
   1. 진단 로그 데이터 이름을 *ADXExportedData*라고 지정합니다.
   1. **메트릭** 아래에서 **모든 메트릭** 확인란을 선택합니다(선택 사항).
   1. **이벤트 허브로의 스트림** 확인란을 선택합니다.
   1. **구성**을 선택합니다.

      ![진단 설정 창](media/ingest-data-no-code/diagnostic-settings-window.png)

1. **이벤트 허브 선택** 창에서 직접 만든 이벤트 허브로 진단 로그의 데이터를 내보내는 방법을 구성합니다.
    1. **이벤트 허브 네임스페이스 선택** 목록에서 *AzureMonitoringData*를 선택합니다.
    1. **이벤트 허브 이름 선택** 목록에서 *diagnosticlogsdata*를 선택합니다.
    1. **이벤트 허브 정책 이름** 목록에서 **RootManagerSharedAccessKey**를 선택합니다.
    1. **확인**을 선택합니다.

1. **저장**을 선택합니다.

### <a name="connect-activity-logs-to-your-event-hub"></a>이벤트 허브에 활동 로그 연결

1. Azure Portal 왼쪽 메뉴에서 **활동 로그**를 선택합니다.
1. **활동 로그** 창이 열립니다. **이벤트 허브로 내보내기**를 선택합니다.

    ![활동 로그 창](media/ingest-data-no-code/activity-log.png)

1. **활동 로그 내보내기** 창이 열립니다.
 
    ![활동 로그 내보내기 창](media/ingest-data-no-code/export-activity-log.png)

1. **활동 로그 내보내기** 창에서 다음 단계를 수행합니다.
      1. 구독을 선택합니다.
      1. **지역** 목록에서 **모두 선택**을 선택합니다.
      1. **이벤트 허브로 내보내기** 확인란을 선택합니다.
      1. **Service Bus 네임스페이스를 선택합니다.** 를 선택하여 **이벤트 허브 선택** 창을 엽니다.
      1. **이벤트 허브 선택** 창에서 구독을 선택합니다.
      1. **이벤트 허브 네임스페이스 선택** 목록에서 *AzureMonitoringData*를 선택합니다.
      1. **이벤트 허브 정책 이름 선택** 목록에서 기본 이벤트 허브 정책 이름을 선택합니다.
      1. **확인**을 선택합니다.
      1. 창의 왼쪽 위 모서리에서 **저장**을 선택합니다.
   이름이 *insights-operational-logs*인 이벤트 허브가 만들어집니다.

### <a name="see-data-flowing-to-your-event-hubs"></a>이벤트 허브로 흐르는 데이터 보기

1. 연결이 정의되고 이벤트 허브로 활동 로그 내보내기가 완료 될 때까지 잠시 기다립니다. Event Hubs 네임스페이스로 이동하여 직접 만든 이벤트 허브를 봅니다.

    ![만들어진 Event Hubs](media/ingest-data-no-code/event-hubs-created.png)

1. 이벤트 허브로 흐르는 데이터 보기:

    ![이벤트 허브의 데이터](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Azure Data Explorer에 이벤트 허브연결

이제 진단 로그 및 활동 로그에 대한 데이터 연결을 만들어야 합니다.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>진단 로그에 대한 데이터 연결 만들기

1. *kustodocs*라는 Azure Data Explorer 클러스터의 왼쪽 메뉴에서 **Databases**를 선택합니다.
1. **데이터베이스** 창에서 *TestDatabase*를 선택합니다.
1. 왼쪽 메뉴에서 **데이터 수집**을 선택합니다.
1. **데이터 수집** 창에서 **+ 데이터 연결 추가**를 클릭합니다.
1. **데이터 연결** 창에서 다음 정보를 입력합니다.

    ![이벤트 허브 데이터 연결](media/ingest-data-no-code/event-hub-data-connection.png)

    데이터 원본:

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **데이터 연결 이름** | *DiagnosticsLogsConnection* | Azure 데이터 탐색기에서 만들 연결의 이름입니다.|
    | **이벤트 허브 네임스페이스** | *AzureMonitoringData* | 앞에서 선택한 네임스페이스를 식별하는 이름입니다. |
    | **이벤트 허브** | *diagnosticlogsdata* | 앞에서 만든 이벤트 허브입니다. |
    | **소비자 그룹** | *adxpipeline* | 앞에서 만든 이벤트 허브에 정의된 소비자 그룹입니다. |
    | | |

    대상 테이블:

    라우팅 옵션으로는 *고정* 라우팅과 *동적* 라우팅이라는 두 가지 옵션이 있습니다. 이 자습서에서는 고정 라우팅(기본값)을 사용합니다. 이 경우 테이블 이름, 데이터 형식 및 매핑을 직접 지정합니다. **내 데이터에 라우팅 정보 포함**을 선택 취소합니다.

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **테이블** | *DiagnosticLogsRawRecords* | *TestDatabase* 데이터베이스에 만든 테이블입니다. |
    | **날짜 형식** | *JSON* | 테이블에 사용되는 형식입니다. |
    | **열 매핑** | *DiagnosticLogsRecordsMapping* | *TestDatabase* 데이터베이스에 만든 매핑이며, 들어오는 JSON 데이터를 *DiagnosticLogsRawRecords* 테이블의 열 이름 및 데이터 형식에 매핑합니다.|
    | | |

1. **만들기**를 선택합니다.  

### <a name="create-the-data-connection-for-activity-logs"></a>활동 로그에 대한 데이터 연결 만들기

진단 로그에 대한 데이터 연결 만들기 섹션의 단계를 반복하여 활동 로그에 대한 데이터 연결을 만듭니다.

1. **데이터 연결** 창에 다음 설정을 사용합니다.

    데이터 원본:

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **데이터 연결 이름** | *ActivityLogsConnection* | Azure 데이터 탐색기에서 만들 연결의 이름입니다.|
    | **이벤트 허브 네임스페이스** | *AzureMonitoringData* | 앞에서 선택한 네임스페이스를 식별하는 이름입니다. |
    | **이벤트 허브** | *insights-operational-logs* | 앞에서 만든 이벤트 허브입니다. |
    | **소비자 그룹** | *$Default* | 기본 소비자 그룹입니다. 필요하면 다른 소비자 그룹을 만들 수 있습니다. |
    | | |

    대상 테이블:

    라우팅 옵션으로는 *고정* 라우팅과 *동적* 라우팅이라는 두 가지 옵션이 있습니다. 이 자습서에서는 고정 라우팅(기본값)을 사용합니다. 이 경우 테이블 이름, 데이터 형식 및 매핑을 직접 지정합니다. **내 데이터에 라우팅 정보 포함**을 선택 취소합니다.

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **테이블** | *ActivityLogsRawRecords* | *TestDatabase* 데이터베이스에 만든 테이블입니다. |
    | **날짜 형식** | *JSON* | 테이블에 사용되는 형식입니다. |
    | **열 매핑** | *ActivityLogsRawRecordsMapping* | *TestDatabase* 데이터베이스에 만든 매핑이며, 들어오는 JSON 데이터를 *ActivityLogsRawRecords* 테이블의 열 이름 및 데이터 형식에 매핑합니다.|
    | | |

1. **만들기**를 선택합니다.  

## <a name="query-the-new-tables"></a>새 테이블 쿼리

이제 데이터가 흐르는 파이프라인이 있습니다. 클러스터를 통한 수집은 기본적으로 5분이 소요되므로 쿼리를 시작하기 전에 몇 분 동안 데이터 흐름을 허용합니다.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>진단 로그 테이블을 쿼리하는 예제

다음 쿼리는 Azure Data Explorer의 진단 로그 레코드에서 쿼리 기간 데이터를 분석합니다.

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

쿼리 결과:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="an-example-of-querying-the-activity-logs-table"></a>활동 로그 테이블을 쿼리하는 예제

다음 쿼리는 Azure Data Explorer에서 활동 로그 레코드의 데이터를 분석합니다.

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

쿼리 결과:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 Azure Data Explorer에서 추출한 데이터에 대해 훨씬 많은 쿼리를 작성하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)
