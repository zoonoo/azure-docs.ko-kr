---
title: Apache Cassandra 및 Azure Cosmos DB 일관성 수준
description: Apache Cassandra 및 Azure Cosmos DB 일관성 수준입니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 8159fa7574830a6754a409245f7fca4590a0c5c3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536086"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra 및 Azure Cosmos DB Cassandra API 일관성 수준
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB와 달리 Apache Cassandra는 기본적으로 정확하게 정의된 일관성을 보장하지 않습니다. 대신 Apache Cassandra는 고가용성, 일관성, 대기 시간을 절충할 수 있도록 쓰기 일관성 수준과 읽기 일관성 수준을 제공합니다. Azure Cosmos DB의 Cassandra API를 사용하는 경우 수행되는 작업은 다음과 같습니다.

* Apache Cassandra의 쓰기 일관성 수준은 Azure Cosmos 계정에 구성된 기본 일관성 수준에 매핑됩니다. 쓰기 작업(CL)의 일관성은 요청마다 변경될 수 없습니다.

* Azure Cosmos DB는 Cassandra 클라이언트 드라이버에서 지정한 읽기 일관성 수준을, 읽기 요청에 동적으로 구성된 Azure Cosmos DB 일관성 수준 중 하나에 동적으로 매핑합니다.

## <a name="multi-region-writes-vs-single-region-writes"></a>다중 지역 쓰기와 단일 지역 쓰기 비교

Apache Cassandra 데이터베이스는 기본적으로 다중 마스터 시스템이며, 다중 지역 복제를 사용하는 단일 지역 쓰기를 위한 기본 제공 옵션을 읽기에 제공하지 않습니다. 그러나 Azure Cosmos DB는 단일 지역 또는 [다중 지역](../how-to-multi-master.md) 쓰기 구성을 포함하는 턴키 기능을 제공합니다. 여러 지역에서 단일 지역 쓰기 구성을 선택할 수 있으면 지역 간 충돌 시나리오를 방지하고 여러 지역에서 강력한 일관성을 유지할 수 있다는 이점이 있습니다. 

단일 지역 쓰기를 사용하면 [자동 장애 조치(failover)](../high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)를 사용하여 지역 간에 고가용성 수준을 유지하면서 강력한 일관성을 유지할 수 있습니다. 이 구성에서는 요청마다 결과적 일관성으로 다운그레이드함으로써, 데이터 지역성을 계속 이용하여 읽기 대기 시간을 줄일 수 있습니다. 이 기능 외에도 Azure Cosmos DB 플랫폼은 지역을 선택할 때 [영역 중복성](../high-availability.md#availability-zone-support)을 사용하도록 설정하는 기능을 제공합니다. 따라서 기본 Apache Cassandra와 달리 Azure Cosmos DB를 사용하면 CAP 정리 [절충 스펙트럼](../consistency-levels.md#rto)을 더 세부적으로 살펴볼 수 있습니다.

## <a name="mapping-consistency-levels"></a>매핑 일관성 수준

Azure Cosmos DB 플랫폼은 [CAP 정리](https://en.wikipedia.org/wiki/CAP_theorem)와 [PACLC 정리](https://en.wikipedia.org/wiki/PACELC_theorem)에서 정의된 복제 및 절충과 관련하여 잘 정의된 비즈니스 사용 사례 중심의 일관성 설정 5개를 제공합니다. 이 접근 방식은 Apache Cassandra와 크게 다르므로 관련 [설명서](../consistency-levels.md)에서 Azure Cosmos DB 일관성 설정을 검토하고 이해하거나 짧은 [동영상](https://www.youtube.com/watch?v=t1--kZjrG-o) 가이드를 시청하여 Azure Cosmos DB 플랫폼의 일관성 설정을 이해하는 것이 좋습니다.

다음 표에서는 Cassandra API를 사용할 때 Apache Cassandra 일관성 수준과 Azure Cosmos DB 일관성 수준 간에 가능한 매핑을 보여 줍니다. 이를 통해 단일 지역, 단일 지역 쓰기가 있는 다중 지역 읽기, 다중 지역 쓰기의 구성을 볼 수 있습니다.

> [!NOTE]
> 다음 표에서 보여 주는 매핑은 정확하지는 않습니다. 대신 Apache Cassandra와 가장 유사한 것을 제공했으며 맨 오른쪽 열에서 질적인 차이를 명확하게 나타냈습니다. 위에서 설명한 대로 Azure Cosmos DB의 [일관성 설정](../consistency-levels.md)을 검토하는 것이 좋습니다. 

:::image type="content" source="./media/apache-cassandra-consistency-mapping/account.png" alt-text="Cassandra 일관성 계정 수준 매핑" lightbox="./media/apache-cassandra-consistency-mapping/account.png" :::

:::image type="content" source="./media/apache-cassandra-consistency-mapping/dynamic.png" alt-text="Cassandra 일관성 동적 매핑" lightbox="./media/apache-cassandra-consistency-mapping/dynamic.png" :::

Azure Cosmos 계정이 강력한 일관성 이외의 일관성 수준으로 구성된 경우 PBS(‘확률적 제한된 부실’) 메트릭을 확인하여 워크로드에 대한 강력한 일관성 읽기를 가져오도록 클라이언트의 확률을 확인할 수 있습니다. 이 메트릭은 Azure Portal에서 노출됩니다. 자세히 알아보려면 [PBS(확률적 제한된 부실) 메트릭 모니터링](../how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)을 참조하세요.

확률적 제한된 부실은 최종이 어떻게 최종 일관성인지 보여줍니다. 이 메트릭은 현재 Azure Cosmos 계정에서 구성한 일관성 수준보다 얼마나 자주 더 강력한 일관성을 가져올 수 있는지에 대한 통찰력을 제공합니다. 즉, 쓰기 및 읽기 영역 조합에 대한 강력한 일관성 읽기를 가져오는 확률(밀리초로 제한됨)을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 전역 배포 및 일관성 수준에 관해 자세히 알아봅니다.

* [글로벌 배포 개요](../distribute-data-globally.md)
* [일관성 수준 개요](../consistency-levels.md)
* [고가용성](../high-availability.md)
