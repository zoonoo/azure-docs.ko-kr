---
title: 진단 및 Azure Cosmos DB를 사용 하는 경우 쿼리 문제 해결
description: 식별, 진단 및 Azure Cosmos DB SQL 쿼리 문제를 해결 하는 방법에 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835830"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 쿼리 성능 문제 해결
이 문서에서는 식별, 진단 및 Azure Cosmos DB SQL 쿼리 문제를 해결 하는 방법을 설명 합니다. Azure Cosmos DB 쿼리에 대 한 최적의 성능을 달성 하려면 다음 문제 해결 단계를 수행 합니다. 

## <a name="collocate-clients-in-same-azure-region"></a>동일한 Azure 지역에 클라이언트 배치 
호출하는 애플리케이션이 프로비전된 Azure Cosmos DB 엔드포인트와 동일한 Azure 지역 내에 있도록 하면 가능한 최저 대기 시간을 얻을 수 있습니다. 사용 가능한 지역 목록은 참조 하세요 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/#services) 문서.

## <a name="check-consistency-level"></a>일관성 수준 확인
[일관성 수준](consistency-levels.md) 성능 및 비용에 영향을 줄 수 있습니다. 주어진된 시나리오에 대 한 적절 한 일관성 수준 인지 확인 합니다. 자세한 내용은 참조 하세요 [일관성 수준 선택](consistency-levels-choosing.md)합니다.

## <a name="log-query-metrics"></a>로그 쿼리 메트릭
사용 하 여 `QueryMetrics` 느리거나 비용이 많이 드는 쿼리 문제를 해결 하려면. 

  * 설정할 `FeedOptions.PopulateQueryMetrics = true` 필요가 `QueryMetrics` 응답에서 합니다.
  * `QueryMetrics` 클래스에 오버 로드 `.ToString()` 의 문자열 표현을 가져오기 위해 호출할 수 있는 함수 `QueryMetrics`합니다. 
  * 특히 다음 정보를 파생 시키는 메트릭을 활용할 수 있습니다. 
  
      * 여부 쿼리 파이프라인의 특정 구성 요소 길어서 비정상적으로 완료 (수백 밀리초 이상의의 순서로). 

          * 살펴볼 `TotalExecutionTime`합니다.
          * 경우는 `TotalExecutionTime` 클라이언트 쪽 또는 네트워크에서 시간이 소요 되는지 다음 쿼리의 종단 간 실행 시간 보다 작습니다. 다시 확인 하는 클라이언트와 Azure 지역에 함께 배치 됩니다.
      
      * 문서에서 거짓 긍정 있었는지 여부를 (출력 문서 수는 훨씬 적은 검색 문서 개수 보다) 하는 경우 분석 합니다.  

          * 살펴볼 `Index Utilization`합니다.
          * `Index Utilization` = (반환 된 문서 수 / 로드 된 수의 문서)
          * 반환 된 문서 수를 로드 하는 수보다 훨씬 적은 경우 거짓 긍정 분석 되 고 됩니다.
          * 좁은 필터를 사용 하 여 검색 되는 문서의 수를 제한 합니다.  

      * 개별 왕복 fared (참조를 `Partition Execution Timeline` 의 문자열 표현에서 `QueryMetrics`). 
      * 여부 쿼리 높은 요청 요금을 사용 합니다. 

자세한 내용은 참조 하세요 [SQL 쿼리 실행 메트릭을 가져오는 방법을](profile-sql-api-query.md) 문서.
      
## <a name="tune-query-feed-options-parameters"></a>쿼리 피드 옵션 매개 변수 조정 
요청을 통해 쿼리 성능을 조정할 수 있습니다 [피드 옵션](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) 매개 변수입니다. 설정 된 옵션 아래:

  * 설정 `MaxDegreeOfParallelism` 를-1와 다른 값에서 성능을 비교 합니다. 
  * 설정 `MaxBufferedItemCount` 를-1와 다른 값에서 성능을 비교 합니다. 
  * 설정 `MaxItemCount` -1입니다.

성능의 다른 값을 비교할 때 2, 4, 8, 16, 등과 같은 값을 사용해 보십시오.
 
## <a name="read-all-results-from-continuations"></a>연속 작업에서 모든 결과 읽습니다.
모든 결과 얻을 수 없는 경우에 연속 작업을 완벽 하 게 드레이닝 해야 합니다. 즉, 연속 토큰 생성 하기 위해 더 많은 문서에 있는 동안 결과 읽을 유지 합니다.

드레이닝 완벽 하 게 다음 패턴 중 하나를 사용 하 여 수행할 수 있습니다.

  * 연속 비어 있지 않은 동안 결과 처리를 계속 합니다.
  * 쿼리는 더 많은 결과 처리를 계속 합니다. 

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

## <a name="choose-system-functions-that-utilize-index"></a>인덱스를 활용 하는 시스템 함수를 선택 합니다.
식이 문자열 값의 범위에 번역할 수, 하는 경우 다음이 인덱스를 활용할 수; 그렇지 않으면 불가능합니다. 

