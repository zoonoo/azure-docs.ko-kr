---
title: MongoDB용 Azure Cosmos DB의 API에서 일반적으로 발생하는 문제 해결
description: 본 문서에서는 MongoDB용 Azure Cosmos DB의 API에서 발생하는 일반적인 문제를 해결하는 방법을 설명합니다.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: c2d86af956ff60bba22effe059996cf499e4b282
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108134090"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Mongo DB용 Azure Cosmos DB의 API에서 일반적으로 발생하는 문제 해결
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

본 문서에서는 Mongo DB용 Azure Cosmos DB API를 사용한 배포에서 발생하는 일반적인 오류와 그 해결 방법을 설명합니다.

>[!Note]
> Azure Cosmos DB는 MongoDB 엔진을 호스트하지 않습니다. MongoDB [유선 프로토콜 버전 4.0](mongodb-feature-support-40.md)과 [3.6](mongodb-feature-support-36.md) 및 [유선 프로토콜 버전 3.2](mongodb-feature-support.md)의 레거시 지원을 구현합니다. 따라서 일부 오류는 MongoDB용 Azure Cosmos DB의 API에서만 발생합니다.

## <a name="common-errors-and-solutions"></a>일반 오류 및 해결 방법

| 코드       | 오류                | Description  | 해결 방법  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | 한 가지 일반적인 원인은 지정된 order-by 항목에 해당하는 인덱스 경로가 제외되거나 order-by 쿼리에 제공할 수 있는 해당 복합 인덱스가 없기 때문입니다. 쿼리에서 인덱싱되지 않은 필드에 대한 정렬을 요청합니다. | 시도 중인 정렬 쿼리에 대해 일치하는 인덱스(또는 복합 인덱스)를 만듭니다. |
| 2 | 트랜잭션이 활성 상태가 아닙니다. | 다중 문서 트랜잭션이 고정된 5초 제한 시간을 초과했습니다. | 다중 문서 트랜잭션을 다시 시도하거나 다중 문서 트랜잭션 내의 작업 범위를 제한하여 5초 제한 시간 내에 완료되도록 합니다. |
| 13 | 권한 없음 | 요청에 완료할 수 있는 권한이 없습니다. | 올바른 키를 사용하고 있는지 확인합니다.  |
| 26 | NamespaceNotFound | 쿼리에서 참조되는 데이터베이스 또는 컬렉션을 찾을 수 없습니다. | 데이터베이스/컬렉션 이름이 쿼리의 이름과 정확하게 일치하는지 확인합니다.|
| 50 | ExceededTimeLimit | 요청이 실행 시간 제한인 60초를 초과했습니다. |  이 오류의 원인은 여러 가지가 있을 수 있습니다. 원인 중 하나는 현재 할당된 요청 단위 용량이 요청을 완료하기에 충분하지 않은 경우입니다. 이는 해당 컬렉션 또는 데이터베이스의 요청 단위를 늘려서 해결할 수 있습니다. 다른 경우에는 큰 요청을 더 작은 요청으로 분할하여 이 오류를 해결할 수 있습니다. 이 오류가 수신된 쓰기 작업을 다시 시도하면 쓰기가 중복될 수 있습니다. <br><br>RU에 영향을 주지 않고 대량의 데이터를 삭제하려는 경우: <br>- TTL(타임스탬프 기반)을 사용하는 것이 좋습니다. [Azure Cosmos DB의 MongoDB API를 사용하여 데이터 만료](mongodb-time-to-live.md) <br>- 커서/일괄 처리 크기를 사용하여 삭제를 수행합니다. 한 번에 하나의 문서를 가져와서 루프를 통해 삭제할 수 있습니다. 이렇게 하면 프로덕션 애플리케이션에 영향을 주지 않고 데이터를 천천히 삭제할 수 있습니다.|
| 61 | ShardKeyNotFound | 요청의 문서에 컬렉션의 분할 키(Azure Cosmos DB 파티션 키)가 포함되어 있지 않습니다. | 컬렉션의 분할 키가 요청에 사용되고 있는지 확인합니다.|
| 66 | ImmutableField | 요청에서 변경할 수 없는 필드를 변경하려고 합니다. | “_id” 필드는 변경할 수 없습니다. 요청에서 해당 필드 또는 분할 키 필드의 업데이트하려고 하지 않는지 확인합니다. |
| 67 | CannotCreateIndex | 인덱스를 만드는 요청을 완료할 수 없습니다. | 컨테이너에서 최대 500개의 단일 필드 인덱스를 만들 수 있습니다. 복합 인덱스에는 최대 8개의 필드를 포함할 수 있습니다. 복합 인덱스는 버전 3.6 이상에서 지원됩니다. |
| 112 | WriteConflict | 다중 문서 트랜잭션 충돌로 인해 다중 문서 트랜잭션이 실패했습니다. | 성공할 때까지 다중 문서 트랜잭션을 다시 시도합니다. |
| 115 | CommandNotSupported | 시도한 요청은 지원되지 않습니다. | 오류에 추가 세부 정보를 제공해야 합니다. 배포에 해당 기능이 중요하다면 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 지원 티켓을 만드세요. Azure Cosmos DB 팀이 연락할 것입니다. |
| 11000 | DuplicateKey | 삽입 중인 문서의 분할 키(Azure Cosmos DB 파티션 키)가 이미 컬렉션에 있거나 고유 인덱스 필드 제약 조건을 위반했습니다. | update() 함수를 사용하여 기존 문서를 업데이트합니다. 고유 인덱스 필드 제약 조건을 위반한 경우 분할/파티션에 아직 존재하지 않는 필드 값으로 문서를 삽입하거나 업데이트합니다. ID 및 분할 키 필드의 조합을 포함하는 필드를 사용할 수도 있습니다. |
| 16500 | TooManyRequests  | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure Portal에서 컨테이너 또는 컨테이너 집합에 할당된 처리량을 크기 조정하거나 작업을 다시 시도하는 것이 좋습니다. [SSR(서버 쪽 다시 시도)](prevent-rate-limiting-errors.md)을 사용하도록 설정하면 Azure Cosmos DB는 이 오류로 인해 실패한 요청을 자동으로 다시 시도합니다. |
| 16501 | ExceededMemoryLimit | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. Azure Cosmos DB API for MongoDB 버전 3.2에만 적용할 수 있습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. 예: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | 인식할 수 없는 파이프라인 단계 이름입니다. | 집계 파이프라인 요청에서 단계 이름이 인식되지 않았습니다. | 요청에서 모든 집계 파이프라인 이름이 유효한지 확인합니다. |
| - | MongoDB 유선 버전 문제 | 이전 버전의 MongoDB 드라이버는 연결 문자열에서 Azure Cosmos 계정 이름을 검색할 수 없습니다. | 연결 문자열 마지막에 `appName=@accountName@`을 추가합니다. 여기서 `accountName`은 내 Azure Cosmos DB 계정 이름입니다. |
| - | MongoDB 클라이언트 네트워킹 문제(예: 소켓 또는 endOfStream 예외)| 네트워크 요청이 실패했습니다. 이는 MongoDB 클라이언트가 사용하려는 비활성 TCP 연결로 인해 발생하는 경우가 많습니다. MongoDB 드라이버는 종종 연결 풀링을 사용하므로 요청에 사용되는 풀에서 임의의 연결이 선택됩니다. 비활성 연결로 인해 일반적으로 Azure Cosmos DB 끝에서 4분 후에 시간 초과됩니다. | 애플리케이션 코드에서 이러한 실패 요청을 다시 시도하거나, MongoDB 클라이언트(드라이버) 설정을 변경하여 4분 제한 시간 전에 비활성 TCP 연결을 해제하거나, TCP 연결을 활성 상태로 유지하도록 OS `keepalive` 설정을 구성할 수 있습니다.<br><br>연결 메시지를 방지하기 위해 `maxConnectionIdleTime`을 1-2분으로 설정하도록 연결 문자열을 변경하려고 합니다.<br>- Mongo 드라이버: `maxIdleTimeMS=120000` 구성 <br>- Node.JS: `socketTimeoutMS=120000` 구성, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3분
| - | Mongo Shell이 Azure Portal에서 작동하지 않습니다. | 사용자가 Mongo shell을 열려고 하면 아무 일도 발생하지 않으며 탭이 비어 있습니다.  | 방화벽을 확인합니다. 방화벽은 Azure Portal의 Mongo Shell에서 지원되지 않습니다. <br>- 방화벽 규칙 내에서 로컬 컴퓨터에 Mongo shell 설치 <br>- 레거시 Mongo shell 사용
| - | 연결 문자열에 연결할 수 없습니다.  | 3\.2에서 3.6으로 업그레이드하는 경우 연결 문자열이 변경되었습니다. | Azure Cosmos DB의 API for MongoDB 계정을 사용하는 경우 3.6 버전의 계정에는 `*.mongo.cosmos.azure.com` 형식의 엔드포인트가 있지만 3.2 버전의 계정에는 `*.documents.azure.com` 형식의 엔드포인트가 있습니다.  

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.
