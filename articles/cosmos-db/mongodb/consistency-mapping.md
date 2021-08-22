---
title: Azure Cosmos DB API for MongoDB에 대한 매핑 일관성 수준
description: Azure Cosmos DB API for MongoDB에 대한 매핑 일관성 수준입니다.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: fa434fc45b46353d04a22000f9f2f252898cb715
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535559"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Azure Cosmos DB 및 API for MongoDB에 대한 일관성 수준
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB와 달리 네이티브 MongoDB는 정확하게 정의된 일관성 보장을 제공하지 않습니다. 대신, 네이티브 MongoDB를 사용하면 사용자가 읽기 작업을 주 복제본이나 보조 복제본으로 보내 원하는 일관성 수준을 달성하기 위해 쓰기 문제, 읽기 문제, isMaster 지시문과 같은 일관성 보장을 구성할 수 있습니다.

Azure Cosmos DB의 API for MongoDB를 사용하는 경우 MongoDB 드라이버는 쓰기 지역을 주 복제본으로 처리하고, 다른 모든 지역은 읽기 복제본이 됩니다. Azure Cosmos 계정에 주 복제본으로 연결되는 지역을 선택할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB의 기본 일관성 모델은 세션입니다. 세션은 Cassandra 또는 MongoDB에서 기본적으로 지원되지 않는 클라이언트 중심 일관성 모델입니다. 선택할 일관성 모델에 대한 자세한 내용은 [Azure Cosmos DB의 일관성 수준](../consistency-levels.md)을 참조하세요.

Azure Cosmos DB의 API for MongoDB를 사용하는 동안 다음과 같은 작업이 가능합니다.

* 쓰기 문제가 Azure Cosmos 계정에 구성된 기본 일관성 수준에 매핑됩니다.

* Azure Cosmos DB가 MongoDB 클라이언트 드라이버에서 지정된 읽기 문제를 읽기 요청에서 동적으로 구성된 Azure Cosmos DB 일관성 수준 중 하나에 동적으로 매핑합니다.  

* Azure Cosmos 계정과 연결된 특정 지역을 쓰기 가능한 첫 번째 지역으로 만들어 “주 지역”으로 주석을 지정할 수 있습니다. 

## <a name="mapping-consistency-levels"></a>매핑 일관성 수준

다음 표에서는 Azure Cosmos DB의 API for MongoDB를 사용하는 경우 네이티브 MongoDB 쓰기/읽기 문제가 Azure Cosmos 일관성 수준에 매핑되는 방식을 보여 줍니다.

:::image type="content" source="../media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB 일관성 모델 매핑" lightbox= "../media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Azure Cosmos 계정이 강력한 일관성 이외의 일관성 수준으로 구성된 경우 PBS(‘확률적 제한된 부실’) 메트릭을 통해 클라이언트가 워크로드에 대한 강력하고 일관된 읽기를 얻을 수 있는 확률을 확인할 수 있습니다. 이 메트릭은 Azure Portal에서 노출됩니다. 자세히 알아보려면 [PBS(확률적 제한된 부실) 메트릭 모니터링](../how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)을 참조하세요.

확률적 제한된 부실은 최종이 어떻게 최종 일관성인지 보여줍니다. 이 메트릭은 현재 Azure Cosmos 계정에서 구성한 일관성 수준보다 얼마나 자주 더 강력한 일관성을 가져올 수 있는지에 대한 통찰력을 제공합니다. 즉, 쓰기 및 읽기 영역 조합에 대한 강력한 일관성 읽기를 가져오는 확률(밀리초로 제한됨)을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 전역 배포 및 일관성 수준에 관해 자세히 알아봅니다.

* [글로벌 배포 개요](../distribute-data-globally.md)
* [일관성 수준 개요](../consistency-levels.md)
* [고가용성](../high-availability.md)
