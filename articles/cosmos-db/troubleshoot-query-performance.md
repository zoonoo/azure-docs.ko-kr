---
title: Azure Cosmos DB를 사용 하는 경우 쿼리 문제 해결
description: Azure Cosmos DB SQL 쿼리 문제를 식별, 진단 및 해결 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 1859fa8f71b5c4c44d6e5da1b6a36ca9d9399516
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444717"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 쿼리 성능 문제 해결
이 문서에서는 Azure Cosmos DB SQL 쿼리 문제를 식별, 진단 및 해결 하는 방법을 설명 합니다. Azure Cosmos DB 쿼리에 대 한 최적의 성능을 얻으려면 아래 문제 해결 단계를 수행 합니다. 

## <a name="collocate-clients-in-same-azure-region"></a>동일한 Azure 지역의 배치 클라이언트 
호출하는 애플리케이션이 프로비전된 Azure Cosmos DB 엔드포인트와 동일한 Azure 지역 내에 있도록 하면 가능한 최저 대기 시간을 얻을 수 있습니다. 사용 가능한 지역 목록은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/#services) 문서를 참조 하세요.

## <a name="check-consistency-level"></a>일관성 수준 확인
[일관성 수준은](consistency-levels.md) 성능 및 요금에 영향을 줄 수 있습니다. 일관성 수준이 지정 된 시나리오에 적합 한지 확인 합니다. 자세한 내용은 [일관성 수준 선택](consistency-levels-choosing.md)을 참조 하세요.

## <a name="log-the-executed-sql-query"></a>실행 된 SQL 쿼리 기록 

실행 된 SQL 쿼리를 저장소 계정 또는 진단 로그 테이블에 기록할 수 있습니다. [진단 로그를 통해 SQL 쿼리 로그](cosmosdb-monitor-resource-logs.md) 를 사용 하 여 선택한 저장소 계정에 난독 처리 된 쿼리를 기록할 수 있습니다. 이를 통해 로그를 확인 하 고 더 높은 RUs를 사용 하는 쿼리를 찾을 수 있습니다. 나중에 작업 ID를 사용 하 여 QueryRuntimeStatistics의 실제 쿼리를 일치 시킬 수 있습니다. 이 쿼리는 보안 목적 및 쿼리 매개 변수 이름에 대해 난독 처리 되며 where 절의 해당 값은 실제 이름 및 값과 다릅니다. 저장소 계정에 대 한 로깅을 사용 하 여 실행 된 쿼리의 장기 보존을 유지할 수 있습니다.  

## <a name="log-query-metrics"></a>로그 쿼리 메트릭

`QueryMetrics`를 사용 하 여 느리거나 비용이 많이 드는 쿼리 문제를 해결할 수 있습니다. 

  * 응답에 `QueryMetrics`를 포함 하도록 `FeedOptions.PopulateQueryMetrics = true` 설정 합니다.
  * `QueryMetrics` 클래스에는 `QueryMetrics`의 문자열 표현을 가져오기 위해 호출할 수 있는 오버 로드 된 `.ToString()` 함수가 있습니다. 
  * 메트릭을 활용 하 여 다음과 같은 정보를 얻을 수 있습니다. 
  
      * 쿼리 파이프라인의 특정 구성 요소를 완료 하는 데 비정상적으로 오랜 시간이 소요 되었는지 여부 (수백 밀리초 이상). 

          * `TotalExecutionTime`를 확인 합니다.
          * 쿼리의 `TotalExecutionTime`가 종단 간 실행 시간 보다 낮으면 클라이언트 쪽 또는 네트워크에서 시간이 소요 됩니다. 클라이언트 및 Azure 지역이 배치 된를 다시 한 번 확인 합니다.
      
      * 분석 된 문서에 가양성이 있는지 여부 (출력 문서 수가 검색 된 문서 수보다 훨씬 작은 경우).  

          * `Index Utilization`를 확인 합니다.
          * `Index Utilization` = (반환 된 문서 수/로드 된 문서 수)
          * 반환 된 문서 수가 로드 된 수보다 훨씬 작은 경우 거짓 긍정이 분석 됩니다.
          * 더 좁은 필터를 사용 하 여 검색 되는 문서 수를 제한 합니다.  

      * 개별 왕복 fared (`QueryMetrics`문자열 표현의 `Partition Execution Timeline` 참조). 
      * 쿼리가 높은 요청 요금을 소비 했는지 여부입니다. 

자세한 내용은 [SQL 쿼리 실행 메트릭을 가져오는 방법](profile-sql-api-query.md) 문서를 참조 하세요.
      
## <a name="tune-query-feed-options-parameters"></a>쿼리 피드 옵션 매개 변수 조정 
요청의 [피드 옵션](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) 매개 변수를 통해 쿼리 성능을 조정할 수 있습니다. 아래 옵션을 설정해 봅니다.

  * 먼저 `MaxDegreeOfParallelism`를-1로 설정한 다음 다른 값에 대해 성능을 비교 합니다. 
  * 먼저 `MaxBufferedItemCount`를-1로 설정한 다음 다른 값에 대해 성능을 비교 합니다. 
  * `MaxItemCount`를-1로 설정 합니다.

다른 값의 성능을 비교할 때 2, 4, 8, 16 등의 값을 사용해 보세요.
 
## <a name="read-all-results-from-continuations"></a>연속 작업의 모든 결과 확인
모든 결과를 얻지 않았다고 생각되면 연속 작업을 완전히 드레이닝해야 합니다. 즉, 연속 토큰에 생성되는 문서가 많을 경우 계속해서 결과를 읽습니다.

