---
title: Azure Cosmos DB에서 요청 비용 최적화
description: 이 문서에서는 Azure Cosmos DB에 대 한 요청을 실행 하는 경우 비용을 최적화 하는 방법을 설명 합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 3f4c33a88d9a8fbf2c3d64135d93da54cf75fab3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097509"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 요청 비용 최적화
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 읽기 및 쓰기 요청을 [요청 단위로](request-units.md) 변환 하는 방법과 이러한 요청의 비용을 최적화 하는 방법을 설명 합니다. 읽기 작업에는 지점 읽기 및 쿼리가 포함 됩니다. 쓰기 작업에는 항목의 삽입, 바꾸기, 삭제 및 upsert 포함 됩니다.

Azure Cosmos DB는 컨테이너 내의 항목에 대해 작동 하는 다양 한 데이터베이스 작업 집합을 제공 합니다. 이러한 작업 각각과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 생각 하 고 관리 하는 대신 요청을 처리 하는 다양 한 데이터베이스 작업을 수행 하는 데 필요한 리소스에 대 한 단일 측정값으로 요청 단위 ()를 생각해 볼 수 있습니다.

## <a name="measuring-the-ru-charge-of-a-request"></a>요청에 대 한 비용 측정

실제 비용을 이해 하 고 최적화의 효과를 평가 하기 위해 요청에 대 한 비용을 측정 하는 것이 중요 합니다. Azure Portal를 사용 하거나 Sdk 중 하나를 통해 Azure Cosmos DB에서 다시 보낸 응답을 검사 하 여이 비용을 페치할 수 있습니다. 이를 수행 하는 방법에 대 한 자세한 지침은 [Azure Cosmos DB에서 요청 단위 요금 청구](find-request-unit-charge.md) 를 참조 하세요.

## <a name="reading-data-point-reads-and-queries"></a>데이터 읽기: point reads 및 queries

Azure Cosmos DB의 읽기 작업은 일반적으로 다음과 같이 사용 측면에서 가장 빠르고 효율적으로 정렬 됩니다.  

* Point reads (단일 항목 ID 및 파티션 키에 대 한 키/값 조회).
* 단일 파티션 키 내에 필터 절이 있는 쿼리
* 속성에 대해 같음 또는 범위 필터 절이 없는 쿼리
* 필터가 없는 쿼리

### <a name="role-of-the-consistency-level"></a>일관성 수준의 역할

**강력** 또는 **제한 된 부실** [일관성 수준](consistency-levels.md)중 하나를 사용 하는 경우 읽기 작업 (지점 읽기 또는 쿼리)의 사용 비용은 두 배가 됩니다.

### <a name="point-reads"></a>지점 읽기

사용 된 일관성 수준 외에도 읽기의 추가 요금에 영향을 주는 유일한 요인은 검색 된 항목의 크기입니다. 다음 표에서는 1kb 및 100 KB 크기의 항목에 대 한 포인트 읽기 비용을 보여 줍니다.

| **항목 크기** | **한 지점에서 읽은 비용** |
| --- | --- |
| 1KB | 1RU |
| 100KB | 10RU |

Point reads (항목 ID의 키/값 조회)는 가장 효율적인 읽기 이므로 가능한 경우 항목 ID에 의미 있는 값이 있는지 확인 해야 합니다 .이 경우 쿼리 대신 point 읽기가 있는 항목을 가져올 수 있습니다.

### <a name="queries"></a>쿼리

쿼리의 요청 단위는 여러 가지 요인에 따라 달라집니다. 예를 들어, 로드/반환 되는 Azure Cosmos 항목의 수, 인덱스에 대 한 조회 수, 쿼리 컴파일 시간 등에 대 한 정보를 표시 합니다. Azure Cosmos DB는 동일한 데이터에 대해 실행될 경우 동일한 쿼리가 항상 반복 실행에서 동일한 요청 단위 수를 사용하도록 보장합니다. 쿼리 실행 메트릭을 사용하는 쿼리 프로필은 요청 단위가 소비되는 적절한 방식을 제시합니다.  

일부 경우에 페이징된 쿼리 실행에서 200개 및 429개의 응답 시퀀스와 가변 요청 단위를 볼 수 있습니다. 쿼리가 사용 가능한 RU에 따라 가능한 한 빠르게 실행되기 때문입니다. 쿼리 실행이 서버와 클라이언트 간에 여러 페이지/왕복으로 구분되는 것을 볼 수도 있습니다. 예를 들어, 10,000개 항목이 여러 페이지로 반환될 수 있으며, 해당 페이지에 대해 계산 기준으로 요금이 부과됩니다. 이러한 페이지에서 합계를 계산하는 경우 전체 쿼리의 경우와 동일한 수의 RU를 가져와야 합니다.

#### <a name="metrics-for-troubleshooting-queries"></a>쿼리 문제 해결에 대 한 메트릭

쿼리에서 사용 하는 성능 및 처리량 (사용자 정의 함수 포함)은 대부분 함수 본문에 따라 다릅니다. UDF에서 쿼리 실행이 얼마나 소요 되는 시간과 사용 된 RUs 수를 확인 하는 가장 쉬운 방법은 쿼리 메트릭을 사용 하도록 설정 하는 것입니다. .NET SDK를 사용 하는 경우 SDK에서 반환 된 샘플 쿼리 메트릭은 다음과 같습니다.

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>쿼리 최적화 비용에 대한 모범 사례 

비용에 대한 쿼리를 최적화하는 경우 다음 모범 사례를 고려합니다.

