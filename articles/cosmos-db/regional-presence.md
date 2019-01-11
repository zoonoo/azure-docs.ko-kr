---
title: Azure Cosmos DB의 지역 현재 상태
description: 이 문서에서는 Azure Cosmos DB 및 여러 클라우드 환경의 지역 현재 상태에 대해 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 5a4523405f7a9182bb5123ebacaebc9a9e5ae9a9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038666"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Azure Cosmos DB의 지역 현재 상태

현재, Azure는 [54개 지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 사용할 수 있습니다. Azure Cosmos DB는 Azure의 기본 서비스이며 Azure를 사용할 수 있는 모든 지역에서 사용할 수 있습니다.

[![Azure Cosmos DB를 사용할 수 있는 지역](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB는 고객이 사용할 수 있는 5개의 고유한 Azure 클라우드 환경에서 사용할 수 있습니다.

* **Azure 공용** 클라우드: 전역적으로 사용할 수 있습니다.

* **Azure China**: Microsoft와 중국 최대의 인터넷 공급자인 21Vianet의 제휴를 통해 사용할 수 있습니다.

* **Azure Germany**: 데이터 트러스티 모델에 따라 서비스를 제공하며 고객 데이터는 Deutsche Telekom 자회사 T-Systems International GmbH가 독일 데이터 신탁 기관으로서 독일에서 관리합니다.

* **Azure Government**: 미국 4개 지역에서 미국 정부 기관 및 해당 파트너가 사용할 수 있습니다. 

* **American Department of Defense(미국방부)**: 미국 2개 지역에서 미국방부가 사용할 수 있습니다.

## <a name="regional-presence-with-global-distribution"></a>전역 배포가 지원되는 지역 현재 상태

Azure Cosmos DB에 노출되는 여러 다른 API(SQL, MongoDB, Cassandra, Gremlin 및 Azure Table 스토리지 포함)은 모든 Azure 지역에서 사용할 수 있습니다. 예를 들어, MongoDB 및 Cassandra API는 모든 전역 Azure 지역 뿐만 아니라 Azure 소버린 지역[예: 중국, 독일, 정부 및 DoD(국방부) 지역]의 Azure Cosmos DB에서 노출될 수 있습니다.

Azure Cosmos DB는 [전역적으로 분산되는](distribute-data-globally.md) 데이터베이스입니다. 제한 없는 수의 Azure 지역을 Azure Cosmos 계정과 연결할 수 있으며 데이터는 자동으로 투명하게 복제됩니다. 언제든지 Azure Cosmos 계정에서 지역을 추가 또는 제거할 수 있습니다. 턴키 전역 배포 기능 및 다중 마스터 복제 프로토콜을 사용하여 Azure Cosmos DB는 99번째 백분위수에서 10밀리초 미만의 읽기 및 쓰기 대기 시간, 99.999의 읽기 및 쓰기 가용성, Azure Cosmos 계정과 연결된 모든 지역에서 읽기 및 쓰기의 프로비전된 처리량을 탄력적으로 조정하는 기능을 제공합니다. 또한 Azure Cosmos DB는 5개의 잘 정의된 일관성 모델을 제공하고 특정 일관성 모델을 데이터에 적용하도록 선택할 수 있습니다. 마지막으로, Azure Cosmos DB는 프로비전된 처리량, 99번째 백분위 수, 고가용성 및 일관성을 아우르는 포괄적인 SLA(서비스 수준 계약)를 제공하는 업계의 유일한 데이터베이스 서비스입니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 여러 다른 개념을 배울 수 있습니다.

* [글로벌 데이터 배포](distribute-data-globally.md)
* [Azure Cosmos DB 계정을 관리하는 방법](manage-account.md)
* [Cosmos DB 컨테이너 및 데이터베이스에 대한 처리량 프로비전](set-throughput.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
