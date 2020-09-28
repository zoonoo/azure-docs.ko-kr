---
title: Mongo DB에 대 한 Azure Cosmos DB의 API의 일반적인 오류 문제 해결
description: 이 문서에서는 Azure Cosmos DB의 MongoDB API에서 발생 하는 일반적인 문제를 해결 하는 방법을 설명 합니다.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: 27a9c7eb48c4a0148401c0d146a50a5197593806
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409632"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API의 일반적인 문제 해결

다음 문서에서는 MongoDB 용 Azure Cosmos DB API를 사용 하는 데이터베이스에 대 한 일반적인 오류 및 해결 방법을 설명 합니다.

>[!Note]
> Azure Cosmos DB MongoDB 엔진을 호스팅하지 않습니다. MongoDB [유선 프로토콜 버전 3.6](mongodb-feature-support-36.md) 및 [유선 프로토콜 버전 3.2](mongodb-feature-support.md)에 대 한 레거시 지원의 구현을 제공 하므로 이러한 오류 중 일부는 Azure Cosmos DB의 MongoDB API 에서만 찾을 수 있습니다. 

## <a name="common-errors-and-solutions"></a>일반 오류 및 해결 방법

| 오류               | 코드  | Description  | 해결 방법  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | 요청이 실행 시간 제한인 60초를 초과했습니다. | 이 오류의 원인은 여러 가지가 있을 수 있습니다. 원인 중 하나는 현재 할당된 요청 단위 용량이 요청을 완료하기에 충분하지 않은 경우입니다. 이는 해당 컬렉션 또는 데이터베이스의 요청 단위를 늘려서 해결할 수 있습니다. 다른 경우에는 대규모 요청을 더 작은 요청으로 분할 하 여이 오류를 해결할 수 있습니다. |
| TooManyRequests     | 16500 | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure Portal에서 컨테이너 또는 컨테이너 집합에 할당된 처리량을 크기 조정하거나 작업을 다시 시도하는 것이 좋습니다. |
| ExceededMemoryLimit | 16501 | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. 예: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 지정된 order-by 항목에 해당하는 인덱스 경로가 제외되거나 order-by 쿼리에 제공할 수 있는 해당 복합 인덱스가 없습니다. | 2 | 쿼리에서 인덱싱되지 않은 필드에 대한 정렬을 요청합니다. | 시도 중인 정렬 쿼리에 대해 일치 하는 인덱스 (또는 복합 인덱스)를 만듭니다. |
| MongoDB 유선 버전 문제 | - | 이전 버전의 MongoDB 드라이버는 연결 문자열에서 Azure Cosmos 계정의 이름을 검색할 수 없습니다. | MongoDB에 대 한 Cosmos DB API 연결 문자열의 끝에 *appName = @**accountName** @ * 을 추가 합니다. 여기서 ***accountname*** 은 Cosmos DB 계정 이름입니다. |

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.

