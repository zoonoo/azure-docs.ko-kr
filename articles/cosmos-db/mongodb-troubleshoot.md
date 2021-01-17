---
title: Mongo DB에 대 한 Azure Cosmos DB의 API의 일반적인 오류 문제 해결
description: 이 문서에서는 Azure Cosmos DB의 MongoDB API에서 발생 하는 일반적인 문제를 해결 하는 방법을 설명 합니다.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 26097408d0b83b043f4a25183146c892fc4b48ad
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538544"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API의 일반적인 문제 해결
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

다음 문서에서는 MongoDB 용 Azure Cosmos DB API를 사용 하는 배포에 대 한 일반적인 오류 및 해결 방법을 설명 합니다.

>[!Note]
> Azure Cosmos DB MongoDB 엔진을 호스팅하지 않습니다. MongoDB 유선 프로토콜의 구현을 제공 합니다. 따라서 이러한 오류 중 일부는 Azure Cosmos DB의 MongoDB API 에서만 찾을 수 있습니다. 

## <a name="common-errors-and-solutions"></a>일반 오류 및 해결 방법

| 코드       | 오류                | 설명  | 솔루션  |
|------------|----------------------|--------------|-----------|
| 2 | 지정 된 order by 항목에 해당 하는 인덱스 경로가 제외 되거나 order by 쿼리에 해당 인덱스를 제공할 수 있는 해당 복합 인덱스가 없습니다. | 쿼리에서 인덱싱되지 않은 필드에 대한 정렬을 요청합니다. | 시도 중인 정렬 쿼리에 대해 일치 하는 인덱스 (또는 복합 인덱스)를 만듭니다. |
| 13 | 권한 없음 | 요청에 완료할 수 있는 권한이 없습니다. | 데이터베이스 및 컬렉션에 대 한 적절 한 권한을 설정 했는지 확인 합니다.  |
| 16 | InvalidLength | 지정 된 요청에 잘못 된 길이가 있습니다. | 설명 () 함수를 사용 하는 경우에는 하나의 작업만 제공 해야 합니다. |
| 26 | NamespaceNotFound | 쿼리에서 참조 되는 데이터베이스 또는 컬렉션을 찾을 수 없습니다. | 데이터베이스/컬렉션 이름이 쿼리의 이름과 정확 하 게 일치 하는지 확인 합니다.|
| 50 | ExceededTimeLimit | 요청이 실행 시간 제한인 60초를 초과했습니다. |  이 오류의 원인은 여러 가지가 있을 수 있습니다. 원인 중 하나는 현재 할당 된 요청 단위 용량이 부족 하 여 요청을 완료할 수 없는 경우입니다. 이는 해당 컬렉션 또는 데이터베이스의 요청 단위를 늘려서 해결할 수 있습니다. 다른 경우에는 대규모 요청을 더 작은 요청으로 분할 하 여이 오류를 해결할 수 있습니다. 이 오류가 수신 된 쓰기 작업을 다시 시도 하면 쓰기가 중복 될 수 있습니다.|
| 61 | ShardKeyNotFound | 요청의 문서에 컬렉션의 분할 키 (Azure Cosmos DB 파티션 키)가 포함 되어 있지 않습니다. | 컬렉션의 분할 키가 요청에 사용 되 고 있는지 확인 합니다.|
| 66 | ImmutableField | 요청에서 변경할 수 없는 필드를 변경 하려고 합니다. | "id" 필드는 변경할 수 없습니다. 요청에서 해당 필드의 업데이트를 시도 하지 않는지 확인 합니다. |
| 67 | CannotCreateIndex | 인덱스를 만드는 요청을 완료할 수 없습니다. | 컨테이너에서 최대 500 개의 단일 필드 인덱스를 만들 수 있습니다. 복합 인덱스에는 최대 8 개의 필드를 포함할 수 있습니다. 복합 인덱스는 버전 3.6 이상에서 지원 됩니다. |
| 115 | CommandNotSupported | 시도한 요청은 지원 되지 않습니다. | 오류에 추가 세부 정보를 제공 해야 합니다. 배포에이 기능이 중요 한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 지원 티켓을 만들어 알려주세요. |
| 11000 | DuplicateKey | 삽입 중인 문서의 분할 키 (Azure Cosmos DB 파티션 키)가 이미 컬렉션에 있거나 고유 인덱스 필드 제약 조건을 위반 했습니다. | Update () 함수를 사용 하 여 기존 문서를 업데이트할 수 있습니다. Unique index 필드 제약 조건을 위반 하는 경우에는 분할/파티션에 아직 존재 하지 않는 필드 값으로 문서를 삽입 하거나 업데이트 합니다. |
| 16500 | TooManyRequests  | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure Portal에서 컨테이너 또는 컨테이너 집합에 할당된 처리량을 크기 조정하거나 작업을 다시 시도하는 것이 좋습니다. SSR (서버 쪽 다시 시도)를 [사용 하도록 설정](prevent-rate-limiting-errors.md) 하면 Azure Cosmos DB이 오류로 인해 실패 한 요청을 자동으로 다시 시도 합니다. |
| 16501 | ExceededMemoryLimit | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. 예: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | 인식할 수 없는 파이프라인 단계 이름입니다. | 집계 파이프라인 요청에서 스테이지 이름이 인식 되지 않았습니다. | 요청에서 모든 집계 파이프라인 이름이 유효한 지 확인 합니다. |
| - | MongoDB 유선 버전 문제 | 이전 버전의 MongoDB 드라이버는 연결 문자열에서 Azure Cosmos 계정의 이름을 검색할 수 없습니다. | MongoDB에 대 한 Cosmos DB API 연결 문자열의 끝에 *appName = @**accountName** @* 을 추가 합니다. 여기서 ***accountname*** 은 Cosmos DB 계정 이름입니다. |
| - | MongoDB client 네트워킹 문제 (예: 소켓 또는 endOfStream 예외)| 네트워크 요청이 실패 했습니다. 이는 MongoDB 클라이언트에서 사용 하려고 하는 비활성 TCP 연결로 인해 발생 하는 경우가 많습니다. MongoDB 드라이버는 연결 풀링을 사용 하 여 요청에 사용 되는 풀에서 임의 연결을 선택 하는 경우가 많습니다. 비활성 연결은 일반적으로 4 분 후 Azure Cosmos DB 끝에서 시간 초과 됩니다. | 응용 프로그램 코드에서 이러한 실패 한 요청을 다시 시도 하거나, MongoDB client (드라이버) 설정을 변경 하 여 4 분 시간 제한 기간 전에 비활성 TCP 연결을 해체 하거나, OS keepalive 설정을 구성 하 여 활성 상태에서 TCP 연결을 유지 관리할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 API for MongoDB와 함께 [Studio 3T를 사용](mongodb-mongochef.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.
