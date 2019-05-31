---
title: Azure Cosmos DB의 API for MongoDB 소개
description: Azure Cosmos DB의 API for MongoDB를 사용하여 Azure Cosmos DB로 대량의 데이터를 저장하고 쿼리하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 05/20/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 22438837a7cee1755d115993683101c260b99f2c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953839"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB

[Azure Cosmos DB](introduction.md)는 업무에 중요한 애플리케이션에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB는 [업계 최고의 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)로 지원되는 [턴키 전역 분산](distribute-data-globally.md), 전 세계적으로 [처리량 및 저장소의 탄력적인 확장](partition-data.md), 99번째 백분위수의 1자리 수 밀리초 크기 대기 시간, 보장된 고가용성을 제공합니다. Azure Cosmos DB는 사용자가 스키마 및 인덱스 관리를 처리하지 않아도 되도록 [데이터를 자동으로 인덱싱](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)합니다. 또한 다중 모델 방식이며, 문서, 키-값, 그래프 및 열 형식 데이터 모델을 지원합니다. 기본적으로 SQL API를 사용하여 Cosmos DB와 상호 작용할 수 있습니다. 또한 Cosmos DB 서비스는 Cassandra, MongoDB, Gremlin 및 Azure Table Storage를 비롯한 일반 NoSQL API를 위한 유선 프로토콜을 구현합니다. 따라서 고객은 익숙한 NoSQL 클라이언트 드라이버 및 도구를 사용하여 Cosmos 데이터베이스와 상호 작용할 수 있습니다.

## <a name="wire-protocol-compatibility"></a>유선 프로토콜 호환성

Cosmos DB는 Cassandra, MongoDB, Gremlin 및 Azure Table Storage를 비롯한 일반 NoSQL 데이터베이스를 위한 유선 프로토콜을 구현합니다. 기본적으로 Cosmos DB 내에서 유선 프로토콜을 직접, 효율적으로 구현하므로 NoSQL 데이터베이스의 기존 클라이언트 SDK, 드라이버 및 도구가 Cosmos DB와 투명하게 상호 작용할 수 있습니다. Cosmos DB는 NoSQL 데이터베이스에 유선 호환 API를 제공하기 위해 데이터베이스의 소스 코드를 사용하지 않습니다.

기본적으로 Azure Cosmos DB의 API for MongoDB는 MongoDB의 유선 프로토콜 버전 3.2와 호환됩니다. 유선 프로토콜 버전 3.4에 추가된 기능 또는 쿼리 연산자는 현재 미리 보기로 제공됩니다. 이러한 프로토콜 버전을 이해하는 모든 MongoDB 클라이언트 드라이버는 기본적으로 Cosmos DB에 연결할 수 있어야 합니다.

![Azure Cosmos DB의 API for MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

## <a name="key-benefits"></a>주요 이점

전역적으로 배포되는 완전 관리형 Database as a Service인 Cosmos DB의 주요 이점은 [여기](introduction.md)에 설명되어 있습니다. 또한 인기 있는 NoSQL API의 유선 프로토콜을 기본적으로 구현하는 Cosmos DB는 다음과 같은 혜택을 제공합니다.

* 애플리케이션 논리의 상당 부분을 유지하면서도 애플리케이션을 Cosmos DB로 간편하게 마이그레이션할 수 있습니다.
* 계속해서 애플리케이션의 이식성을 유지하고 클라우드 공급업체에 종속되지 않습니다.
* Cosmos DB 기반의 일반 NoSQL API에 업계 최고 수준의 재정적 지원이 포함된 SLA를 제공합니다.
* 요구 사항에 따라 프로비전된 처리량 및 스토리지 크기를 탄력적으로 조정하여 필요한 처리량 및 스토리지에 대한 요금만 지불하면 됩니다. 이를 통해 상당한 비용을 절감할 수 있습니다.
* 다중 마스터 복제를 사용한 턴키, 글로벌 배포.

## <a name="cosmos-dbs-api-for-mongodb"></a>Cosmos DB의 API for MongoDB

빠른 시작에 따라 Cosmos 계정을 만들고, Azure Cosmos DB를 사용하도록 기존 MongoDB 애플리케이션을 마이그레이션하거나 새로 빌드합니다.

* [기존 MongoDB Node.js 웹앱 마이그레이션](create-mongodb-nodejs.md)
* [Azure Cosmos DB의 API for MongoDB 및 .NET SDK를 사용하여 웹앱 빌드](create-mongodb-dotnet.md)
* [Azure Cosmos DB의 API for MongoDB 및 Java SDK를 사용하여 콘솔 앱 빌드](create-mongodb-java.md)

## <a name="next-steps"></a>다음 단계

다음은 시작하기 위한 몇 가지 지침입니다.

* [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md) 자습서에 따라 계정 연결 문자열 정보를 가져오는 방법을 알아봅니다.
* [Azure Cosmos DB에 Studio 3T 사용](mongodb-mongochef.md) 자습서에 따라 Studio 3T에서 Cosmos 데이터베이스와 MongoDB 앱을 연결하는 방법을 알아봅니다.
* [Azure Cosmos DB로 MongoDB 데이터 가져오기](mongodb-migrate.md) 자습서에 따라 Cosmos 데이터베이스로 데이터를 가져옵니다.
* [Robo 3T](mongodb-robomongo.md)를 사용하여 Cosmos 계정에 연결
* [전역적으로 분산되는 앱의 읽기 기본 설정 구성](../cosmos-db/tutorial-global-distribution-mongodb.md) 방법을 알아봅니다.

<sup>참고: 이 문서에서는 MongoDB 데이터베이스와 유선 프로토콜 호환성을 제공하는 Azure Cosmos DB 기능을 설명합니다. Microsoft는 이 서비스를 제공하기 위해 MongoDB 데이터베이스를 실행하지 않습니다. Azure Cosmos DB는 MongoDB, Inc.와 관련이 없습니다.</sup>
