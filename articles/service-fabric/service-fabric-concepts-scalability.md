---
title: "Service Fabric 서비스의 확장성 | Microsoft Docs"
description: "서비스 패브릭 서비스의 규모를 조정하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: appi101
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: aprameyr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 88a30547528e3e39f5d36d09fc823700f7263cba


---
# <a name="scaling-service-fabric-applications"></a>서비스 패브릭 응용 프로그램 크기 조정
Azure 서비스 패브릭을 사용하면 클러스터의 모든 노드에서 부하 분산 서비스, 파티션 및 복제본에 의해 확장 가능한 응용 프로그램의 빌드가 쉬워집니다. 최대 리소스 사용률을 가능하게 합니다.

서비스 패브릭 응용 프로그램의 높은 규모는 다음 두 가지 방법으로 얻을 수 있습니다.

1. 파티션 수준에서 크기 조정
2. 서비스 이름 수준에서 크기 조정

## <a name="scaling-at-the-partition-level"></a>파티션 수준에서 크기 조정
서비스 패브릭은 개별 서비스를 여러 개의 작은 파티션으로 분할하는 것을 지원합니다. [분할 개요](service-fabric-concepts-partitioning.md) 는 지원되는 분할 체계의 유형에 대한 정보를 제공합니다. 각 파티션의 복제본은 클러스터의 노드 간에 분산됩니다. 하위 키 0, 상위 키 99 및 파티션 4개의 범위 지정 분할 체계를 사용하는 서비스를 상정해 봅니다. 3노드 클러스터에서 서비스는 다음과 같이 각 노드에서 리소스를 공유하는 4개의 복제본으로 배치될 수 있습니다.

![3개의 노드가 있는 파티션 레이아웃](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

노드 수를 늘리면 서비스 패브릭이 일부 복제본을 빈 노드로 이동시켜서 새 노드의 리소스를 활용할 수 있게 됩니다. 노드 수를 4개로 증가시키면 서비스가 서로 다른 파티션의 각 노드에서 실행되는 3개의 복제본을 갖게 되어 리소스 사용률 및 성능이 더 좋아집니다.

![4개의 노드가 있는 파티션 레이아웃](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>서비스 이름 수준에서 크기 조정
서비스 인스턴스는 응용 프로그램 이름 및 서비스 형식 이름의 특정 인스턴스입니다( [서비스 패브릭 응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)참조). 서비스를 만드는 동안 사용할 파티션 구성표를 지정합니다( [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)참조).

크기 조정의 첫 번째 수준은 서비스 이름에 의해 조정됩니다. 기존 서비스 인스턴스가 바빠지면 분할의 다른 수준으로 서비스의 새 인스턴스를 만들 수 있습니다. 그러면 새 서비스 소비자가 더 바쁜 서비스 인스턴스 대신 덜 바쁜 서비스 인스턴스를 사용할 수 있습니다.

용량을 늘리고 파티션 수를 늘리거나 줄이는 한 가지 옵션은 새 파티션 체계로 새 서비스 인스턴스를 만드는 것입니다. 하지만 사용 중인 모든 클라이언트가 언제 어떻게 다르게 이름 지정된 서비스를 사용하는지 알아야 하므로 복잡성이 추가됩니다.

### <a name="example-scenario-embedded-dates"></a>예제 시나리오: 포함된 날짜
한 가지 가능한 시나리오는 서비스 이름의 일부로 날짜 정보를 사용하는 것입니다. 예를 들어 2013년에 합류한 모든 고객에 대해 특정한 이름의 서비스 인스턴스를 사용하고 2014년에 합류한 고객에 대해서는 다른 이름의 서비스 인스턴스를 사용할 수 있습니다. 이 이름 지정 체계로 날짜에 따라 이름이 증가하는 프로그래밍 방식을 사용할 수 있습니다(2014 접근 방식으로 2014년에 대한 서비스 인스턴스를 필요에 따라 만들 수 있습니다).

그러나 이 접근 방식은 서비스 패브릭 지식 범위 밖의 특정 이름 지정 정보 응용 프로그램을 사용하는 클라이언트에 기반합니다.

* *이름 지정 규칙 사용*: 2013년에 사용할 응용 프로그램이 있는 경우 fabric:/app/service2013라는 이름의 서비스를 만듭니다. 2013년 2/4분기를 향해 fabric:/app/service2014라는 이름의 다른 서비스를 만듭니다. 이 두 서비스는 동일한 서비스 유형입니다. 이 접근 방법에서는 클라이언트가 연도를 기준으로 적절한 서비스 이름을 생성하는 논리가 있어야 합니다.
* *조회 서비스 사용*: 다른 패턴은 원하는 키에 대한 서비스의 이름을 제공할 수 있는 보조 “조회 서비스”를 사용하는 것입니다. 그러면 조회 서비스에 의해새 서비스 인스턴스가 만들어집니다. 조회 서비스 자체는 생성한 서비스 이름에 대한 데이터 외에는 어떤 응용 프로그램 데이터도 유지하지 않습니다. 따라서 같은 해에 기반한 위 예제의 경우, 클라이언트는 먼저 조회 서비스에 연결하여 주어진 해에 대한 서비스 처리 데이터의 이름을 확인한 후 그 서비스 이름을 사용하여 실제 작업을 수행합니다. 첫 번째 조회 결과는 캐시될 수 있습니다.

## <a name="next-steps"></a>다음 단계
서비스 패브릭 개념에 대한 자세한 내용은 다음을 참조하세요.

* [서비스 패브릭 서비스의 가용성](service-fabric-availability-services.md)
* [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)
* [상태 정의 및 관리](service-fabric-concepts-state.md)




<!--HONumber=Nov16_HO3-->


