---
title: Apache Cassandra 및 Azure Cosmos DB 일관성 수준
description: Apache Cassandra 및 Azure Cosmos DB 일관성 수준
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f640ad85fd34dd5a4803e7dd96f1c0283f0c859a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100432"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Apache Cassandra 및 Azure Cosmos DB 일관성 수준
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB와 달리 Apache Cassandra는 기본적으로 정확 하 게 정의 된 일관성 보장을 제공 하지 않습니다. 대신, Apache Cassandra는 높은 가용성, 일관성 및 대기 시간을 절충 하는 쓰기 일관성 수준 및 읽기 일관성 수준을 제공 합니다. Azure Cosmos DB의 Cassandra API를 사용 하는 경우:

* Apache Cassandra의 쓰기 일관성 수준은 Azure Cosmos 계정에 구성 된 기본 일관성 수준에 매핑됩니다. 쓰기 작업 (CL)의 일관성은 요청 별로 변경 될 수 없습니다.

* Azure Cosmos DB은 Cassandra client 드라이버에서 지정 된 읽기 일관성 수준을 읽기 요청에 따라 동적으로 구성 된 Azure Cosmos DB 일관성 수준 중 하나로 동적으로 매핑합니다.

## <a name="mapping-consistency-levels"></a>매핑 일관성 수준

다음 표에서는 Cassandra API를 사용할 때 기본 Cassandra 일관성 수준이 Azure Cosmos DB의 일관성 수준에 매핑되는 방법을 보여 줍니다.  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra 일관성 모델 매핑" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Azure Cosmos 계정이 강력한 일관성 이외의 일관성 수준으로 구성 된 경우에는 PBS ( *확률적 한정 된 부실* ) 메트릭을 살펴보면 클라이언트에서 작업에 대해 강력 하 고 일관 된 읽기를 수행할 수 있는 확률을 확인할 수 있습니다. 이 메트릭은 Azure Portal에서 노출됩니다. 자세히 알아보려면 [PBS(확률적 제한된 부실) 메트릭 모니터링](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)을 참조하세요.

확률적 제한된 부실은 최종이 어떻게 최종 일관성인지 보여줍니다. 이 메트릭은 현재 Azure Cosmos 계정에 구성 된 일관성 수준 보다 더 강력한 일관성을 얻을 수 있는 빈도에 대 한 정보를 제공 합니다. 즉, 쓰기 및 읽기 영역 조합에 대한 강력한 일관성 읽기를 가져오는 확률(밀리초로 제한됨)을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에 대 한 전역 배포 및 일관성 수준에 대해 자세히 알아보세요.

* [글로벌 배포 개요](distribute-data-globally.md)
* [일관성 수준 개요](consistency-levels.md)
* [고가용성](high-availability.md)
