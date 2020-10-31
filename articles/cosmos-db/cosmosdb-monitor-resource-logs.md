---
title: Azure 진단 설정을 사용 하 여 Azure Cosmos DB 데이터 모니터링
description: Azure 진단 설정을 사용 하 여에 저장 된 데이터의 성능 및 가용성을 모니터링 하는 방법에 대해 알아봅니다 Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/28/2020
ms.author: sngun
ms.openlocfilehash: 18850fafd1f6cb084c9e5fdb9a24e9c4fd8bb4cc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097569"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure에서 진단 설정을 사용 하 여 Azure Cosmos DB 데이터 모니터링
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure의 진단 설정은 리소스 로그를 수집 하는 데 사용 됩니다. Azure 리소스 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 합니다. 이러한 로그는 요청당 캡처되고 "데이터 평면 로그" 라고도 합니다. 데이터 평면 작업의 몇 가지 예로는 delete, insert 및 readFeed 있습니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

플랫폼 메트릭과 활동 로그는 자동으로 수집 되지만 리소스 로그를 수집 하거나 Azure Monitor 외부로 전달 하려면 진단 설정을 만들어야 합니다. 다음 단계를 사용 하 여 Azure Cosmos 계정에 대 한 진단 설정을 켤 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Cosmos 계정으로 이동합니다. **진단 설정** 창을 열고 **진단 설정 추가** 옵션을 선택 합니다.

1. **진단 설정** 창에서 다음 세부 정보로 양식을 채웁니다. 

    * **이름** : 만들 로그에 대한 이름을 입력합니다.

    * **저장소 계정에 보관** 하는 로그를 저장 하 고, **이벤트 허브에 스트림** 하거나, **로 보낼** 수 있습니다 Log Analytics

1. 진단 설정을 만들 때 수집할 로그 범주를 지정 합니다. Azure Cosmos DB에서 지 원하는 로그의 범주는 아래에 나열 된 샘플 로그와 함께 아래에 나열 되어 있습니다.

 * **DataPlaneRequests** : AZURE COSMOS DB의 SQL, Graph, MongoDB, Cassandra 및 Table API 계정을 포함 하는 모든 api에 백 엔드 요청을 기록 하려면이 옵션을 선택 합니다. 주의 해야 할 주요 속성은 `Requestcharge` , `statusCode` , `clientIPaddress` , `partitionID` , `resourceTokenPermissionId` 및 `resourceTokenPermissionMode` 입니다.

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372", "resourceTokenPermissionId": "perm-prescriber-app","resourceTokenPermissionMode": "all", "resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests** : 프런트 엔드에서 사용자가 시작한 요청을 MONGODB의 API에 대 한 Azure Cosmos DB 요청을 처리 하도록 기록 하려면이 옵션을 선택 합니다. 이 로그 유형은 다른 API 계정에 사용할 수 없습니다. 주의 해야 할 주요 속성은 `Requestcharge` , `opCode` 입니다. 진단 로그에서 MongoRequests를 사용 하도록 설정 하는 경우 DataPlaneRequests를 해제 해야 합니다. API에 대 한 모든 요청에 대해 하나의 로그가 표시 됩니다.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests** : 프런트 엔드에서 사용자가 시작한 요청을 CASSANDRA의 API에 대 한 Azure Cosmos DB 요청을 처리 하도록 기록 하려면이 옵션을 선택 합니다. 이 로그 유형은 다른 API 계정에 사용할 수 없습니다. 주의할 핵심 속성은 `operationName` , `requestCharge` , `piiCommandText` 입니다. 진단 로그에서 CassandraRequests를 사용 하도록 설정 하는 경우 DataPlaneRequests를 해제 해야 합니다. API에 대 한 모든 요청에 대해 하나의 로그가 표시 됩니다.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **Queryruntimestatistics** : 실행 된 쿼리 텍스트를 기록 하려면이 옵션을 선택 합니다. 이 로그 유형은 SQL API 계정에만 사용할 수 있습니다.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* 파티션 **키 통계를** 기록 하려면이 옵션을 선택 합니다. 이는 현재 파티션 키의 저장소 크기 (KB)로 표시 됩니다. 이 문서의 [Azure 진단 쿼리를 사용 하 여 문제 해결](#diagnostic-queries) 섹션을 참조 하세요. 예를 들어 "파티션"을 사용 하는 쿼리를 사용 합니다. 대부분의 데이터 저장소를 차지 하는 처음 세 개의 파티션 키에 대해 로그를 내보냅니다. 이 로그에는 구독 ID, 지역 이름, 데이터베이스 이름, 컬렉션 이름, 파티션 키, 저장소 크기 (KB) 등의 데이터가 포함 됩니다.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* 분할:이 로그는 파티션 키의 초당 집계 된 사용량을 보고 **합니다.** 현재 Azure Cosmos DB은 SQL API 계정에 대 한 파티션 키와 지점 읽기/쓰기 및 저장 프로시저 작업에 대 한 파티션 키를 보고 합니다. 다른 Api 및 작업 유형은 지원 되지 않습니다. 다른 Api의 경우 진단 로그 테이블의 파티션 키 열이 비어 있게 됩니다. 이 로그에는 구독 ID, 지역 이름, 데이터베이스 이름, 컬렉션 이름, 파티션 키, 작업 유형, 요청 요금 등의 데이터가 포함 됩니다. 이 문서의 [Azure 진단 쿼리를 사용 하 여 문제 해결](#diagnostic-queries) 섹션을 참조 하세요. 예를 들어 "파티션"을 사용 하는 쿼리를 사용 합니다. 

* **ControlPlaneRequests** :이 로그에는 계정 만들기, 지역 추가 또는 제거, 계정 복제 설정 업데이트 등의 제어 평면 작업에 대 한 세부 정보가 포함 됩니다. 이 로그 유형은 SQL (Core), MongoDB, Gremlin, Cassandra, Table API를 포함 하는 모든 API 유형에 사용할 수 있습니다.

* **요청** : Azure Cosmos DB에서 진단 설정의 대상으로 메트릭 데이터를 수집 하려면이 옵션을 선택 합니다. Azure 메트릭에 의해 자동으로 수집 되는 동일한 데이터입니다. 리소스 로그를 사용 하 여 메트릭 데이터를 수집 하 여 두 종류의 데이터를 함께 분석 하 고 Azure Monitor 외부에서 메트릭 데이터를 보냅니다.

Azure Portal, CLI 또는 PowerShell을 사용 하 여 진단 설정을 만드는 방법에 대 한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기](../azure-monitor/platform/diagnostic-settings.md) 문서를 참조 하세요.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> 진단 쿼리와 관련 된 문제 해결