* **여러 엔터티 형식을 공동으로 배치**

   단일 컨테이너 또는 소수의 컨테이너 내에 여러 엔터티 형식을 공동으로 배치합니다. 이 방법은 가격 책정 관점에서 뿐만 아니라 쿼리 실행 및 트랜잭션 측면에서도 이점이 있습니다. 쿼리의 범위는 단일 컨테이너로 지정되고, 저장 프로시저/트리거를 통해 여러 레코드에서 수행되는 원자성 트랜잭션의 범위는 단일 컨테이너 내의 파티션 키로 지정됩니다. 같은 컨테이너 내에서 엔터티를 공동으로 배치하면 레코드 간 관계를 해석하는 데 필요한 네트워크 왕복 수를 줄일 수 있습니다. 이를 통해 엔드투엔드 성능이 향상되고, 더 큰 데이터 세트의 여러 레코드에 대해 원자성 트랜잭션이 활성화되며 결과적으로 비용이 절감됩니다. 일반적으로 기존 애플리케이션을 마이그레이션하며 코드를 변경하고 싶지 않기 때문에 단일 컨테이너 또는 더 작은 수의 컨테이너에 여러 엔터티 형식을 공동으로 배치하는 것이 어려우면 데이터베이스 수준에서 처리량을 프로비전하는 것이 좋습니다.  

* **낮은 요청 단위/초 사용량 측정 및 튜닝**

   쿼리의 복잡성은 작업에 사용되는 RU(요청 단위)의 수에 영향을 줍니다. 조건자의 수, 조건자의 특성, UDF 수 및 원본 데이터 세트의 크기 등의 모든 요소는 쿼리 작업의 비용에 영향을 줍니다. 

Azure Cosmos DB는 프로 비전 된 처리량 모델을 사용 하 여 처리량 및 대기 시간 측면에서 예측 가능한 성능을 제공 합니다. 프로비전된 처리량은 초당 [요청 단위](request-units.md) 또는 RU/초로 표시됩니다. RU(요청 단위)는 CPU, 메모리, IO 등과 같이 요청을 수행하는 데 필요한 컴퓨팅 리소스에 대한 논리적 추상화입니다. 프로비전된 처리량(RU)은 따로 구분되며, 예측 가능한 처리량 및 대기 시간을 제공하기 위해 컨테이너 또는 데이터베이스에 전용으로 사용됩니다. 프로비전된 처리량을 사용하면 Azure Cosmos DB는 규모에 관계없이, 예측 가능하고 일관된 성능, 보장된 낮은 대기 시간 및 고가용성을 제공할 수 있습니다. 요청 단위는 애플리케이션에 필요한 리소스의 수를 보다 간편하게 파악할 수 있도록 하는 정규화된 통화를 나타냅니다.

요청 헤더에 반환된 요청 비용은 지정된 쿼리의 비용을 나타냅니다. 예를 들어, 쿼리가 1000개의 1KB 항목을 반환하는 경우 작업 비용은 1000입니다. 따라서 1초 이내에 서버는 후속 요청의 속도를 제한하기 전에 이러한 두 가지 요청만 인식합니다. 자세한 내용은 [요청 단위](request-units.md) 문서와 요청 단위 계산기를 참조하세요.

## <a name="writing-data"></a>데이터 쓰기

항목을 작성 하는 데 드는 비용은 다음에 따라 달라 집니다.

- 항목 크기입니다.
- [인덱싱 정책](index-policy.md) 에서 적용 되며 인덱싱되는 데 필요한 속성의 수입니다.

5 개 미만의 속성을 포함 하는 1kb 항목을 삽입 하 여 5 개의 RUs를 기준으로 비용을 인덱싱합니다. 항목을 삽입 하는 데 필요한 두 번의 요금으로 항목을 바꿉니다.

### <a name="optimizing-writes"></a>쓰기 최적화

쓰기 작업의 비용을 최적화 하는 가장 좋은 방법은 항목 크기와 인덱싱되는 속성 수를 오른쪽으로 조정 하는 것입니다.

- 매우 큰 항목을 Azure Cosmos DB에 저장 하면 높은 수준의 요금이 발생 하며이를 방지 패턴으로 간주할 수 있습니다. 특히 쿼리 하지 않아도 되는 이진 콘텐츠나 많은 텍스트를 저장 하지 마세요. [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 에 이러한 종류의 데이터를 저장 하 고 Azure Cosmos DB 쓰는 항목에 Blob에 대 한 참조 (또는 링크)를 저장 하는 것이 가장 좋습니다.
- 쿼리를 통해 필터링 되는 속성만 인덱싱하는 인덱싱 정책 최적화는 쓰기 작업에서 사용 하는 RUs에서 큰 차이를 만들 수 있습니다. 새 컨테이너를 만들 때 기본 인덱싱 정책은 항목에 있는 모든 속성 및 각 속성을 인덱싱합니다. 이는 개발 작업에 대 한 좋은 기본값 이지만 프로덕션으로 전환 하거나 작업에서 상당한 트래픽을 수신 하기 시작 하는 경우에는 인덱싱 정책을 다시 평가 하 고 [사용자 지정](how-to-manage-indexing-policy.md) 하는 것이 좋습니다.

데이터 대량 수집을 수행 하는 경우 이러한 작업의 과도 한 사용을 최적화 하도록 설계 된 [Azure Cosmos DB 대량 실행자 라이브러리](bulk-executor-overview.md) 를 사용 하는 것이 좋습니다. 필요에 따라 동일한 라이브러리를 기반으로 하는 [Azure Data Factory](../data-factory/introduction.md) 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
* [Azure Cosmos DB 예약 된 용량](cosmos-db-reserved-capacity.md) 에 대 한 자세한 정보
