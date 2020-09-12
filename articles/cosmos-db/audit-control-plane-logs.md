---
title: Azure Cosmos DB 제어 평면 작업을 감사 하는 방법
description: Azure Cosmos DB에서 지역 추가, 처리량 업데이트, 지역 장애 조치, VNet 추가 등의 제어 평면 작업을 감사 하는 방법에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/25/2020
ms.author: sngun
ms.openlocfilehash: 691c6ec0559eceb60d57bf04819701edebbffd83
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462448"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure Cosmos DB 제어 평면 작업을 감사 하는 방법

Azure Cosmos DB의 컨트롤 평면은 Azure Cosmos 계정에서 다양 한 작업 집합을 수행할 수 있는 RESTful 서비스입니다. 리소스 모델에 대 한 작업을 수행 하기 위해 공용 리소스 모델 (예: 데이터베이스, 계정) 및 다양 한 작업을 노출 합니다. 제어 평면 작업에는 Azure Cosmos 계정 또는 컨테이너에 대 한 변경 내용이 포함 됩니다. 예를 들어 Azure Cosmos 계정 만들기, 지역 추가, 처리량 업데이트, 지역 장애 조치 (failover), VNet 추가 등의 작업은 제어 평면 작업 중 일부입니다. 이 문서에서는 Azure Cosmos DB에서 제어 평면 작업을 감사 하는 방법을 설명 합니다. Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 Azure Cosmos 계정에 대 한 제어 평면 작업을 실행할 수 있지만 컨테이너의 경우 Azure CLI 또는 PowerShell을 사용 합니다.

다음은 제어 평면 작업을 감사 하는 몇 가지 예제 시나리오입니다.

* Azure Cosmos 계정에 대 한 방화벽 규칙이 수정 될 때 경고를 받을 수 있습니다. 이 경고는 Azure Cosmos 계정의 네트워크 보안을 제어 하는 규칙에 대 한 무단 수정을 확인 하 고 빠른 작업을 수행 하는 데 필요 합니다.

* Azure Cosmos 계정에서 새 지역을 추가 하거나 제거 하는 경우 경고를 받을 수 있습니다. 지역을 추가 하거나 제거 하면 청구 및 데이터 주권 요구 사항에 영향을 미칩니다. 이 경고는 계정에서 지역에 대 한 실수로 인 한 추가 또는 제거를 검색 하는 데 도움이 됩니다.

* 변경 된 내용에 대 한 진단 로그에서 자세한 정보를 가져올 수 있습니다. 예를 들어 VNet이 변경 되었습니다.

## <a name="disable-key-based-metadata-write-access"></a>키 기반 메타 데이터 쓰기 액세스 사용 안 함

