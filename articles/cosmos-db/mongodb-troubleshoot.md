---
title: 몽고 DB용 Azure 코스모스 DB의 API에서 일반적인 오류 문제 해결
description: 이 문서에서는 MongoDB용 Azure Cosmos DB의 API에서 발생하는 일반적인 문제를 해결하는 방법에 대해 설명합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941840"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>몽고DB용 Azure 코스모스 DB의 API에서 일반적인 문제 해결

Azure 코스모스 DB는 MongoDB를 포함한 일반적인 NoSQL 데이터베이스의 와이어 프로토콜을 구현합니다. 와이어 프로토콜 구현으로 인해 기존 클라이언트 SDK, 드라이버 및 NoSQL 데이터베이스와 함께 작동하는 도구를 사용하여 Azure Cosmos DB와 투명하게 상호 작용할 수 있습니다. Azure Cosmos DB는 NoSQL 데이터베이스에 대해 와이어 호환 API를 제공하기 위해 데이터베이스의 소스 코드를 사용하지 않습니다. 와이어 프로토콜 버전을 이해하는 모든 MongoDB 클라이언트 드라이버는 Azure Cosmos DB에 연결할 수 있습니다.

MongoDB용 Azure Cosmos DB의 API는 MongoDB의 와이어 프로토콜의 3.2 버전과 호환되지만(버전 3.4에 추가된 쿼리 연산자 및 기능은 현재 미리 보기로 사용할 수 있음) Azure Cosmos DB에 해당하는 일부 사용자 지정 오류 코드가 있습니다. 특정 오류가 있습니다. 이 문서에서는 다양한 오류, 오류 코드 및 이러한 오류를 해결하는 단계를 설명합니다.

## <a name="common-errors-and-solutions"></a>일반적인 오류 및 솔루션

| Error               | 코드  | 설명  | 해결 방법  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure 포털에서 컨테이너 또는 컨테이너 집합에 할당된 처리량을 조정하거나 작업을 다시 시도할 수 있습니다. |
| ExceededMemoryLimit | 16501 | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. 예: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 지정된 주문별 항목에 대응하는 인덱스 경로가 제외/ 쿼리별 순서는 제공될 수 있는 해당 복합 인덱스를 갖지 않습니다. | 2 | 쿼리는 인덱싱되지 않은 필드에 대한 정렬을 요청합니다. | 시도중인 정렬 쿼리에 대한 일치 인덱스(또는 복합 인덱스)를 만듭니다. |
| MongoDB 유선 버전 문제 | - | 이전 버전의 MongoDB 드라이버는 연결 문자열에서 Azure Cosmos 계정의 이름을 검색할 수 없습니다. | MongoDB 연결 문자열에 대한 코스모스 DB API 끝에 *있는 appName=@**계정 이름을** @ * 부가하면 계정 ***이름이*** 코스모스 DB 계정 이름입니다. |


## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.

