---
title: Azure Cosmos DB로 데이터 글로벌 배포 | Microsoft Docs
description: 글로벌 분산형 다중 모델 데이터베이스 서비스인 Azure Cosmos DB에서 글로벌 데이터베이스를 사용한 전 세계적 지역 복제, 다중 마스터, 장애 조치(Failover) 및 데이터 복구에 대해 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408898"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용한 글로벌 데이터 분산

Azure는 어디에나 존재합니다. 전 세계 50개 이상의 지역에서 사용되며 계속해서 확장 중입니다. 이처럼 글로벌로 사용되며 다중 마스터 지원 덕분에 Azure가 개발자에게 제공하는 차별화된 기능 중 하나는 글로벌 분산형 응용 프로그램을 간편하게 빌드, 배포 및 관리하는 능력입니다.

[Azure Cosmos DB](../cosmos-db/introduction.md)는 업무에 중요한 응용 프로그램에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB는 [업계 최고의 포괄적인 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)로 지원되는 턴키 글로벌 분산, 전 세계적인 [처리량 및 저장소의 탄력적인 확장](../cosmos-db/partition-data.md), 99번째 백분위수의 밀리초 대기 시간 1자리 수 읽고 쓰기, [잘 정의된 일관성 모델](consistency-levels.md) 및 보장된 고가용성을 제공합니다. Azure Cosmos DB는 사용자가 스키마 또는 인덱스 관리를 처리하지 않아도 되도록 [모든 데이터를 자동으로 인덱싱](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)합니다.

## <a name="global-distribution-with-multi-master"></a>다중 마스터를 사용한 글로벌 분산

클라우드 서비스인 Azure Cosmos DB는 문서, 키 값, 그래프 및 열 패밀리 데이터 모델에 대한 다중 테넌트, 글로벌 분산 및 다중 마스터를 지원하도록 신중하게 설계되었습니다.

![Azure Cosmos DB 컨테이터를 분할하여 세 하위 지역에 분산](./media/distribute-data-globally/global-apps.png)

**단일 Azure Cosmos DB 컨테이너를 분할하여 여러 Azure 하위 지역에 분산**

우리가 Azure Cosmos DB를 개발하면서 알게 된 사실은 전역 분산을 나중에 추가할 수 없다는 것입니다. 전역 분산은 "단일 사이트" 데이터베이스 시스템 위에 "추가"할 수 없습니다. 글로벌 분산형 데이터베이스가 제공하는 기능은 "단일 사이트" 데이터베이스가 제공하는 기존의 Geo-DR(지역 재해 복구) 기능보다 훨씬 우수합니다. 지역 재해 복구를 제공하는 단일 사이트 데이터베이스는 전역에 분산되는 데이터베이스의 엄격한 하위 집합입니다.

Azure Cosmos DB 턴키 글로벌 분산을 사용하면 개발자는 데이터베이스 로그에서 람다 패턴을 사용하거나 여러 지역에 걸쳐 "이중 쓰기"를 수행하여 개발자 고유의 복제 스캐폴딩을 빌드할 필요가 없습니다. 이러한 접근 방식은 정확성을 유지하고 우수한 SLA를 제공하는 것이 불가능하기 때문에 권장하지 *않습니다*.

## <a id="Next Steps"></a>다음 단계

* [Azure Cosmos DB에서 턴키 글로벌 분산을 사용하도록 설정하는 방법](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB 글로벌 분산의 주요 이점](distribute-data-globally-benefits.md)

* [Azure Cosmos DB 글로벌 데이터베이스 복제를 구성하는 방법](tutorial-global-distribution-sql-api.md)

* [Azure Cosmos DB 계정에 대해 다중 마스터를 사용하도록 설정하는 방법](enable-multi-master.md)

* [Azure Cosmos DB에서 자동 및 수동 장애 조치(failover)가 작동하는 방법](regional-failover.md)

* [Azure Cosmos DB의 충돌 해결 이해](multi-master-conflict-resolution.md)

* [오픈 소스 NoSQL 데이터베이스로 다중 마스터 사용](multi-master-oss-nosql.md)