1. 실행 하는 데 3 밀리초 보다 오래 걸리는 작업을 쿼리 하는 방법:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. 작업을 실행 하는 사용자 에이전트를 쿼리 하는 방법:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. 장기 실행 작업을 쿼리 하는 방법:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. 데이터베이스 계정에 대 한 상위 3 개 파티션 간의 기울기를 평가 하는 파티션 키 통계를 가져오는 방법:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

1. 비용이 많이 드는 쿼리에 대 한 요청 요금을 가져오는 방법

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

1. 많은 작업을 수행 하는 작업을 찾는 방법

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. **DataPlaneRequests** 및 **queryruntimestatistics** 의 데이터와 조인 된 100 o s/s를 사용 하는 모든 쿼리를 가져오는 방법입니다.

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. 요청 요금 및 쿼리 실행 기간을 가져오는 방법

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. 여러 작업에 대 한 배포를 가져오는 방법

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 파티션이 사용한 최대 처리량은 무엇 인가요?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 초당 파티션 키/s 사용에 대 한 정보를 가져오는 방법

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 특정 파티션 키에 대 한 요청 요금을 가져오는 방법

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 특정 기간에 사용 되는 대부분의 o s/s를 사용 하 여 상위 파티션 키를 가져오는 방법 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. 저장소 크기가 8gb 보다 큰 파티션 키에 대 한 로그를 가져오는 방법

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. 작업, 요청 요금 또는 응답의 길이에 대 한 P 99 또는 P50 복제 대기 시간을 얻는 방법

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. ControlPlane 로그를 가져오는 방법
 
   [키 기반 메타 데이터 쓰기 액세스 사용 안 함](audit-control-plane-logs.md#disable-key-based-metadata-write-access) 문서에 설명 된 대로 플래그를 전환 하 고 Azure PowerShell, Azure CLI 또는 Azure Resource Manager를 사용 하 여 작업을 실행 해야 합니다.
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB용 Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그](use-metrics.md)
