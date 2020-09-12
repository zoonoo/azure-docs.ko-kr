---
title: Azure Cosmos DB의 API for MongoDB로 데이터 마이그레이션을 위한 마이그레이션 전 단계
description: 이 문서에서는 MongoDB에서 Cosmos DB로 데이터 마이그레이션을 위한 필수 구성 요소에 대한 개요를 제공합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/01/2020
ms.author: lbosq
ms.openlocfilehash: be38b1cfa698907f44c6deee77bb9b8ca88b77b7
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318219"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에서 Azure Cosmos DB의 API for MongoDB로 데이터 마이그레이션을 위한 마이그레이션 전 단계

MongoDB(온-프레미스 또는 클라우드)에서 Azure Cosmos DB의 API for MongoDB로 데이터를 마이그레이션하기 전에 다음을 수행해야 합니다.

1. [Azure Cosmos DB의 API for MongoDB 사용에 대한 주요 고려 사항 읽기](#considerations)
2. [데이터를 마이그레이션하는 옵션 선택](#options)
3. [워크로드에 필요한 처리량 예측](#estimate-throughput)
4. [데이터의 최적 파티션 키 선택](#partitioning)
5. [데이터에 대해 설정할 수 있는 인덱싱 정책 이해](#indexing)

위의 마이그레이션 필수 구성 요소를 이미 완료한 경우에는 [Azure Database Migration Service를 사용하여 MongoDB 데이터를 Azure Cosmos DB의 API for MongoDB로 마이그레이션](../dms/tutorial-mongodb-cosmos-db.md)할 수 있습니다. 또한 계정을 만들지 않은 경우 계정을 만드는 단계를 보여 주는 [빠른 시작](create-mongodb-dotnet.md)을 찾아볼 수 있습니다.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Azure Cosmos DB의 API for MongoDB 사용 시 고려 사항

다음은 Azure Cosmos DB의 API for MongoDB에 대한 특정 특성입니다.

- **용량 모델**: Azure Cosmos DB의 데이터베이스 용량은 처리량 기반 모델을 기반으로 합니다. 이 모델은 초당 컬렉션에 대해 실행할 수 있는 데이터베이스 작업의 수를 나타내는 단위인 [초당 요청 단위](request-units.md)를 기반으로 합니다. 이 용량은 [데이터베이스 또는 컬렉션 수준](set-throughput.md)에서 할당될 수 있으며, 할당 모델에서 프로비저닝하거나 [자동 크기 조정 프로비저닝된 처리량](provision-throughput-autoscale.md)을 사용하여 프로비저닝할 수 있습니다.

- **요청 단위**: 모든 데이터베이스 작업에는 Azure Cosmos DB에 관련된 RU(요청 단위) 비용이 포함됩니다. 이를 실행하면 지정된 초에 사용 가능한 요청 단위 수준에서 뺍니다. 요청에 현재 할당된 RU/초보다 많은 RU가 필요한 경우 문제를 해결하는 두 가지 옵션이 있습니다. RU의 용량을 늘리거나 다음 초가 시작될 때까지 기다린 후 작업을 다시 시도하세요.

- **탄력적 용량**: 지정된 컬렉션 또는 데이터베이스에 대한 용량은 언제든지 변경할 수 있습니다. 이를 통해 데이터베이스는 워크로드의 처리량 요구 사항에 맞게 탄력적으로 적응할 수 있습니다.

- **자동 분할**: Azure Cosmos DB는 분할(또는 파티션 키)만 필요한 자동 분할 시스템을 제공합니다. [자동 분할 메커니즘](partition-data.md)은 모든 Azure Cosmos DB API에서 공유되고, 원활한 데이터를 허용하고 수평 분산을 통해 전체를 확장할 수 있습니다.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Azure Cosmos DB의 API for MongoDB에 대한 마이그레이션 옵션

[Azure Cosmos DB의 API for MongoDB에 대한 Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md)는 완전 관리형 호스팅 플랫폼, 마이그레이션 모니터링 옵션 및 자동 제한 처리를 제공하여 데이터 마이그레이션을 간소화하는 메커니즘을 제공합니다. 옵션의 전체 목록은 다음과 같습니다.

|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|온라인|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB 대량 실행기 라이브러리 사용 <br/>&bull; 대량 데이터 세트에 적합하고 라이브 변경 내용 복제를 처리 <br/>&bull; 다른 MongoDB 소스에서만 작동|
|오프라인|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB 대량 실행기 라이브러리 사용 <br/>&bull; 대량 데이터 세트에 적합하고 라이브 변경 내용 복제를 처리 <br/>&bull; 다른 MongoDB 소스에서만 작동|
|오프라인|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; 간편한 설정 및 여러 소스 지원 <br/>&bull; Azure Cosmos DB 대량 실행기 라이브러리 사용 <br/>&bull; 대량 데이터 세트에 적합 <br/>&bull; 검사점이 없으면 마이그레이션 과정에서 발생하는 모든 문제에 대해 전체 마이그레이션 프로세스를 다시 시작해야 함을 의미함<br/>&bull; 배달 못한 편지 큐가 없으면 일부 잘못된 파일이 전체 마이그레이션 프로세스를 중지할 수 있음을 의미함 <br/>&bull; 특정 데이터 원본에 대한 읽기 처리량을 늘리려면 사용자 지정 코드가 필요함|
|오프라인|[기존 Mongo 도구(mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 간편한 설정 및 통합 <br/>&bull; 제한에 대한 사용자 지정 처리 필요|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> 워크로드에 필요한 처리량 예측

Azure Cosmos DB에서 처리량은 사전에 프로비저닝되며 초당 RU(요청 단위)로 측정됩니다. VM 또는 온-프레미스 서버와 달리 RU는 언제든지 쉽게 확장 및 축소할 수 있습니다. 프로비저닝된 RU의 수를 즉시 변경할 수 있습니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요.

[Azure Cosmos DB 용량 계산기](https://cosmos.azure.com/capacitycalculator/)를 사용하여 데이터베이스 계정 구성, 데이터 양, 문서 크기 및 초당 필요한 읽기/쓰기를 기반으로 하는 요청 단위 크기를 결정할 수 있습니다.

다음은 필수 RU의 수에 영향을 주는 주요 요소입니다.
- **문서 크기**: 항목/문서의 크기가 증가함에 따라 항목/문서를 읽거나 쓰는 데 사용되는 RU 수도 증가합니다.

- **문서 속성 수**: 문서를 만들거나 업데이트하는 데 사용된 RU 수는 해당 속성의 수, 복잡성 및 길이와 관련이 있습니다. [인덱싱되는 속성 수를 제한](mongodb-indexing.md)하면 쓰기 작업에 대한 요청 단위 사용을 줄일 수 있습니다.

- **쿼리 패턴**: 쿼리의 복잡성은 쿼리에서 사용되는 요청 단위의 양에 영향을 줍니다. 

쿼리 비용을 이해하는 가장 좋은 방법은 Azure Cosmos DB에서 샘플 데이터를 사용하고 `getLastRequestStastistics` 명령을 사용하여 [MongoDB Shell에서 샘플 쿼리를 실행](connect-mongodb-account.md)하여 요청 요금을 가져오는 것입니다. 그러면 사용된 RU 수가 출력됩니다.

`db.runCommand({getLastRequestStatistics: 1})`

이 명령은 다음과 유사한 JSON 문서를 출력합니다.

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

[진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB에 대해 실행되는 쿼리의 빈도와 패턴을 파악할 수도 있습니다. 진단 로그의 결과를 스토리지 계정, EventHub 인스턴스 또는 [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md)로 보낼 수 있습니다.  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>파티션 키 선택
분할(Sharding이라고도 함)은 데이터를 마이그레이션하기 전에 고려해야 할 주요 요소입니다. Azure Cosmos DB는 완전 관리형 분할을 사용하여 스토리지 및 처리량 요구 사항에 맞게 데이터베이스의 용량을 늘립니다. 이 기능에는 라우팅 서버의 호스팅 또는 구성이 필요 없습니다.   

이와 비슷한 방식으로 분할 기능이 자동으로 용량을 추가하고 그에 따라 데이터의 균형을 다시 조정합니다. 데이터의 올바른 파티션 키를 선택하는 방법에 대한 자세한 내용과 권장 사항은 [파티션 키 선택 문서](partitioning-overview.md#choose-partitionkey)를 참조하세요. 

## <a name="index-your-data"></a><a id="indexing"></a>데이터 인덱싱

MongoDB server 버전 3.6에 대 한 Azure Cosmos DB API는 자동으로 `_id` 필드를 인덱싱합니다. 이 필드는 삭제할 수 없습니다. 분할 된 키 당 필드의 고유성을 자동으로 적용 합니다 `_id` . 추가 필드를 인덱싱하려면 MongoDB 인덱스 관리 명령을 적용합니다. 이 기본 인덱싱 정책은 모든 필드를 기본적으로 인덱싱하는 Azure Cosmos DB SQL API와 다릅니다.

Azure Cosmos DB에서 제공 하는 인덱싱 기능에는 복합 인덱스, 고유 인덱스 및 TTL (time-to-live) 인덱스를 추가 하는 작업이 포함 됩니다. 인덱스 관리 인터페이스는 `createIndex()` 명령에 매핑됩니다. [Azure Cosmos DB의 MONGODB API에 대 한](mongodb-indexing.md)자세한 내용은 인덱싱을 참조 하세요.

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md)는 MongoDB 컬렉션을 고유 인덱스로 자동으로 마이그레이션합니다. 그러나 마이그레이션 전에 고유 인덱스를 만들어야 합니다. 컬렉션에 이미 데이터가 있는 경우 Azure Cosmos DB는 고유 인덱스 생성을 지원하지 않습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](unique-keys.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Database Migration Service를 사용하여 MongoDB 데이터를 Cosmos DB로 마이그레이션](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량 프로비저닝](set-throughput.md)
* [Azure Cosmos DB에서 분할](partition-data.md)
* [Azure Cosmos DB의 글로벌 배포](distribute-data-globally.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
