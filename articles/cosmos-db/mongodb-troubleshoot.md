---
title: Mongo DB에 대 한 Azure Cosmos DB API의 일반적인 오류 해결
description: 이 문서는 MongoDB에 대 한 Azure Cosmos DB API에서 발생 하는 일반적인 문제를 해결 하는 방법을 설명 합니다.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735708"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API의 일반적인 문제를 해결 합니다.

Azure Cosmos DB는 MongoDB 등 일반적인 NoSQL 데이터베이스의 유선 프로토콜을 구현 합니다. 통신 프로토콜 구현으로 인해 투명 하 게 상호 작용할 수 있습니다 Azure Cosmos DB는 기존 클라이언트 Sdk, 드라이버 및 NoSQL 데이터베이스를 사용 하는 도구를 사용 하 여 합니다. Azure Cosmos DB는 NoSQL 데이터베이스에 대 한 유선 호환 Api를 제공 하는 것에 대 한 데이터베이스의 모든 소스 코드를 사용 하지 않습니다. 통신 프로토콜 버전을 이해 하는 MongoDB 클라이언트 드라이버는 Azure Cosmos DB에 연결할 수 있습니다.

Azure Cosmos DB에 해당 하는 일부 사용자 지정 오류 코드는 Azure Cosmos DB의 MongoDB 용 API는 MongoDB 유선 프로토콜 (버전 3.4에서에서 추가 기능과 쿼리 연산자는 현재 미리 보기로 제공)의 3.2 버전과 호환 되는 동안 특정 오류가 발생 했습니다. 이 문서에서는 다양 한 오류, 오류 코드 및 오류를 해결 하는 단계를 설명 합니다.

## <a name="common-errors-and-solutions"></a>일반적인 오류 및 해결 방법

| 오류               | 코드  | 설명  | 해결 방법  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure portal에서 컨테이너 또는 컨테이너 집합에 할당 된 처리량을 크기 조정 하는 것이 좋습니다. 또는 작업을 다시 시도할 수 있습니다. |
| ExceededMemoryLimit | 16501 | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. 예제: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| MongoDB 유선 버전 문제 | - | 이전 버전의 MongoDB 드라이버 연결 문자열에는 Azure Cosmos 계정의 이름을 검색 하지 못합니다. | 추가 *appName = @**accountName** @*  Cosmos DB의 API에 대 한 MongoDB 연결 문자열의 끝에 있는 ***accountName*** 은 Cosmos DB 계정 이름 . |


## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.

