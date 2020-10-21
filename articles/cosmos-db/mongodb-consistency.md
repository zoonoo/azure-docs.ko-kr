---
title: MongoDB에 대 한 Azure Cosmos DB API의 매핑 일관성 수준
description: MongoDB API에 대 한 Azure Cosmos DB API에 대 한 매핑 일관성 수준입니다.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 19dcc0f7605ae771e2ff826299413eaa073f212b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284107"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Azure Cosmos DB에 대 한 일관성 수준 및 MongoDB 용 API

Azure Cosmos DB와 달리 native MongoDB는 정확 하 게 정의 된 일관성 보장을 제공 하지 않습니다. 대신, 네이티브 MongoDB를 사용 하면 쓰기 우려, 읽기 관심사 및 isMaster 지시어를 사용 하 여 읽기 작업을 주 복제본 또는 보조 복제본으로 전달 하 여 원하는 일관성 수준을 달성할 수 있습니다.

MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 경우 MongoDB 드라이버는 쓰기 지역을 주 복제본으로 처리 하 고 다른 모든 지역은 읽기 복제본입니다. Azure Cosmos 계정에 연결 된 영역을 주 복제본으로 선택할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB에 대 한 기본 일관성 모델은 Session입니다. Session은 Cassandra 또는 MongoDB에서 기본적으로 지원 하지 않는 클라이언트 중심의 일관성 모델입니다. 선택할 일관성 모델에 대 한 자세한 내용은 [Azure Cosmos DB의 일관성 수준](consistency-levels.md) 을 참조 하십시오.

MongoDB에 Azure Cosmos DB API를 사용 하는 동안:

* 쓰기 문제는 Azure Cosmos 계정에 구성 된 기본 일관성 수준에 매핑됩니다.

* Azure Cosmos DB은 MongoDB 클라이언트 드라이버에 지정 된 읽기 문제를 읽기 요청에 대해 동적으로 구성 된 Azure Cosmos DB 일관성 수준 중 하나로 동적으로 매핑합니다.  

* Azure Cosmos 계정에 연결 된 특정 지역에는 해당 지역을 쓰기 가능한 첫 번째 지역으로 만들어 주석을 달 수 있습니다. 

## <a name="mapping-consistency-levels"></a>매핑 일관성 수준

다음 표에서는 MongoDB에 대 한 Azure Cosmos DB의 API를 사용 하는 경우 기본 MongoDB 쓰기/읽기 관련 사항을 Azure Cosmos 일관성 수준에 매핑하는 방법을 보여 줍니다.

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB 일관성 모델 매핑" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Azure Cosmos 계정이 강력한 일관성 이외의 일관성 수준으로 구성 된 경우에는 PBS ( *확률적 한정 된 부실* ) 메트릭을 살펴보면 클라이언트에서 작업에 대해 강력 하 고 일관 된 읽기를 수행할 수 있는 확률을 확인할 수 있습니다. 이 메트릭은 Azure Portal에서 노출됩니다. 자세히 알아보려면 [PBS(확률적 제한된 부실) 메트릭 모니터링](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)을 참조하세요.

확률적 제한된 부실은 최종이 어떻게 최종 일관성인지 보여줍니다. 이 메트릭은 현재 Azure Cosmos 계정에 구성 된 일관성 수준 보다 더 강력한 일관성을 얻을 수 있는 빈도에 대 한 정보를 제공 합니다. 즉, 쓰기 및 읽기 영역 조합에 대한 강력한 일관성 읽기를 가져오는 확률(밀리초로 제한됨)을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에 대 한 전역 배포 및 일관성 수준에 대해 자세히 알아보세요.

* [글로벌 배포 개요](distribute-data-globally.md)
* [일관성 수준 개요](consistency-levels.md)
* [고가용성](high-availability.md)
