---
title: Azure Cosmos DB Gremlin 제한
description: 그래프 엔진의 런타임 제한 사항에 대 한 참조 설명서
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911097"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 제한
이 문서에서는 Azure Cosmos DB Gremlin 엔진의 제한에 대해 설명 하 고 고객이 탐색에 영향을 미칠 수 있는 방법에 대해 설명 합니다.

Cosmos DB Gremlin는 Cosmos DB 인프라를 기반으로 구축 되므로 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) 의 모든 한도가 여전히 적용 됩니다. 

## <a name="limits"></a>제한

Gremlin limit에 도달 하면 제한 오류를 나타내는 **x m m-m** s-429을 사용 하 여 순회가 취소 됩니다.

**Resource**    | **기본 제한** | **설명**
--- | --- | ---
*요청당 메모리* | **2GB** | 처리하는 동안 한 요청에서 사용할 수 있는 최대 메모리입니다. 대량 데이터 집합을 계산 해야 하는 요청은 추가 메모리를 사용 합니다. 이러한 제한을 초과 하거나 OLAP 솔루션을 사용 하지 않도록 더 작은 데이터 집합에 대 한 범위 지정 요청을 고려 합니다.
*스크립트 길이* | **64KB** | 요청당 Gremlin 순회 스크립트의 최대 길이입니다.
*연산자 깊이* | **400** |  순회의 총 고유 단계 수입니다. 예 ```g.V().out()``` 를 들어의 연산자 개수는 2입니다. V () 및 out ()에 ```g.V('label').repeat(out()).times(100)``` 는 연산자 깊이가 3입니다. 는 연산자에 대 ```.times(100)``` ```.repeat()``` 한 매개 변수 이므로 V (), repeat () 및 out ()입니다.
*병렬 처리 수준* | **32** | 단일 요청에서 스토리지 레이어에 쿼리한 스토리지 파티션의 최대 수입니다. 수백 개의 파티션이 있는 그래프는이 제한의 영향을 받습니다.
*반복 제한* | **32** | ```.repeat()``` 연산자가 실행할 수 있는 최대 반복 횟수입니다. 대부분의 경우 ```.repeat()``` 단계의 각 반복은 너비 우선 순회를 실행 합니다. 즉, 모든 트래버스는 꼭 짓 점 사이의 최대 32 홉으로 제한 됩니다.
*순회 시간 제한* | **30초** | 이 시간을 초과 하면 순회가 취소 됩니다. Cosmos DB Graph는 대부분의 순회가 밀리초 내에 완료되는 OLTP 데이터베이스입니다. Cosmos DB Graph에서 OLAP 쿼리를 실행 하려면 [그래프 데이터 프레임](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) 및 [Cosmos DB Spark 커넥터](https://github.com/Azure/azure-cosmosdb-spark)를 사용 하 여 [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) 를 사용 합니다.
*조건자 제한* | **20** | 단일 꼭짓점 또는 에지에 적용되는 ```.has()``` 또는 ```.hasNot()``` 단계의 수입니다. 이 제한에 도달하면 애플리케이션에서 ```The SQL query exceeded the maximum number of joins. The allowed limit is 20``` 오류가 발생합니다. 팀에서이 한도를 리프트 하기 위해 작업 하 고 있으므로 일시적으로 불편 합니다. 
*유휴 연결 시간 제한* | **5 시간** | 그래프 서버는 트래픽 없이 websocket 연결을 열린 상태로 유지 합니다. TCP keep-alive 패킷 또는 HTTP 연결 유지 요청은이 제한을 초과 하는 연결 수명을 연장 하지 않지만, 전송 되지 않은 경우 기본 Azure 인프라에서 더 빨리 연결을 닫을 수 있습니다. Cosmos DB Graph 엔진은 실행 중인 Gremlin 순회가 없는 경우 유휴 상태로 간주 합니다.
*시간당 리소스 토큰* | **100** | Gremlin 클라이언트가 한 지역의 Gremlin 계정에 연결 하는 데 사용 하는 고유 리소스 토큰 수입니다. 응용 프로그램이 매시간 고유한 토큰 제한을 초과 하 `"Exceeded allowed resource token limit of 100 that can be used concurrently"` 는 경우는 다음 인증 요청 시 반환 됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure Cosmos DB Gremlin 응답 헤더](gremlin-headers.md) 
* [Gremlin를 사용 하 여 리소스 토큰 Azure Cosmos DB](how-to-use-resource-tokens-gremlin.md)
