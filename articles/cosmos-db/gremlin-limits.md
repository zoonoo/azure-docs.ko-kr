---
title: Azure 코스모스 DB 그렘린의 한계
description: 그래프 엔진의 런타임 제한에 대한 참조 문서
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029855"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 제한
이 문서에서는 Azure Cosmos DB Gremlin 엔진의 한계에 대해 설명하고 고객 탐색에 미치는 영향을 설명합니다.

코스모스 DB 그렘린은 코스모스 DB 인프라 위에 구축되었습니다. 이로 인해 [Azure Cosmos DB 서비스 제한에](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) 설명된 모든 제한은 여전히 적용됩니다. 

## <a name="limits"></a>제한

그렘린 제한에 도달하면 제한 오류를 나타내는 **x-ms 상태 코드** 429로 통과가 취소됩니다. 자세한 내용은 [Gremlin 서버 응답 헤더를](gremlin-limits.md) 참조하십시오.

**리소스**    | **기본 제한** | **설명**
--- | --- | ---
*스크립트 길이* | **64KB** | 요청당 Gremlin 순회 스크립트의 최대 길이입니다.
*연산자 깊이* | **400** |  순회의 총 고유 단계 수입니다. 예를 들어 ```g.V().out()``` 연산자 수 2: V() ```g.V('label').repeat(out()).times(100)``` 및 out() 연산자 깊이는 3: V(), repeat() 및 out() ```.times(100)``` ```.repeat()``` 연산자 매개변수이기 때문입니다.
*평행주의 정도* | **32** | 단일 요청에서 스토리지 레이어에 쿼리한 스토리지 파티션의 최대 수입니다. 파티션이 수백 개인 그래프는 이 제한의 영향을 받게 됩니다.
*반복 제한* | **32** | ```.repeat()``` 연산자가 실행할 수 있는 최대 반복 횟수입니다. 대부분의 경우 ```.repeat()``` 단계의 각 반복은 너비 우선 통과를 실행하므로 모든 순회는 정점 간에 최대 32개의 홉으로 제한됩니다.
*순회 시간 제한* | **30초** | 이 시간을 초과하면 통과가 취소됩니다. Cosmos DB Graph는 대부분의 순회가 밀리초 내에 완료되는 OLTP 데이터베이스입니다. 코스모스 DB 그래프에서 OLAP 쿼리를 실행하려면 그래프 [데이터 프레임](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) 및 [코스모스 DB 스파크 커넥터가](https://github.com/Azure/azure-cosmosdb-spark)있는 [아파치 스파크를](https://azure.microsoft.com/services/cosmos-db/) 사용합니다.
*유휴 연결 시간 제한* | **1시간** | Gremlin 서비스는 유휴 웹소켓 연결을 열어 두는 시간입니다. TCP keep-alive 패킷 또는 HTTP keep-alive 요청은 이 제한을 초과하여 연결 수명을 연장하지 않습니다. Cosmos DB 그래프 엔진은 활성 Gremlin 요청이 실행중인 경우 웹 소켓 연결을 유휴 상태로 간주합니다.
*시간당 리소스 토큰* | **100** | Gremlin 클라이언트가 한 지역의 Gremlin 계정에 연결하는 데 사용하는 고유 리소스 토큰 수입니다. 응용 프로그램이 시간별 고유 토큰 `"Exceeded allowed resource token limit of 100 that can be used concurrently"` 제한을 초과하면 다음 인증 요청에 반환됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure 코스모스 DB 그렘린 응답 헤더](gremlin-headers.md) 
* [Gremlin을 사용한 Azure Cosmos DB 리소스 토큰](how-to-use-resource-tokens-gremlin.md)