인덱스를 활용할 수 있는 문자열 함수 목록은 다음과 같습니다. 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING (str_expr, num_expr, num_expr) 첫 번째 num_expr 0 이면만 str_expr = 
    
    몇 가지 쿼리 예는 다음과 같습니다. 
    
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

  * 필터에 대 한 시스템 함수 방지 (또는 WHERE 절) 하는 인덱스에서 제공 하지 않습니다. 이러한 시스템 함수의 예로 Contains, 위, 아래 있습니다.
  * 가능한 경우 파티션 키에서 필터를 사용하는 쿼리를 작성합니다.
  * 고성능을 달성 하기 위해 쿼리 필터에 대/소문자를 호출 하지 마세요. 대신 삽입 시 값의 대/소문자를 정규화 합니다. 각 값에 대 한 원하는 대/소문자를 사용 하 여 값을 삽입 또는 원래 값과 원하는 대/소문자를 사용 하 여 값을 삽입 합니다. 

    예:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    이 경우 저장소 "JOE"는 대문자로 시작 또는 "JOE" 및 "Joe" 원래 값을 저장 합니다. 
    
    JSON 데이터 대/소문자를 정규화 하는 경우 쿼리가 됩니다.
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    두 번째 쿼리는 "JOE" 값을 비교 하기 위해 각 값에서 변환을 수행 하지 않아도으로 성능이 향상 됩니다.

자세한 시스템 함수에 대 한 세부 정보 참조 [시스템 함수](sql-query-system-functions.md) 문서.

## <a name="check-indexing-policy"></a>인덱싱 정책 확인
확인 하려면 현재 [인덱싱 정책](index-policy.md) 적합 합니다.

  * 더 빠른 읽기에 대 한 쿼리에서 사용 되는 모든 JSON 경로 인덱싱 정책에 포함 되도록 합니다.
  * 쿼리에서 사용 되지는 자세한 고성능 쓰기에 대 한 경로 제외 합니다.

자세한 내용은 참조 하세요 [하는 방법에 인덱싱 정책 관리](how-to-manage-indexing-policy.md) 문서.

## <a name="spatial-data-check-ordering-of-points"></a>공간 데이터. 요소의 순서를 확인 합니다.
다각형 내의 점을 시계 반대 방향 순서로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.

## <a name="optimize-join-expressions"></a>조인 식 최적화
`JOIN` 식 대규모 간 제품으로 확장할 수 있습니다. 때 가능한 한 작은 검색에 대 한 쿼리 필터를 더 좁은 통 공간입니다.

다중 값 하위 쿼리를 최적화할 수 있습니다 `JOIN` 식에서 모든 크로스 조인 후가 아니라 각 선택 다 식 뒤에 조건자를 푸시하는 `WHERE` 절. 자세한 예제를 참조 하세요 [조인 식 최적화](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) 문서.

## <a name="optimize-order-by-expressions"></a>ORDER BY 식 최적화 
`ORDER BY` 필드의 인덱스 정책에 포함 되지 않음 또는 스파스 경우 쿼리 성능이 저하 될 수 있습니다.

  * 시간 등 스파스 필드에 대 한 필터를 사용 하 여 최대한 검색 공간을 줄입니다. 
  * 단일 속성에 대 한 `ORDER BY`, 인덱스 정책에 속성을 포함 합니다. 
  * 여러 속성에 대 한 `ORDER BY` 식을 정의 된 [복합 인덱스](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) 정렬 하 고 필드에 합니다.  

## <a name="many-large-documents-being-loaded-and-processed"></a>큰 문서 로드 및 처리
쿼리에서 필요한 Ru와 시간만에 종속 되지 않는 응답의 크기, 쿼리 처리 파이프라인에 의해 수행 되는 작업에 종속 됩니다. 시간 및 Ru는 양의 전체 쿼리 처리 파이프라인에서 수행 된 작업을 사용 하 여 비례적으로 증가 합니다. 큰 문서에 대 한 더 많은 작업이 수행 됩니다, 그리고 따라서 더 많은 시간과 Ru는 로드 하 고 큰 문서를 처리 해야 합니다.

## <a name="low-provisioned-throughput"></a>프로 비전 된 처리량이 낮은
프로 비전 된 처리량 워크 로드를 처리할 수 있는지 확인 합니다. 영향을 받는 컬렉션에 대 한 RU 예산 늘리기

## <a name="try-upgrading-to-the-latest-sdk-version"></a>최신 SDK 버전으로 업데이트 해 보세요.
최신 SDK 참조를 확인 하려면 [SDK 다운로드 및 릴리스 정보](sql-api-sdk-dotnet.md) 문서.

## <a name="next-steps"></a>다음 단계
쿼리당 Ru를 측정, 실행 통계 등 사용자 쿼리를 튜닝 하는 방법은 아래 문서를 참조 하세요.

* [.NET SDK를 사용 하 여 SQL 쿼리를 실행 하는 메트릭 가져오기](profile-sql-api-query.md)
* [Azure Cosmos DB와 함께 쿼리 성능 튜닝](sql-api-sql-query-metrics.md)
* [.NET SDK에 대 한 성능 팁](performance-tips.md)