Azure Cosmos DB에서 제어 평면 작업을 감사 하기 전에 계정에 대 한 키 기반 메타 데이터 쓰기 액세스를 사용 하지 않도록 설정 합니다. 키 기반 메타 데이터 쓰기 액세스를 사용 하지 않도록 설정 하면 계정 키를 통해 Azure Cosmos 계정에 연결 하는 클라이언트가 계정에 액세스할 수 없습니다. 속성을 true로 설정 하 여 쓰기 액세스를 비활성화할 수 있습니다 `disableKeyBasedMetadataWriteAccess` . 이 속성을 설정한 후에는 적절 한 RBAC (역할 기반 액세스 제어) 역할 및 자격 증명을 사용 하는 사용자가 리소스에 대 한 변경 내용을 수행할 수 있습니다. 이 속성을 설정 하는 방법에 대 한 자세한 내용은 [sdk에서 변경 방지](role-based-access-control.md#prevent-sdk-changes) 문서를 참조 하세요. 

`disableKeyBasedMetadataWriteAccess`가 설정 된 후 SDK 기반 클라이언트가 만들기 또는 업데이트 작업을 실행 하는 경우 *' ContainerNameorDatabaseName ' 리소스에 대 한 작업 ' POST '는 Azure Cosmos DB 끝점이 반환 될 수 없습니다* . 계정에 대 한 이러한 작업에 대 한 액세스를 설정 하거나 Azure Resource Manager, Azure CLI 또는 Azure PowerShell를 통해 만들기/업데이트 작업을 수행 해야 합니다. 다시 전환 하려면 [COSMOS SDK에서 변경 방지](role-based-access-control.md#prevent-sdk-changes) 문서에 설명 된 대로 Azure CLI를 사용 하 여 disableKeyBasedMetadataWriteAccess을 **false** 로 설정 합니다. 의 값을 `disableKeyBasedMetadataWriteAccess` true 대신 false로 변경 해야 합니다.

메타 데이터 쓰기 액세스를 해제 하는 경우 다음 사항을 고려 하세요.

* SDK 또는 계정 키를 사용 하 여 응용 프로그램에서 위의 리소스 (예: 컬렉션 만들기, 업데이트 처리량, ...)를 변경 하는 메타 데이터 호출을 수행 하지 않도록 평가 하 고 확인 합니다.

* 현재 Azure Portal는 메타 데이터 작업에 계정 키를 사용 하므로 이러한 작업이 차단 됩니다. 또는 Azure CLI, Sdk 또는 리소스 관리자 템플릿 배포를 사용 하 여 이러한 작업을 수행 합니다.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>제어 평면 작업에 대 한 진단 로그 사용

Azure Portal를 사용 하 여 제어 평면 작업에 대 한 진단 로그를 사용 하도록 설정할 수 있습니다. 을 사용 하도록 설정 하면 진단 로그에서 작업을 관련 세부 정보를 포함 하는 시작 및 완료 이벤트 쌍으로 기록 합니다. 예를 들어 영역 *Failoverstart* 및 영역 *failovercomplete* 는 지역 장애 조치 (failover) 이벤트를 완료 합니다.

다음 단계를 사용 하 여 제어 평면 작업에 대 한 로깅을 사용 하도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Cosmos 계정으로 이동합니다.

1. **진단 설정** 창을 열고 만들 로그의 **이름을** 입력 합니다.

1. 로그 형식에 대해 **ControlPlaneRequests** 를 선택 하 고 **Log Analytics 보내기** 옵션을 선택 합니다.

저장소 계정 또는 스트림에 이벤트 허브에 로그를 저장할 수도 있습니다. 이 문서에서는 log analytics로 로그를 전송 하 고 쿼리 하는 방법을 보여 줍니다. 을 사용 하도록 설정한 후에는 진단 로그를 적용 하는 데 몇 분이 걸립니다. 해당 시점 이후에 수행 된 모든 제어 평면 작업을 추적할 수 있습니다. 다음 스크린샷에서는 제어 평면 로그를 사용 하도록 설정 하는 방법을 보여 줍니다.

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="제어 평면 요청 로깅 사용":::

## <a name="view-the-control-plane-operations"></a>제어 평면 작업 보기

로깅을 켠 후 다음 단계를 사용 하 여 특정 계정에 대 한 작업을 추적 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 합니다.

1. 왼쪽 탐색 창에서 **모니터** 탭을 열고 **로그** 창을 선택 합니다. 범위에서 해당 특정 계정으로 쿼리를 쉽게 실행할 수 있는 UI가 열립니다. 다음 쿼리를 실행 하 여 제어 평면 로그를 확인 합니다.

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

다음 스크린샷은 Azure Cosmos 계정에 대 한 일관성 수준이 변경 될 때 로그를 캡처합니다.

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="VNet이 추가 될 때의 제어 평면 로그":::

다음 스크린샷은 Cassandra 계정의 keyspace 또는 테이블이 만들어지고 처리량이 업데이트 될 때 로그를 캡처합니다. 다음 스크린샷에 표시 된 것 처럼 데이터베이스 및 컨테이너에 대 한 만들기 및 업데이트 작업의 제어 평면 로그는 별도로 기록 됩니다.

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="처리량이 업데이트 되는 경우의 제어 평면 로그":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>특정 작업에 연결 된 id를 식별 합니다.

추가로 디버깅 하려는 경우 작업 ID 또는 작업 타임 스탬프를 사용 하 여 **활동 로그** 에서 특정 작업을 식별할 수 있습니다. 타임 스탬프는 활동 ID가 명시적으로 전달 되지 않은 일부 리소스 관리자 클라이언트에 사용 됩니다. 활동 로그는 작업이 시작 된 id에 대 한 세부 정보를 제공 합니다. 다음 스크린샷에서는 활동 ID를 사용 하 고 활동 로그에서 활동 ID와 연결 된 작업을 찾는 방법을 보여 줍니다.

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="작업 ID를 사용 하 여 작업을 찾습니다.":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Azure Cosmos 계정에 대 한 제어 평면 작업

계정 수준에서 사용할 수 있는 제어 평면 작업은 다음과 같습니다. 대부분의 작업은 계정 수준에서 추적 됩니다. 이러한 작업은 Azure monitor에서 메트릭으로 사용할 수 있습니다.

* 추가 된 지역
* 지역이 제거 됨
* 계정이 삭제 됨
* 지역 장애 조치
* 만든 계정
* 가상 네트워크 삭제 됨
* 계정 네트워크 설정 업데이트 됨
* 계정 복제 설정 업데이트 됨
* 계정 키가 업데이트 됨
* 계정 백업 설정 업데이트 됨
* 계정 진단 설정 업데이트 됨

## <a name="control-plane-operations-for-database-or-containers"></a>데이터베이스 또는 컨테이너에 대 한 제어 평면 작업

데이터베이스 및 컨테이너 수준에서 사용할 수 있는 제어 평면 작업은 다음과 같습니다. 이러한 작업은 Azure monitor에서 메트릭으로 사용할 수 있습니다.

* SQL Database 만들어짐
* SQL Database 업데이트 됨
* SQL Database 처리량 업데이트 됨
* SQL Database 삭제 됨
* 만든 SQL 컨테이너
* 업데이트 된 SQL 컨테이너
* SQL 컨테이너 처리량 업데이트
* 삭제 된 SQL 컨테이너
* 만든 Cassandra Keyspace
* Cassandra Keyspace 업데이트 됨
* Cassandra Keyspace 처리량 업데이트 됨
* Cassandra Keyspace Deleted
* Cassandra 테이블을 만들었습니다.
* Cassandra 테이블 업데이트 됨
* Cassandra 테이블 처리량이 업데이트 됨
* Cassandra 테이블 삭제 됨
* Gremlin 데이터베이스를 만들었습니다.
* Gremlin 데이터베이스 업데이트 됨
* Gremlin 데이터베이스 처리량이 업데이트 됨
* Gremlin 데이터베이스 삭제 됨
* Gremlin 그래프가 만들어짐
* Gremlin 그래프가 업데이트 됨
* Gremlin Graph 처리량이 업데이트 됨
* Gremlin 그래프가 삭제 됨
* Mongo 데이터베이스를 만들었습니다.
* Mongo 데이터베이스 업데이트 됨
* Mongo 데이터베이스 처리량이 업데이트 됨
* Mongo 데이터베이스가 삭제 됨
* Mongo 수집을 만듦
* Mongo 수집 업데이트 됨
* Mongo 수집 처리량이 업데이트 됨
* Mongo 컬렉션이 삭제 됨
* AzureTable 테이블을 만들었습니다.
* AzureTable 테이블 업데이트 됨
* AzureTable 테이블 처리량이 업데이트 됨
* AzureTable 테이블 삭제 됨

## <a name="diagnostic-log-operations"></a>진단 로그 작업

다음은 다양 한 작업에 대 한 진단 로그의 작업 이름입니다.

* 국가 Addstart, 지역 Addcomplete
* 지역 Removestart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* 지역 Failoverstart, 지역 Failovercomplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

API 관련 작업의 경우 작업은 다음과 같은 형식으로 이름이 지정 됩니다.

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "처리량" + operationType

**예제** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

*Resourcedetails* 속성은 전체 리소스 본문을 요청 페이로드로 포함 하 고 업데이트 하도록 요청 된 모든 속성을 포함 합니다.

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>제어 평면 작업에 대 한 진단 로그 쿼리

다음은 제어 평면 작업에 대 한 진단 로그를 가져오는 몇 가지 예입니다.

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdate"
```

컨테이너 삭제 작업을 시작한 호출자와 호출자를 가져오는 쿼리:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB용 Azure Monitor 살펴보기](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그](use-metrics.md)
