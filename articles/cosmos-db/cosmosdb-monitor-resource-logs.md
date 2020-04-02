---
title: Azure 진단 설정을 사용하여 Azure Cosmos DB 데이터 모니터링
description: Azure 진단 설정을 사용하여 Azure Cosmos DB에 저장된 데이터의 성능 및 가용성을 모니터링하는 방법에 대해 알아봅니다.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521040"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure에서 진단 설정을 사용하여 Azure Cosmos DB 데이터 모니터링

Azure의 진단 설정은 리소스 로그를 수집하는 데 사용됩니다. Azure 리소스 로그는 리소스에서 내보내지며 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. 이러한 로그는 요청당 캡처되며 "데이터 평면 로그"라고도 합니다. 데이터 평면 작업의 몇 가지 예로는 삭제, 삽입 및 readFeed가 있습니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

플랫폼 메트릭 및 활동 로그는 자동으로 수집되지만 리소스 로그를 수집하거나 Azure Monitor 외부에서 전달하려면 진단 설정을 만들어야 합니다. 다음 단계를 사용하여 Azure Cosmos 계정에 대한 진단 설정을 켤 수 있습니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. Azure Cosmos 계정으로 이동합니다. 진단 **설정** 창을 연 다음 **진단 설정 추가** 옵션을 선택합니다.

1. 진단 **설정** 창에서 양식을 다음과 같은 세부 정보로 채웁니다. 

    * **이름**: 만들 로그에 대한 이름을 입력합니다.

    * 보관할 로그를 **저장소 계정으로 저장하고** **이벤트 허브로 스트리밍하거나** **로그 분석으로 보낼 수** 있습니다.

1. 진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Azure Cosmos DB에서 지원하는 로그 범주는 수집된 샘플 로그와 함께 다음과 같습니다.

 * **DataPlaneRequests**: Azure Cosmos DB의 SQL, 그래프, 몽고DB, 카산드라 및 테이블 API 계정을 포함하는 모든 API에 백 엔드 요청을 로그온하려면 이 옵션을 선택합니다. 참고할 주요 속성은 `statusCode` `clientIPaddress`" `Requestcharge` `partitionID`및 .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: MongoDB에 대한 Azure Cosmos DB의 API에 요청을 제공하기 위해 프런트 엔드에서 사용자 시작 요청을 기록하려면 이 옵션을 선택합니다. 이 로그 유형은 다른 API 계정에서 사용할 수 없습니다. 주의해야 할 주요 `Requestcharge` `opCode`속성은 다음과 같습니다. 진단 로그에서 MongoRequests를 사용하도록 설정하면 DataPlaneRequests를 해제해야 합니다. API에서 요청한 모든 요청에 대해 하나의 로그가 표시됩니다.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: 카산드라에 대한 Azure Cosmos DB의 API에 요청을 제공하기 위해 프런트 엔드에서 사용자 시작 요청을 기록하려면 이 옵션을 선택합니다. 이 로그 유형은 다른 API 계정에서 사용할 수 없습니다. 주의해야 할 주요 `operationName` `requestCharge`속성은 " `piiCommandText` 진단 로그에서 CassandraRequests를 사용하도록 설정하면 DataPlaneRequests를 해제해야 합니다. API에서 요청한 모든 요청에 대해 하나의 로그가 표시됩니다.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **쿼리런타임통계**: 실행된 쿼리 텍스트를 기록하려면 이 옵션을 선택합니다. 이 로그 유형은 SQL API 계정에서만 사용할 수 있습니다.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **파티션키통계**: 파티션 키의 통계를 기록하려면 이 옵션을 선택합니다. 이는 현재 파티션 키의 저장소 크기(KB)로 표시됩니다. 이 문서의 Azure 진단 쿼리 섹션을 [사용하여 문제 해결을](#diagnostic-queries) 확인합니다. 예를 들어 "PartitionKeyStatistics"를 사용하는 쿼리입니다. 로그는 대부분의 데이터 저장소를 차지하는 처음 세 개의 파티션 키에 대해 내보내입니다. 이 로그에는 구독 ID, 지역 이름, 데이터베이스 이름, 컬렉션 이름, 파티션 키 및 KB의 저장소 크기와 같은 데이터가 포함됩니다.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: 이 로그는 파티션 키의 초당 집계된 RU/s 소비를 보고합니다. 현재 Azure Cosmos DB는 SQL API 계정과 포인트 읽기/쓰기 및 저장 프로시저 작업에 대해서만 파티션 키를 보고합니다. 다른 API 및 작업 형식은 지원되지 않습니다. 다른 API의 경우 진단 로그 테이블의 파티션 키 열이 비어 있습니다. 이 로그에는 구독 ID, 지역 이름, 데이터베이스 이름, 컬렉션 이름, 파티션 키, 작업 유형 및 요청 요금과 같은 데이터가 포함됩니다. 이 문서의 Azure 진단 쿼리 섹션을 [사용하여 문제 해결을](#diagnostic-queries) 확인합니다. 예를 들어 "PartitionKeyRUConsumption"를 사용하는 쿼리입니다. 

* **ControlPlaneRequests**: 이 로그에는 계정 만들기, 지역 추가 또는 제거, 계정 복제 설정 업데이트 등과 같은 제어 평면 작업에 대한 세부 정보가 포함되어 있습니다. 이 로그 유형은 SQL(코어), 몽고DB, 그렘린, 카산드라, 테이블 API를 포함하는 모든 API 형식에 사용할 수 있습니다.

* **요청**: Azure Cosmos DB에서 진단 설정의 대상까지 메트릭 데이터를 수집하려면 이 옵션을 선택합니다. 이는 Azure 메트릭에서 자동으로 수집되는 데이터와 동일합니다. 리소스 로그를 사용하여 메트릭 데이터를 수집하여 두 종류의 데이터를 함께 분석하고 Azure Monitor 외부로 메트릭 데이터를 보냅니다.

Azure 포털, CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 방법에 대한 자세한 내용은 Azure 문서에서 [플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기를](../azure-monitor/platform/diagnostic-settings.md) 참조하십시오.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>진단 쿼리 문제 해결

1. 고가의 쿼리에 대한 요청 요금을 받는 방법은 무엇입니까?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. RU/s의 대부분을 차지하는 작업을 찾는 방법은 무엇입니까?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. 다른 작업에 대한 배포를 얻는 방법은 무엇입니까?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 파티션에서 사용한 최대 처리량은 무엇입니까?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 파티션 키 RU / 초당 소비에 대한 정보를 얻는 방법은 무엇입니까?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 특정 파티션 키에 대한 요청 요금을 받는 방법

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 특정 기간에 사용되는 대부분의 RU /s가있는 상위 파티션 키를 얻는 방법은 무엇입니까? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. 스토리지 크기가 8GB보다 큰 파티션 키에 대한 로그를 얻는 방법은 무엇입니까?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. 파티션 키 통계를 사용하여 데이터베이스 계정의 상위 3개 파티션에서 기울이기를 평가하는 방법은 무엇입니까?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>다음 단계

* [Azure 코스모스 DB용 Azure 모니터](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그](use-metrics.md)
