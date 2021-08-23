---
title: Azure Cosmos DB Gremlin 제한
description: Graph 엔진의 런타임 제한 사항에 대한 참조 설명서
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 48fc687404d1a57cf17f9b6e90dc8146bbc4b996
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528318"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 제한
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

이 문서에서는 Azure Cosmos DB Gremlin 엔진의 제한과 고객 순회에 미치는 영향을 설명합니다.

Cosmos DB Gremlin은 Cosmos DB 인프라 위에 빌드되었습니다. 이 때문에 [Azure Cosmos DB 서비스 제한](../concepts-limits.md)에서 설명한 모든 제한이 계속 적용됩니다.

## <a name="limits"></a>제한

Gremlin 제한에 도달하면 제한 오류를 나타내는 **x-ms-status-code** 429가 발생하고 순회가 취소됩니다. 자세한 내용은 [Gremlin 서버 응답 헤더](gremlin-limits.md)를 참조하세요.

**리소스**    | **기본 제한** | **설명**
--- | --- | ---
*스크립트 길이* | **64KB** | 요청당 Gremlin 순회 스크립트의 최대 길이입니다.
*연산자 깊이* | **400** |  순회의 총 고유 단계 수입니다. 예를 들어 ```.times(100)```는 ```.repeat()``` 연산자의 매개 변수이므로 ```g.V().out()```의 연산자 수는 V() 및 out() 등 2개이고, ```g.V('label').repeat(out()).times(100)```의 연산자 깊이는 V(), repeat(), out()인 등 3개입니다.
*병렬 처리 수준* | **32** | 단일 요청에서 스토리지 레이어에 쿼리한 스토리지 파티션의 최대 수입니다. 수백 개의 파티션이 있는 그래프는 이 제한의 영향을 받습니다.
*반복 제한* | **32** | ```.repeat()``` 연산자가 실행할 수 있는 최대 반복 횟수입니다. 대부분의 경우 ```.repeat()``` 단계를 반복할 때마다 폭 우선 순회가 실행됩니다. 즉, 모든 순회는 꼭짓점 간 최대 32홉으로 제한됩니다.
*순회 시간 제한* | **30초** | 이 시간을 초과하면 순회가 취소됩니다. Cosmos DB Graph는 대부분의 순회가 밀리초 내에 완료되는 OLTP 데이터베이스입니다. Cosmos DB Graph에서 OLAP 쿼리를 실행하려면 [그래프 데이터 프레임](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) 및 [Cosmos DB Spark 커넥터](https://github.com/Azure/azure-cosmosdb-spark)와 함께 [Apache Spark](https://azure.microsoft.com/services/cosmos-db/)를 사용합니다.
*유휴 연결 시간 제한* | **1시간** | Gremlin 서비스가 유휴 websocket 연결을 열어 두는 시간입니다. TCP 연결 유지 패킷 또는 HTTP 연결 유지 요청은 이 한도를 초과하여 연결 수명을 연장하지 않습니다. Cosmos DB Graph 엔진은 실행 중인 활성 Gremlin 요청이 없는 경우 websocket 연결을 유휴 상태로 간주합니다.
*시간당 리소스 토큰* | **100** | Gremlin 클라이언트가 한 지역의 Gremlin 계정에 연결하는 데 사용하는 고유 리소스 토큰 수입니다. 애플리케이션이 시간당 고유 토큰 한도를 초과하면 다음 인증 요청 시 `"Exceeded allowed resource token limit of 100 that can be used concurrently"`가 반환됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure Cosmos DB Gremlin 응답 헤더](gremlin-headers.md)
* [Gremlin을 사용한 Azure Cosmos DB 리소스 토큰](how-to-use-resource-tokens-gremlin.md)