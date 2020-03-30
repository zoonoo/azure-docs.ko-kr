---
title: MongoDB용 Azure Cosmos DB의 API로 데이터 마이그레이션을 위한 사전 마이그레이션 단계
description: 이 문서는 MongoDB에서 Cosmos DB로의 데이터 마이그레이션에 대한 필수 구성 에 대한 개요를 제공합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942088"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에서 MongoDB용 Azure 코스모스 DB의 API로의 데이터 마이그레이션을 위한 사전 마이그레이션 단계

MongoDB(온-프레미스 또는 클라우드)에서 MongoDB용 Azure Cosmos DB의 API로 데이터를 마이그레이션하기 전에 다음을 수행해야 합니다.

1. [MongoDB에 Azure 코스모스 DB의 API 사용에 대한 주요 고려 사항 읽기](#considerations)
2. [데이터 마이그레이션 옵션 선택](#options)
3. [워크로드에 필요한 처리량 예측](#estimate-throughput)
4. [데이터에 가장 적합한 파티션 키 선택](#partitioning)
5. [데이터에 설정할 수 있는 인덱싱 정책 이해](#indexing)

마이그레이션에 대한 위의 필수 구성 조건을 이미 완료한 경우 [Azure 데이터베이스 마이그레이션 서비스를 사용하여 MongoDB에 대한 MongoDB 데이터를 Azure Cosmos DB의 API로 마이그레이션할](../dms/tutorial-mongodb-cosmos-db.md)수 있습니다. 또한 계정을 만들지 않은 경우 계정을 만드는 단계를 표시하는 [빠른 시작을](create-mongodb-dotnet.md) 찾아볼 수 있습니다.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>몽고DB에 Azure 코스모스 DB의 API를 사용할 때 고려해야 할 사항

다음은 몽고DB용 Azure 코스모스 DB의 API에 대한 특정 특성입니다.

- **용량 모델**: Azure Cosmos DB의 데이터베이스 용량은 처리량 기반 모델을 기반으로 합니다. 이 모델은 초당 컬렉션에 대해 실행할 수 있는 데이터베이스 작업 수를 나타내는 단위인 [초당 요청 단위를](request-units.md)기반으로 합니다. 이 용량은 데이터베이스 [또는 컬렉션 수준에서](set-throughput.md)할당할 수 있으며 할당 모델에서 프로비전하거나 [자동 파일럿 모델을](provision-throughput-autopilot.md)사용하여 프로비전할 수 있습니다.

- **요청 단위**: 모든 데이터베이스 작업에 Azure Cosmos DB에서 연결된 요청 단위(RUs) 비용이 있습니다. 실행하면 지정된 두 번째 에 사용 가능한 요청 단위 수준에서 이 수준이 차감됩니다. 요청에 현재 할당된 RU/s보다 더 많은 RU가 필요한 경우 RU 양을 늘리거나 다음 두 번째 시작 때까지 기다린 다음 작업을 다시 시도할 수 있는 두 가지 옵션이 있습니다.

- **탄력적 용량**: 지정된 컬렉션 또는 데이터베이스의 용량은 언제든지 변경될 수 있습니다. 이렇게 하면 데이터베이스가 워크로드의 처리량 요구 사항에 탄력적으로 적응할 수 있습니다.

- **자동 샤딩**: Azure Cosmos DB는 샤드(또는 파티션 키)만 필요로 하는 자동 파티션 시스템을 제공합니다. [자동 분할 메커니즘은](partition-data.md) 모든 Azure Cosmos DB API에서 공유되며 원활한 데이터와 수평 분포를 통한 확장 을 허용합니다.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>몽고DB에 대한 Azure 코스모스 DB의 API에 대한 마이그레이션 옵션

[MongoDB용 Azure Cosmos DB의 API용 Azure 데이터베이스 마이그레이션 서비스는](../dms/tutorial-mongodb-cosmos-db.md) 완전히 관리되는 호스팅 플랫폼, 마이그레이션 모니터링 옵션 및 자동 제한 처리를 제공하여 데이터 마이그레이션을 간소화하는 메커니즘을 제공합니다. 옵션의 전체 목록은 다음과 같습니다.

|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;설정이 간편하고 여러 소스 지원 <br/>&bull;큰 데이터 집합에는 적합하지 않습니다.|
|오프라인|[Azure 데이터 팩터리](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;설정이 간편하고 여러 소스 지원 <br/>&bull;Azure 코스모스 DB 대량 실행기 라이브러리를 사용합니다. <br/>&bull;대용량 데이터 세트에 적합 <br/>&bull;검사점이 없다는 것은 마이그레이션 과정에서 문제가 발생하므로 전체 마이그레이션 프로세스를 다시 시작해야 합니다.<br/>&bull;배달 못한 편지 대기열이 없어도 몇 개의 잘못된 파일이 전체 마이그레이션 프로세스를 중지할 수 있습니다. <br/>&bull;특정 데이터 원본에 대한 읽기 처리량을 높이기 위해 사용자 지정 코드가 필요합니다.|
|오프라인|[기존 몽고 도구 (몽고 덤프, 몽고 복원, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;간편한 설정 및 통합 <br/>&bull;스로틀에 대한 사용자 정의 처리가 필요합니다.|
|온라인|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;완벽하게 관리되는 마이그레이션 서비스.<br/>&bull;마이그레이션 작업에 대한 호스팅 및 모니터링 솔루션을 제공합니다. <br/>&bull;대규모 데이터 집합에 적합하며 라이브 변경 내용 복제를 처리합니다. <br/>&bull;다른 MongoDB 소스에서만 작동|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>워크로드에 필요한 처리량 예측

Azure Cosmos DB에서 처리량은 사전에 프로비전되며 초당 RU(요청 단위)로 측정됩니다. VM 또는 온-프레미스 서버와 달리 루는 언제든지 쉽게 확장 및 축소할 수 있습니다. 프로비저닝된 RUs 수를 즉시 변경할 수 있습니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요.

[Azure Cosmos DB 용량 계산기를](https://cosmos.azure.com/capacitycalculator/) 사용하여 데이터베이스 계정 구성, 데이터 양, 문서 크기 및 초당 필수 읽기 및 쓰기에 따라 요청 단위의 양을 결정할 수 있습니다.

다음은 필요한 R수에 영향을 주는 주요 요소입니다.
- **문서 크기**: 항목/문서의 크기가 증가함에 따라 항목/문서를 읽거나 쓰는 데 사용되는 루도 증가합니다.

- **문서 속성 수**: 문서를 만들거나 업데이트하는 데 사용되는 루의 수는 해당 속성의 수, 복잡성 및 길이와 관련이 있습니다. [인덱싱된 속성 수를 제한하여](mongodb-indexing.md)쓰기 작업에 대한 요청 단위 사용량을 줄일 수 있습니다.

- **쿼리 패턴**: 쿼리의 복잡성은 쿼리에서 사용되는 요청 단위 수에 영향을 줍니다. 

쿼리 비용을 이해하는 가장 좋은 방법은 Azure Cosmos DB의 샘플 데이터를 사용하고 명령을 사용하여 `getLastRequestStastistics` [MongoDB Shell에서 샘플 쿼리를 실행하여](connect-mongodb-account.md) 요청 요금을 받는 것입니다.

`db.runCommand({getLastRequestStatistics: 1})`

이 명령은 다음과 유사한 JSON 문서를 출력합니다.

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

[진단 설정을](cosmosdb-monitor-resource-logs.md) 사용하여 Azure Cosmos DB에 대해 실행된 쿼리의 빈도와 패턴을 이해할 수도 있습니다. 진단 로그의 결과를 저장소 계정, EventHub 인스턴스 또는 [Azure Log Analytics로](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)보낼 수 있습니다.  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>파티션 키 선택
샤딩이라고도 하는 분할은 데이터를 마이그레이션하기 전에 고려해야 할 핵심 사항입니다. Azure Cosmos DB는 완전히 관리되는 분할을 사용하여 데이터베이스의 용량을 늘려 저장소 및 처리량 요구 사항을 충족합니다. 이 기능은 라우팅 서버의 호스팅 또는 구성이 필요하지 않습니다.   

이와 유사한 방식으로 분할 기능은 자동으로 용량을 추가하고 그에 따라 데이터의 균형을 조정합니다. 데이터에 적합한 파티션 키 선택에 대한 자세한 내용 및 권장 사항은 [파티션 키 선택 문서를](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)참조하십시오. 

## <a name="index-your-data"></a><a id="indexing"></a>데이터 인덱싱
기본적으로 Azure Cosmos DB는 삽입된 모든 데이터에 대한 자동 인덱싱을 제공합니다. Azure Cosmos DB에서 제공하는 인덱싱 기능에는 복합 인덱스, 고유 지수 및 TTL(Time-to-Live) 인덱스추가가 포함됩니다. 인덱스 관리 인터페이스가 `createIndex()` 명령에 매핑됩니다. [MongoDB에 대한 Azure 코스모스 DB의 API에서 인덱싱에 대해](mongodb-indexing.md)자세히 알아보십시오.

[Azure 데이터베이스 마이그레이션 서비스는](../dms/tutorial-mongodb-cosmos-db.md) 고유한 인덱스를 사용하여 MongoDB 컬렉션을 자동으로 마이그레이션합니다. 그러나 마이그레이션 하기 전에 고유한 인덱스를 만들어야 합니다. Azure Cosmos DB는 컬렉션에 이미 데이터가 있는 경우 고유한 인덱스 생성을 지원하지 않습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](unique-keys.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [데이터베이스 마이그레이션 서비스를 사용하여 MongoDB 데이터를 Cosmos DB로 마이그레이션합니다.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량 프로비전](set-throughput.md)
* [Azure Cosmos DB에서 분할](partition-data.md)
* [Azure 코스모스 DB의 글로벌 배포](distribute-data-globally.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure 코스모스 DB의 요청 단위](request-units.md)
