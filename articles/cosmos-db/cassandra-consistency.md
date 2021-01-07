---
title: Apache Cassandra 및 Azure Cosmos DB 일관성 수준
description: Apache Cassandra 및 Azure Cosmos DB 일관성 수준
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516822"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Apache Cassandra 및 Azure Cosmos DB Cassandra API 일관성 수준
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB와 달리 Apache Cassandra는 기본적으로 정확 하 게 정의 된 일관성 보장을 제공 하지 않습니다. 대신, Apache Cassandra는 높은 가용성, 일관성 및 대기 시간을 절충 하는 쓰기 일관성 수준 및 읽기 일관성 수준을 제공 합니다. Azure Cosmos DB의 Cassandra API를 사용 하는 경우:

* Apache Cassandra의 쓰기 일관성 수준은 Azure Cosmos 계정에 구성 된 기본 일관성 수준에 매핑됩니다. 쓰기 작업 (CL)의 일관성은 요청 별로 변경 될 수 없습니다.

* Azure Cosmos DB은 Cassandra client 드라이버에서 지정 된 읽기 일관성 수준을 읽기 요청에 따라 동적으로 구성 된 Azure Cosmos DB 일관성 수준 중 하나로 동적으로 매핑합니다.

## <a name="multi-region-writes-vs-single-region-writes"></a>다중 지역 쓰기 및 단일 지역 쓰기

Apache Cassandra 데이터베이스는 기본적으로 다중 마스터 시스템 이며 읽기에 대 한 다중 지역 복제를 사용 하 여 단일 지역 쓰기에 대 한 기본 옵션을 제공 하지 않습니다. 그러나 Azure Cosmos DB는 단일 지역 또는 [다중 지역](how-to-multi-master.md) 쓰기 구성을 제공 하는 턴키 기능을 제공 합니다. 여러 지역에서 단일 지역 쓰기 구성을 선택할 수 있는 이점 중 하나는 지역 간 충돌 시나리오를 방지 하 고 여러 지역에서 강력한 일관성을 유지 하는 옵션입니다. 

단일 지역 쓰기를 사용 하면 강력한 일관성을 유지할 수 있을 뿐 아니라 [자동 장애 조치 (failover)](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)를 사용 하는 지역에서 고가용성 수준을 유지할 수 있습니다. 이 구성에서는 요청 단위로 최종 일관성으로 다운 그레이드 하 여 읽기 대기 시간을 줄이기 위해 데이터 집약성을 악용할 수 있습니다. 이러한 기능 외에도 Azure Cosmos DB 플랫폼은 영역을 선택할 때 [영역 중복성](high-availability.md#availability-zone-support) 을 사용 하도록 설정 하는 기능을 제공 합니다. 따라서 네이티브 Apache Cassandra와 달리 Azure Cosmos DB를 사용 하면 CAP 정리 [절충 스펙트럼](consistency-levels.md#rto) 을 보다 세부적으로 탐색할 수 있습니다.

## <a name="mapping-consistency-levels"></a>매핑 일관성 수준

Azure Cosmos DB 플랫폼은 복제와 관련 하 여 5 개의 잘 정의 된 비즈니스 사용 사례 지향 일관성 설정 및 [CAP 정리](https://en.wikipedia.org/wiki/CAP_theorem) 및 [PACLC 정리](https://en.wikipedia.org/wiki/PACELC_theorem)에 정의 된 장단점을 제공 합니다. 이 접근 방식은 Apache Cassandra와 크게 다르므로 [설명서](consistency-levels.md)에서 일관성 설정을 검토 하 고 Azure Cosmos DB 이해 하는 데 시간을 투자 하는 것이 좋으며,이 짧은 [비디오](https://www.youtube.com/watch?v=t1--kZjrG-o) 가이드를 시청 하 여 Azure Cosmos DB 플랫폼의 일관성 설정을 이해 하는 것이 좋습니다.

다음 표에서는 Cassandra API를 사용할 때 Apache Cassandra와 Azure Cosmos DB 일관성 수준 간의 가능한 매핑을 보여 줍니다. 단일 지역에 대 한 구성, 단일 지역 쓰기를 사용 하는 다중 지역 읽기 및 다중 지역 쓰기를 표시 합니다.

> [!NOTE]
> 이러한 매핑은 정확한 매핑이 아닙니다. 대신 Apache Cassandra에 가장 가까운 analogues을 제공 하 고 가장 오른쪽 열에서 질적 차를 명확 하 게 구분 합니다. 위에서 설명한 것 처럼 Azure Cosmos DB의 [일관성 설정을](consistency-levels.md)검토 하는 것이 좋습니다. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Cassandra 일관성 계정 수준 매핑" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Cassandra 일관성 동적 매핑" lightbox="./media/cassandra-consistency/dynamic.png" :::

Azure Cosmos 계정이 강력한 일관성 이외의 일관성 수준으로 구성 된 경우에는 PBS ( *확률적 한정 된 부실* ) 메트릭을 살펴보면 클라이언트에서 작업에 대해 강력 하 고 일관 된 읽기를 수행할 수 있는 확률을 확인할 수 있습니다. 이 메트릭은 Azure Portal에서 노출됩니다. 자세히 알아보려면 [PBS(확률적 제한된 부실) 메트릭 모니터링](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)을 참조하세요.

확률적 제한된 부실은 최종이 어떻게 최종 일관성인지 보여줍니다. 이 메트릭은 현재 Azure Cosmos 계정에 구성 된 일관성 수준 보다 더 강력한 일관성을 얻을 수 있는 빈도에 대 한 정보를 제공 합니다. 즉, 쓰기 및 읽기 영역 조합에 대한 강력한 일관성 읽기를 가져오는 확률(밀리초로 제한됨)을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에 대 한 전역 배포 및 일관성 수준에 대해 자세히 알아보세요.

* [글로벌 배포 개요](distribute-data-globally.md)
* [일관성 수준 개요](consistency-levels.md)
* [고가용성](high-availability.md)
