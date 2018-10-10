---
title: Azure Cosmos DB 글로벌 배포의 주요 이점
description: Azure Cosmos DB 다중 마스터, 지역에서 복제의 주요 이점, 도움이 되는 다중 마스터 및 사용 사례에 대해 알아봅니다.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968351"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Azure Cosmos DB로 데이터 글로벌 배포

이 문서에서는 데이터 글로벌 배포의 주요 이점과 데이터 글로벌 배포가 필요한 몇 가지 실시간 시나리오를 설명합니다.

## <a name="key-benefits"></a>주요 이점

다음은 Azure Cosmos DB 글로벌 데이터 배포 기능을 활용하는 계정에서 사용할 수 있는 주요 이점입니다.

* **1자리 대기 시간** - Azure Cosmos DB는 99번째 백분위수에서 10ms 미만의 읽기 및 쓰기 대기 시간을 제공하며, [재정적으로 지원되는 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)에서 보장합니다.

* **5~9의 가용성** – Azure Cosmos DB는 99.999%의 읽기 및 쓰기 가용성을 제공하며, [재정적으로 지원되는 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)에서 보장합니다.

* **유연한 충돌 해결** – 다중 마스터 기능을 활용하는 고객에 대해 Azure Cosmos DB는 글로벌 데이터 무결성과 일관성을 보장하기 위해 충돌을 처리하는 세 가지 모드를 제공합니다.

* **튜닝 가능한 일관성** – Azure Cosmos DB는 멀티 마스터링 기능을 갖춘 계정에 대해 강력한 일관성을 지원하는 글로벌 배포를 고려하여 5가지 [일관성 수준](consistency-levels.md)을 지원합니다.

* **암시적 내결함성** – 여러 지역에서 복제된 데이터를 사용하면 지역별로 가동이 중단되더라도 응용 프로그램의 내결함성이 높아집니다.

## <a name="use-cases"></a>사용 사례

다음은 Azure Cosmos DB 글로벌 배포 기능의 이점을 활용할 수 있는 시나리오의 일부 샘플입니다.

* **소셜 미디어 앱** – 소셜 미디어 응용 프로그램은 전 세계 사용자에게 훌륭한 환경을 제공하기 위해 낮은 대기 시간, 높은 가용성 및 확장성이 필요합니다.

* **IoT** - 지리적으로 분산된 에지 배포는 여러 위치에서 시계열 데이터를 추적해야 하는 경우가 많습니다. 각 장치는 가장 가까운 지역에 속할 수 있습니다. 장치가 다른 위치로 이동하면 해당 장치를 가장 가까이에서 사용 가능한 지역에 쓸 수 있도록 이동할 수 있습니다.

* **전자상거래** - 전자 상거래는 가용성이 높고 대기 시간도 매우 짧아야 합니다. 읽기 및 쓰기로 데이터를 지리적으로 배포하면 사용자에게 가깝게 데이터를 배치하여 응용 프로그램의 응답성을 높일 수 있습니다. 여러 지역에서 읽기 및 쓰기가 모두 가능하기 때문에 뛰어난 가용성이 제공됩니다.

* **사기 행위/이상 탐지**  - 사용자 작업 및 계정 작업을 모니터링하는 응용 프로그램은 전역으로 배포되는 경우가 많고 점수를 업데이트하여 위험 메트릭스를 인라인으로 유지하기 위해 여러 이벤트를 동시에 추적해야 합니다.

* **측광**  - Azure Cosmos DB 다중 마스터를 사용하여 사용량(예: API 호출, 사용되는 트랜잭션/초, 분) 계산 및 규제를 간단하게 전역적으로 구현할 수 있습니다. 기본 제공 충돌 해결은 실시간으로 계산 및 규제의 정확도를 보장합니다.

## <a name="next-steps"></a>다음 단계  

이 문서에서는 Azure Cosmos DB 글로벌 데이터 배포 기능의 주요 이점과 사용 사례에 대해 알아보았습니다. 다음으로 아래 리소스를 살펴보겠습니다.

* [Azure Cosmos DB에서 턴키 글로벌 배포를 사용하도록 설정하는 방법](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB 글로벌 데이터베이스 복제를 구성하는 방법](tutorial-global-distribution-sql-api.md)

* [Azure Cosmos DB 계정에 대해 다중 마스터를 사용하도록 설정하는 방법](enable-multi-master.md)

* [Azure Cosmos DB에서 자동 및 수동 장애 조치(failover)가 작동하는 방법](regional-failover.md)

* [Azure Cosmos DB의 충돌 해결 이해](multi-master-conflict-resolution.md)

* [오픈 소스 NoSQL 데이터베이스로 다중 마스터 사용](multi-master-oss-nosql.md)
