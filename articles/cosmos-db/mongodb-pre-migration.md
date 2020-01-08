---
title: MongoDB 용 Azure Cosmos DB API로 데이터를 마이그레이션하기 위한 마이그레이션 전 단계
description: 이 문서에서는 MongoDB에서 Cosmos DB로 데이터를 마이그레이션하기 위한 필수 구성 요소에 대 한 개요를 제공 합니다.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445204"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 MongoDB에서 Azure Cosmos DB의 API로 데이터 마이그레이션을 위한 마이그레이션 전 단계

MongoDB (온-프레미스 또는 클라우드 (IaaS))에서 MongoDB에 대 한 Azure Cosmos DB의 API로 데이터를 마이그레이션하기 전에 다음을 수행 해야 합니다.

1. [Azure Cosmos DB 계정 만들기](#create-account)
2. [워크 로드에 필요한 처리량 예측](#estimate-throughput)
3. [데이터의 최적 파티션 키 선택](#partitioning)
4. [데이터에 대해 설정할 수 있는 인덱싱 정책 이해](#indexing)

위의 마이그레이션 필수 구성 요소를 이미 완료 한 경우 실제 데이터 마이그레이션 지침은 [MongoDB 데이터를 Azure Cosmos DB의 API로 마이그레이션 MongoDB](../dms/tutorial-mongodb-cosmos-db.md) 을 참조 하세요. 그렇지 않은 경우이 문서는 이러한 필수 구성 요소를 처리 하는 지침을 제공 합니다. 

## <a id="create-account"></a>Azure Cosmos DB 계정 만들기 

마이그레이션을 시작 하기 전에 [MongoDB에 대 한 Azure Cosmos DB의 API를 사용 하 여 Azure Cosmos 계정을 만들어야](create-mongodb-dotnet.md)합니다. 

계정을 만들 때 설정을 선택 하 여 데이터를 [전역적으로 배포할](distribute-data-globally.md) 수 있습니다. 다중 지역 쓰기 (또는 다중 마스터 구성)를 사용 하도록 설정 하 여 각 지역이 쓰기 및 읽기 지역이 되도록 할 수도 있습니다.

![계정 만들기](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>워크 로드에 필요한 처리량 예상

[DMS (Database Migration Service)](../dms/dms-overview.md)를 사용 하 여 마이그레이션을 시작 하기 전에 Azure Cosmos 데이터베이스 및 컬렉션에 대해 프로 비전 할 처리량의 양을 예측 해야 합니다.

처리량은 다음 중 하나에 프로 비전 될 수 있습니다.

- 컬렉션

- 데이터베이스

> [!NOTE]
> 또한 데이터베이스의 일부 컬렉션에 전용 프로 비전 된 처리량이 있을 수 있으며 다른 컬렉션에서 처리량을 공유할 수 있는 위의 조합을 사용할 수 있습니다. 자세한 내용은 [데이터베이스 및 컨테이너에 대 한 처리량 설정](set-throughput.md) 페이지를 참조 하세요.
>

먼저 데이터베이스 또는 컬렉션 수준 처리량을 프로 비전 할 것인지 아니면 둘의 조합을 프로 비전 할지 결정 해야 합니다. 일반적으로 컬렉션 수준에서 전용 처리량을 구성 하는 것이 좋습니다. 데이터베이스 수준에서 처리량을 프로 비전 하면 데이터베이스의 컬렉션에서 프로 비전 된 처리량을 공유할 수 있습니다. 그러나 공유 처리량을 사용 하면 각 개별 컬렉션에 대 한 특정 처리량이 보장 되지 않으며 특정 컬렉션에 대 한 예측 가능한 성능을 얻을 수 없습니다.

각 개별 컬렉션에 대해 전용으로 사용할 처리량의 양에 대해 잘 모르겠으면 데이터베이스 수준 처리량을 선택할 수 있습니다. Azure Cosmos 데이터베이스에 구성 된 프로 비전 된 처리량을 MongoDB VM 또는 물리적 서버의 계산 용량과 동일한 논리로 간주할 수 있지만, 확장 하는 기능을 사용 하면 비용 효율성이 향상 됩니다. 자세한 내용은 [Azure Cosmos 컨테이너 및 데이터베이스에서 처리량 프로 비전](set-throughput.md)을 참조 하세요.

데이터베이스 수준에서 처리량을 프로 비전 하는 경우 해당 데이터베이스 내에 생성 된 모든 컬렉션을 파티션/분할 키를 사용 하 여 만들어야 합니다. 분할에 대 한 자세한 내용은 [Azure Cosmos DB 분할 및 수평적 크기 조정](partition-data.md)을 참조 하세요. 마이그레이션하는 동안 파티션/분할 키를 지정 하지 않으면 Azure Database Migration Service는 자동으로 각 문서에 대해 자동으로 생성 되는 *_id* 특성으로 분할 키 필드를 채웁니다.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>프로 비전 할 최적의 RUs (요청 단위) 수

Azure Cosmos DB 처리량은 사전에 프로 비전 되며 초당 (요청 단위)로 측정 됩니다. VM 또는 온-프레미스에서 MongoDB를 실행 하는 워크 로드가 있는 경우 VM 또는 온-프레미스 서버 크기 및 소유 하는 리소스 (예: 메모리, CPU, IOPs)와 같은 물리적 리소스에 대 한 간단한 추상화 라고 생각 합니다. 

Vm 또는 온-프레미스 서버와 달리 RUs는 언제 든 지 쉽게 확장 및 축소할 수 있습니다. 몇 초 이내에 프로 비전 된 RUs 수를 변경할 수 있으며, 지정 된 1 시간 동안 프로 비전 하는 최대 RUs 수에 대해서만 요금이 청구 됩니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요.

다음은 필수 RUs의 수에 영향을 주는 주요 요소입니다.
- 항목 **(예: 문서) 크기**: 항목/문서 크기를 늘릴 때 항목/문서를 읽거나 쓰는 데 사용 된 RUs의 수도 늘어납니다.
- **항목 속성 수**: 모든 속성에 대 한 [기본 인덱싱을](index-overview.md) 가정 하 고 항목 속성 수가 증가할수록 항목을 쓰는 데 사용 된 RUs 수가 늘어납니다. [인덱싱된 속성 수를 제한](index-policy.md)하 여 쓰기 작업에 대 한 요청 단위 사용량을 줄일 수 있습니다.
- **동시 작업**: 사용 되는 요청 단위는 다른 CRUD 작업 (예: 쓰기, 읽기, 업데이트, 삭제) 및 더 복잡 한 쿼리를 실행 하는 빈도에 따라 달라 집니다. [Mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) 를 사용 하 여 현재 MongoDB 데이터의 동시성 요구 사항을 출력할 수 있습니다.
- **쿼리 패턴**: 쿼리의 복잡성은 쿼리에서 사용 되는 요청 단위 수에 영향을 줍니다.

[Mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) 를 사용 하 여 JSON 파일을 내보내고 초당 발생 하는 쓰기, 읽기, 업데이트 및 삭제 수를 파악 하는 경우 [Azure Cosmos DB capacity planner](https://www.documentdb.com/capacityplanner) 를 사용 하 여 프로 비전 할 초기 RUs 수를 예상할 수 있습니다. Capacity planner는 보다 복잡 한 쿼리 비용을 고려 하지 않습니다. 따라서 데이터에 대해 복잡 한 쿼리를 사용 하는 경우 추가 RUs가 사용 됩니다. 계산기는 또한 모든 필드를 인덱싱하고 세션 일관성을 사용 한다고 가정 합니다. 쿼리 비용을 이해 하는 가장 좋은 방법은 데이터 (또는 샘플 데이터)를 Azure Cosmos DB으로 마이그레이션하고, [Cosmos DB의 끝점에 연결](connect-mongodb-account.md) 하 고, `getLastRequestStastistics` 명령을 사용 하 여 MongoDB 셸에서 샘플 쿼리를 실행 하 여 요청 요금을 가져오는 것입니다. 그러면 사용 된 RUs 수가 출력 됩니다.

`db.runCommand({getLastRequestStatistics: 1})`

이 명령은 다음과 유사한 JSON 문서를 출력 합니다.

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

쿼리에서 사용 된 RUs의 수 및 해당 쿼리에 대 한 동시성 요구 사항을 파악 한 후 프로 비전 된 RUs의 수를 조정할 수 있습니다. RUs를 최적화 하는 작업은 일회성 이벤트가 아닙니다. 과도 한 작업이 나 데이터 가져오기와 달리 트래픽이 과도 하 게 예상 되는지 여부에 따라 프로 비전 된 RUs를 지속적으로 최적화 하거나 확장 해야 합니다.

## <a id="partitioning"></a>파티션 키 선택
분할은 Azure Cosmos DB와 같은 전역적으로 분산 된 데이터베이스로 마이그레이션하기 전에 고려해 야 할 주요 사항입니다. Azure Cosmos DB는 분할을 사용 하 여 응용 프로그램의 확장성 및 성능 요구 사항을 충족 하기 위해 데이터베이스의 개별 컨테이너를 확장 합니다. 분할에서 컨테이너의 항목은 논리적 파티션 이라는 별개의 하위 집합으로 나뉩니다. 데이터의 올바른 파티션 키를 선택 하는 방법에 대 한 자세한 내용과 권장 사항은 [파티션 키 선택 섹션](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)을 참조 하세요. 

## <a id="indexing"></a>데이터 인덱싱
기본적으로 Azure Cosmos DB는 수집 시 모든 데이터 필드를 인덱싱합니다. 언제 든 지 Azure Cosmos DB에서 [인덱싱 정책을](index-policy.md) 수정할 수 있습니다. 실제로 데이터를 마이그레이션할 때 인덱싱을 해제 하 고 데이터가 이미 Cosmos DB에 있는 경우 다시 설정 하는 것이 좋습니다. 인덱싱에 대 한 자세한 내용은 [Azure Cosmos DB의 인덱싱](index-overview.md) 섹션에서 자세히 알아볼 수 있습니다. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 는 고유 인덱스를 사용 하 여 MongoDB 컬렉션을 자동으로 마이그레이션합니다. 그러나 마이그레이션 전에 고유 인덱스를 만들어야 합니다. 컬렉션에 이미 데이터가 있는 경우 Azure Cosmos DB는 고유 인덱스 생성을 지원 하지 않습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](unique-keys.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Database Migration Service를 사용 하 여 MongoDB 데이터를 Cosmos DB으로 마이그레이션합니다.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos 컨테이너 및 데이터베이스에 대 한 처리량 프로 비전](set-throughput.md)
* [Azure Cosmos DB에서 분할](partition-data.md)
* [Azure Cosmos DB의 전역 배포](distribute-data-globally.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