다음 패턴 중 하나를 사용하여 완전히 드레이닝할 수 있습니다.

  * 연속 작업이 비어 있지 않은 상태에서 계속 해 서 결과를 처리 합니다.
  * 쿼리가 더 많은 결과를 포함 하는 동안 처리를 계속 합니다. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>인덱스를 활용하는 시스템 함수 선택
식을 문자열 값 범위로 변환할 수 있는 경우 인덱스를 사용할 수 있으며 그렇지 않은 경우에는 사용할 수 없습니다. 

인덱스를 활용할 수 있는 문자열 함수의 목록은 다음과 같습니다. 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    다음은 몇 가지 쿼리 예입니다. 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * 인덱스에서 제공 하지 않는 필터 (또는 WHERE 절)의 시스템 함수를 사용 하지 마십시오. 이러한 시스템 함수의 예로는 Contains, Upper, Lower가 있습니다.
  * 가능한 경우 파티션 키에서 필터를 사용하는 쿼리를 작성합니다.
  * 성능 쿼리를 얻기 위해 필터에서 위쪽/아래쪽 호출을 수행 하지 않습니다. 대신 삽입 시 값의 대/소문자를 표준화 합니다. 각 값에 대해 원하는 대/소문자를 사용 하 여 값을 삽입 하거나 원래 값과 원하는 대/소문자를 사용 하 여 값을 삽입 합니다. 

    예:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    이 경우 "JOE"를 대문자로 표기 하거나 "Joe"를 원래 값과 "JOE"로 저장 합니다. 
    
    JSON 데이터의 대/소문자를 정규화 하는 경우 쿼리는 다음과 같이 됩니다.
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    두 번째 쿼리는 값을 "JOE"와 비교 하기 위해 각 값에 대해 변환 작업을 수행 하지 않아도 되므로 성능이 더 우수 합니다.

더 많은 시스템 함수에 대 한 자세한 내용은 [시스템 함수](sql-query-system-functions.md) 문서를 참조 하세요.

## <a name="check-indexing-policy"></a>인덱싱 정책 확인
다음을 수행하여 현재 [인덱싱 정책](index-policy.md)이 최적 상태인지 확인합니다.

  * 더 빠른 읽기를 위해 쿼리에 사용 되는 모든 JSON 경로가 인덱싱 정책에 포함 되어 있는지 확인 합니다.
  * 더 높은 성능의 쓰기에 대 한 쿼리에서 사용 되지 않는 경로를 제외 합니다.

자세한 내용은 [인덱싱 정책을 관리 하는 방법](how-to-manage-indexing-policy.md) 문서를 참조 하세요.

## <a name="spatial-data-check-ordering-of-points"></a>공간 데이터: 점의 순서 확인
다각형 내의 점을 시계 반대 방향 순서로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.

## <a name="optimize-join-expressions"></a>조인 식 최적화
`JOIN` 식은 많은 교차곱으로 확장 될 수 있습니다. 가능 하면 더 좁은 필터를 통해 더 작은 검색 공간에 대해 쿼리 합니다.

다중 값 하위 쿼리는 `WHERE` 절의 모든 크로스 조인이 아니라 각 select-many 식 뒤에 조건자를 푸시하여 `JOIN` 식을 최적화할 수 있습니다. 자세한 예제는 [조인 식 최적화](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) 문서를 참조 하세요.

## <a name="optimize-order-by-expressions"></a>ORDER BY 식 최적화 
필드가 스파스 이거나 인덱스 정책에 포함 되지 않은 경우 쿼리 성능이 저하 될 수 있습니다. `ORDER BY`

  * 시간과 같은 스파스 필드의 경우 필터를 사용 하 여 검색 공간을 최대한 줄입니다. 
  * 단일 속성 `ORDER BY`의 경우 인덱스 정책에 속성을 포함 합니다. 
  * 여러 속성 `ORDER BY` 식의 경우 정렬할 필드에 대해 [복합 인덱스](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) 를 정의 합니다.  

## <a name="many-large-documents-being-loaded-and-processed"></a>많은 문서를 로드 하 고 처리 하 고 있습니다.
쿼리에 필요한 시간 및 RUs는 응답의 크기에 따라 달라 지는 것이 아니라 쿼리 처리 파이프라인에서 수행 하는 작업에 종속 됩니다. 시간 및 RUs는 전체 쿼리 처리 파이프라인에서 수행 되는 작업량에 비례하여 증가 합니다. 규모가 많은 문서에 대해 더 많은 작업이 수행 되므로 많은 시간과 RUs가 많은 문서를 로드 하 고 처리 해야 합니다.

## <a name="low-provisioned-throughput"></a>프로 비전 된 처리량 낮음
프로 비전 된 처리량이 워크 로드를 처리할 수 있는지 확인 합니다. 영향을 받는 컬렉션에 대 한 r u r a r을 늘립니다.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>최신 SDK 버전으로 업그레이드 해 보세요.
최신 SDK를 확인 하려면 [Sdk 다운로드 및 릴리스 정보](sql-api-sdk-dotnet.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계
쿼리 별 RUs를 측정 하는 방법, 쿼리를 튜닝 하기 위한 실행 통계 가져오기 등의 문서를 참조 하세요.

* [.NET SDK를 사용하여 SQL 쿼리 실행 메트릭 가져오기](profile-sql-api-query.md)
* [Azure Cosmos DB와 함께 쿼리 성능 튜닝](sql-api-sql-query-metrics.md)
* [.NET SDK용 성능 팁](performance-tips.md)
