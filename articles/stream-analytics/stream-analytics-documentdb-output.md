---
title: Azure Cosmos DB에 Azure Stream Analytics 출력
description: 이 문서에서는 비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 위해 Azure Stream Analytics를 사용하여 JSON 출력에 대한 Azure Cosmos DB에 출력을 저장하는 방법을 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364572"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB에 Azure Stream Analytics 출력  
JSON 출력에 대 한 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 를 대상으로 할 수 AZURE STREAM ANALYTICS 비구조적 json 데이터에 대해 데이터 보관 및 짧은 대기 시간 쿼리를 사용할 수 있습니다. 이 문서에서는 이 구성을 구현하기 위한 몇 가지 모범 사례를 설명합니다.

Azure Cosmos DB에 익숙하지 않은 경우 [Azure Cosmos DB 설명서](https://docs.microsoft.com/azure/cosmos-db/) 를 참조 하 여 시작 하세요. 

> [!Note]
> 현재 Stream Analytics는 *SQL API*를 통해서만 Azure Cosmos DB에 대 한 연결을 지원 합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. 다른 Api를 사용 하 여 만든 계정을 Azure Cosmos DB Stream Analytics 가리키는 경우 데이터가 올바르게 저장 되지 않을 수 있습니다. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>출력 대상으로 Azure Cosmos DB의 기본 사항
Stream Analytics의 Azure Cosmos DB 출력을 사용 하 여 스트림 처리 결과를 Azure Cosmos DB 컨테이너에 JSON 출력으로 쓸 수 있습니다. 

Stream Analytics는 데이터베이스에 컨테이너를 만들지 않습니다. 대신, 앞에서 만들어야 합니다. 그런 다음 Azure Cosmos DB 컨테이너의 청구 비용을 제어할 수 있습니다. [Azure Cosmos DB api](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용 하 여 컨테이너의 성능, 일관성 및 용량을 직접 조정할 수도 있습니다.

> [!Note]
> Azure Cosmos DB 방화벽에서 허용되는 IP 목록에 0.0.0.0을 추가해야 합니다.

다음 섹션에서는 Azure Cosmos DB에 대 한 몇 가지 컨테이너 옵션을 자세히 설명 합니다.

## <a name="tuning-consistency-availability-and-latency"></a>튜닝 일관성, 가용성 및 대기 시간
응용 프로그램 요구 사항을 충족 하기 위해 Azure Cosmos DB를 사용 하면 데이터베이스 및 컨테이너를 미세 조정 하 고 일관성, 가용성, 대기 시간 및 처리량 간에 절충을 내릴 수 있습니다. 

시나리오에서 읽기 및 쓰기 대기 시간에 대해 필요로 하는 읽기 일관성 수준에 따라 데이터베이스 계정에 대한 일관성 수준을 선택할 수 있습니다. 컨테이너에서 RUs (요청 단위)를 확장 하 여 처리량을 향상 시킬 수 있습니다. 

또한 기본적으로 Azure Cosmos DB는 컨테이너에 대 한 각 CRUD 작업에서 동기 인덱싱을 사용 하도록 설정 합니다. 이 옵션은 Azure Cosmos DB에서 쓰기/읽기 성능을 제어 하는 또 다른 유용한 옵션입니다. 

자세한 내용은 [데이터베이스 및 쿼리 일관성 수준 변경](../cosmos-db/consistency-levels.md) 문서를 검토 하세요.

## <a name="upserts-from-stream-analytics"></a>Stream Analytics에서 Upsert
Azure Cosmos DB와 Stream Analytics 통합 하면 지정 된 **문서 ID** 열을 기반으로 컨테이너에 레코드를 삽입 하거나 업데이트할 수 있습니다. 이를 *upsert*라고도 합니다.

Stream Analytics는 낙관적 upsert 방법을 사용 합니다. 문서 ID 충돌 시 삽입이 실패할 경우에만 업데이트가 수행 됩니다. 

호환성 수준 1.0에서는 Stream Analytics이 업데이트를 패치 작업으로 수행 하므로 문서에 대 한 부분 업데이트를 사용할 수 있습니다. Stream Analytics 새 속성을 추가 하거나 기존 속성을 증분 방식으로 바꿉니다. 그러나 JSON 문서에서 배열 속성 값이 변경 되 면 전체 배열이 덮어 생성 됩니다. 즉, 배열이 병합 되지 않습니다. 

1\.2을 사용 하는 경우 문서를 삽입 하거나 upsert 동작을 수정 합니다. 호환성 수준 1.2에 대 한 이후 섹션에서는이 동작을 자세히 설명 합니다.

들어오는 JSON 문서에 기존 ID 필드가 있는 경우 해당 필드는 Azure Cosmos DB의 **문서 id** 열로 자동으로 사용 됩니다. 이후 쓰기는 다음과 같은 상황 중 하나를 초래 하 여 처리 됩니다.

- 고유 Id를 삽입 합니다.
- Id로 설정 된 id와 **문서 Id** **가** 중복 되 면 upsert로 설정 됩니다.
- 중복 된 Id와 **문서 ID가** 설정 되지 않으면 첫 번째 문서 다음에 오류가 발생 합니다.

중복 ID를 포함 하는 문서를 포함 하 여 *모든* 문서를 저장 하려는 경우 **AS** 키워드를 사용 하 여 쿼리에서 id 필드의 이름을 바꿉니다. **AS** 키워드를 사용 하거나 **문서 id** 설정을 사용 하 여 ID 필드를 만들거나 id를 다른 열 값으로 바꿀 Azure Cosmos DB 있습니다.

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB의 데이터 분할
Azure Cosmos DB는 워크 로드에 따라 파티션을 자동으로 크기 조정 합니다. 따라서 데이터 분할 방법으로 컨테이너를 [무제한](../cosmos-db/partition-data.md) 으로 사용 하는 것이 좋습니다. Stream Analytics 무제한 컨테이너에 쓸 때는 이전 쿼리 단계 또는 입력 파티션 구성표로 많은 병렬 기록기를 사용 합니다.

> [!NOTE]
> Azure Stream Analytics은 최상위 수준에서 파티션 키가 있는 무제한 컨테이너만 지원 합니다. 예를 들어 `/region`이 지원됩니다. 중첩 된 파티션 키 (예: `/region/name`)는 지원 되지 않습니다. 

선택한 파티션 키에 따라 다음과 같은 _경고가_표시 될 수 있습니다.

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

여러 고유 값을 포함 하는 파티션 키 속성을 선택 하 여 이러한 값에 대 한 작업 부하를 균등 하 게 분산할 수 있도록 하는 것이 중요 합니다. 분할의 자연 아티팩트는 동일한 파티션 키를 포함 하는 요청은 단일 파티션의 최대 처리량에 의해 제한 됩니다. 

동일한 파티션 키에 속하는 문서의 저장소 크기는 10gb로 제한 됩니다. 이상적인 파티션 키는 쿼리에서 필터로 자주 표시 되 고 솔루션을 확장할 수 있는 충분 한 카디널리티를 포함 하는 키입니다.

파티션 키는 Azure Cosmos DB에 대 한 저장 프로시저 및 트리거의 트랜잭션에 대 한 경계 이기도 합니다. 트랜잭션에서 함께 발생 하는 문서가 동일한 파티션 키 값을 공유 하도록 파티션 키를 선택 해야 합니다. [Azure Cosmos DB 분할](../cosmos-db/partitioning-overview.md) 문서에서는 파티션 키를 선택 하는 방법에 대해 자세히 설명 합니다.

고정 Azure Cosmos DB 컨테이너의 경우 Stream Analytics 전체를 확장 하거나 축소할 수 있는 방법이 없습니다. 크기는 10gb이 고 처리량은 1만 r u/초입니다. 고정 된 컨테이너에서 무제한 컨테이너 (예: 최소 1000 r u/초 및 파티션 키가 있는 데이터)로 데이터를 마이그레이션하려면 [데이터 마이그레이션 도구](../cosmos-db/import-data.md) 또는 [변경 피드 라이브러리](../cosmos-db/change-feed.md)를 사용 합니다.

여러 고정 컨테이너에 쓰는 기능은 더 이상 사용 되지 않습니다. Stream Analytics 작업을 확장 하는 데 권장 하지 않습니다.

## <a name="improved-throughput-with-compatibility-level-12"></a>호환성 수준이 1.2 인 향상 된 처리량
호환성 수준 1.2을 사용 하면 Stream Analytics는 Azure Cosmos DB에 대 한 대량 쓰기에 대 한 기본 통합을 지원 합니다. 이러한 통합을 통해 처리량을 최대화 하 고 제한 요청을 효율적으로 처리 하는 동시에 Azure Cosmos DB를 효과적으로 작성할 수 

향상 된 쓰기 메커니즘은 upsert 동작의 차이로 인해 새 호환성 수준에서 사용할 수 있습니다. 1\.2 이전 수준에서 upsert 동작은 문서를 삽입 하거나 병합 하는 것입니다. 1\.2을 사용 하는 경우 문서를 삽입 하거나 upsert 동작을 수정 합니다.

1\.2 이전의 수준에서 Stream Analytics 사용자 지정 저장 프로시저를 사용 하 여 파티션 키 당 문서를 Azure Cosmos DB 대량으로 대량 upsert 합니다. 여기에서 일괄 처리는 트랜잭션으로 기록 됩니다. 단일 레코드가 일시적인 오류 (제한)에 도달 하는 경우에도 전체 일괄 처리를 다시 시도해 야 합니다. 이렇게 하면 비교적 적절 한 제한으로 훨씬 더 느리게 시나리오를 만들 수 있습니다.

다음 예에서는 동일한 Azure Event Hubs 입력에서 읽는 두 개의 동일한 Stream Analytics 작업을 보여 줍니다. Stream Analytics 작업은 모두 통과 쿼리로 [완전히 분할](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) 되며 동일한 Azure Cosmos DB 컨테이너에 기록 됩니다. 왼쪽에 있는 메트릭은 호환성 수준 1.0로 구성 된 작업에서 가져온 것입니다. 오른쪽의 메트릭은 1.2로 구성 됩니다. Azure Cosmos DB 컨테이너의 파티션 키는 입력 이벤트에서 제공 하는 고유한 GUID입니다.

![Stream Analytics 메트릭의 비교](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Event Hubs 들어오는 이벤트 속도는 Azure Cosmos DB 컨테이너 (2만 RUs)가에서 사용 하도록 구성 되는 것 보다 2 배 더 높기 때문에 Azure Cosmos DB에서 제한이 예상 됩니다. 그러나 1.2를 사용 하는 작업은 더 높은 처리량 (분당 출력 이벤트)으로 일관 되 게 쓰고 평균 SU% 사용률을 줄입니다. 사용자 환경에서이 차이점은 몇 가지 요소에 따라 다릅니다. 이러한 요소에는 이벤트 형식 선택, 입력 이벤트/메시지 크기, 파티션 키 및 쿼리가 포함 됩니다.

![Azure Cosmos DB 메트릭 비교](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1\.2을 사용 하는 Stream Analytics 경우 Azure Cosmos DB에서 사용 가능한 처리량의 100%를 활용 하는 것이 더 지능적 이며 제한 또는 속도 제한을 resubmissions. 이는 동시에 컨테이너에서 실행 되는 쿼리와 같은 다른 워크 로드에 더 나은 환경을 제공 합니다. 초당 1000 ~ 1만 메시지의 싱크로 Azure Cosmos DB Stream Analytics 확장 하는 방법을 확인 하려면 [이 Azure 샘플 프로젝트](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)를 사용해 보세요.

Azure Cosmos DB 출력의 처리량은 1.0 및 1.1와 동일 합니다. Azure Cosmos DB에서 Stream Analytics 호환성 수준 1.2을 사용 하는 것 *이* 좋습니다.

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON 출력에 대 한 Azure Cosmos DB 설정

Stream Analytics에서 Azure Cosmos DB를 출력으로 사용 하면 정보에 대 한 다음 프롬프트가 생성 됩니다.

![Azure Cosmos DB 출력 스트림에 대 한 정보 필드](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|필드           | Description|
|-------------   | -------------|
|출력 별칭    | Stream Analytics 쿼리에서이 출력을 참조 하는 별칭입니다.|
|Subscription    | Azure 구독입니다.|
|계정 ID      | Azure Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다.|
|계정 키     | Azure Cosmos DB 계정에 대한 공유 액세스 키입니다.|
|데이터베이스        | Azure Cosmos DB 데이터베이스 이름입니다.|
|컨테이너 이름 | `MyContainer`와 같은 컨테이너 이름입니다. `MyContainer` 라는 컨테이너 하나가 있어야 합니다.  |
|문서 ID     | (선택 사항) 삽입 또는 업데이트 작업의 기준으로 사용해야 하는 고유 키로 사용되는 출력 이벤트의 열 이름입니다. 비워 두면 업데이트 옵션 없이 모든 이벤트가 삽입 됩니다.|

Azure Cosmos DB 출력을 구성한 후에는 쿼리에서이를 [INTO 문의](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)대상으로 사용할 수 있습니다. 이러한 방식으로 Azure Cosmos DB 출력을 사용 하는 경우 [파티션 키를 명시적으로 설정 해야](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks)합니다. 

출력 레코드는 Azure Cosmos DB의 파티션 키 뒤에 이름이 지정 된 대/소문자를 구분 하는 열을 포함 해야 합니다. 더 많은 병렬 처리를 위해 문에는 동일한 열을 사용 하는 [PARTITION by 절](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) 이 필요할 수 있습니다.

샘플 쿼리는 다음과 같습니다.

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>오류 처리 및 재시도

Stream Analytics에서 Azure Cosmos DB으로 이벤트를 전송 하는 동안 일시적인 오류가 발생 하거나 서비스를 사용할 수 없거나 제한이 발생 한 경우 작업을 성공적으로 완료 하는 데 무한정 재시도 Stream Analytics. 그러나 다음 오류에 대 한 재시도는 시도 하지 않습니다.

- 권한 없음 (HTTP 오류 코드 401)
- NotFound (HTTP 오류 코드 404)
- 사용할 수 없음 (HTTP 오류 코드 403)
- BadRequest (HTTP 오류 코드 400)
