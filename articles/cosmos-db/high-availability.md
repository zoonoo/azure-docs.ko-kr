---
title: Azure Cosmos DB의 고가용성
description: 이 문서에서는 Azure Cosmos DB에서 고가용성을 제공하는 방법을 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2afeae937d56a84c39167ad55a57c86f2623e52d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382714"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Azure Cosmos DB의 고가용성

Azure Cosmos DB는 Cosmos 계정과 연결된 모든 Azure 지역에서 데이터를 투명하게 복제합니다. Cosmos DB는 다음 이미지와 같이 데이터에 대해 여러 계층의 중복성을 사용합니다.

![물리적 분할](./media/high-availability/cosmosdb-data-redundancy.png)

- 코스모스 컨테이너 내의 데이터는 [가로로 분할됩니다.](partitioning-overview.md)

- 각 지역 내에서 모든 파티션은 모든 쓰기가 복제되는 복제본 세트로 보호되며, 대부분의 복제본에서 지속적으로 커밋됩니다. 복제본은 10 ~ 20개의 장애 도메인 간에 분산됩니다.

- 모든 지역에 걸쳐 있는 각 파티션이 복제됩니다. 각 지역에서는 Cosmos 컨테이너의 모든 데이터 파티션을 포함하며, 쓰기를 허용하고 읽기를 제공할 수 있습니다.  

Cosmos 계정이 *N* Azure 지역에 분산되어 있는 경우 모든 데이터의 최소 *N* x 4 복사본이 있습니다. Cosmos 계정과 연결된 리전에서 낮은 대기 시간 데이터 액세스 및 쓰기/읽기 처리량을 확장할 뿐만 아니라 더 많은 리전(상위 *N)을*보유하면 가용성이 더욱 향상됩니다.  

## <a name="slas-for-availability"></a>가용성 SLA

전역적으로 분산된 데이터베이스인 Cosmos DB는 처리량, 99번째 백분위수의 대기 시간, 일관성 및 고가용성을 포함하는 포괄적인 SLA를 제공합니다. 다음 표에서는 Cosmos DB가 제공하는 단일 및 다중 지역 계정에 대한 고가용성 보장 내용을 보여줍니다. 고가용성의 경우 항상 Cosmos 계정을 여러 쓰기 영역으로 구성합니다.

|작업 유형  | 단일 지역 |다중 지역(단일 지역 쓰기)|다중 지역(다중 지역 쓰기) |
|---------|---------|---------|-------|
|쓰기    | 99.99    |99.99   |99.999|
|읽기     | 99.99    |99.999  |99.999|

> [!NOTE]
> 실제로 경계가 있는 부실, 세션, 일관된 접두사 및 최종 일관성 모델에 대한 실제 쓰기 가용성은 게시된 SLA보다 훨씬 높습니다. 모든 일관성 수준에 대한 실제 읽기 가용성은 게시된 SLA보다 훨씬 높습니다.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>지역 가동 중단이 발생하는 경우 Cosmos DB를 통한 고가용성

지역 중단은 드물게 나타나며, Azure Cosmos DB는 사용자의 데이터베이스를 항상 사용할 수 있도록 합니다. 다음 세부 정보는 Cosmos 계정 구성에 따라 중단 시 Cosmos DB 동작을 캡처합니다.

- Cosmos DB를 사용하면 쓰기 작업이 클라이언트에서 승인되기 전에 쓰기 작업을 허용하는 지역 내에서 복제본의 쿼럼에 의해 데이터가 지속적으로 커밋됩니다.

- 다중 쓰기 지역으로 구성된 다중 지역 계정은 쓰기 및 읽기 모두에 대해 고가용성을 유지합니다. 지역별 장애 조치(failover)는 즉각적이며, 애플리케이션에서 변경할 필요가 없습니다.

- 단일 지역 계정은 지역 중단으로 인해 가용성이 손실될 수도 있습니다. 항상 고가용성을 보장하기 위해 Cosmos 계정으로 최소 두 개의 리전(가급적이면 두 개 이상의 쓰기 영역)을 설정하는 것이 좋습니다. **at least two regions**

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>단일 쓰기 영역이 있는 다중 지역 계정(쓰기 지역 중단)

- 쓰기 영역이 중단되는 동안 Cosmos 계정은 Azure Cosmos 계정에서 자동 장애 **조치(failover)를 사용하도록** 설정하면 보조 영역을 새 기본 쓰기 영역으로 자동으로 승격합니다. 사용하도록 설정하면 장애 조치(failover)가 지정한 지역 우선 순위의 순서대로 다른 지역으로 발생합니다.
- 이전에 영향을 받은 지역이 다시 온라인 상태가 되면 지역이 실패했을 때 복제되지 않은 모든 쓰기 데이터가 [충돌 피드를](how-to-manage-conflicts.md#read-from-conflict-feed)통해 사용할 수 있습니다. 응용 프로그램은 충돌 피드를 읽고, 응용 프로그램 별 논리에 따라 충돌을 해결하고, 업데이트된 데이터를 Azure Cosmos 컨테이너에 적절하게 다시 쓸 수 있습니다.
- 이전에 영향을 받는 쓰기 지역이 복구되고 나면, 자동으로 읽기 지역으로 사용할 수 있게 됩니다. 복구된 영역을 쓰기 영역으로 다시 전환할 수 있습니다. [PowerShell, Azure CLI 또는 Azure 포털을](how-to-manage-database-account.md#manual-failover)사용하여 지역을 전환할 수 있습니다. 쓰기 영역을 전환하기 전, 도중 또는 후에 **데이터 또는 가용성 손실이 없으며** 응용 프로그램의 가용성이 계속 향상됩니다.

> [!IMPORTANT]
> **자동 장애 조치(failover)를 사용하도록**프로덕션 워크로드에 사용되는 Azure Cosmos 계정을 구성하는 것이 좋습니다. 수동 장애 조치에는 장애 조치 중에 데이터 손실이 없는지 확인하기 위해 일관성 검사를 완료하기 위해 보조 쓰기 영역과 기본 쓰기 영역 간의 연결이 필요합니다. 기본 지역을 사용할 수 없는 경우 이 일관성 검사를 완료할 수 없으며 수동 장애 조치(failover)가 성공하지 않아 쓰기 가용성이 손실됩니다.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>단일 쓰기 영역이 있는 다중 지역 계정(읽기 지역 중단)

- 읽기 영역이 중단되는 동안 Cosmos 계정은 3개 이상의 읽기 영역과 일관성 수준이나 강력한 일관성을 사용하여 읽기 및 쓰기에 사용할 수 있습니다.
- 영향을 받은 영역은 자동으로 연결이 끊어지고 오프라인으로 표시됩니다. [Azure Cosmos DB SDK는](sql-api-sdk-dotnet.md) 읽기 호출을 기본 설정 지역 목록에서 사용 가능한 다음 리전으로 리디렉션합니다.
- 기본 지역 목록의 어느 지역도 사용할 수 없는 경우 호출은 현재 쓰기 지역으로 자동으로 대체됩니다.
- 읽기 지역 중단을 처리하기 위해 애플리케이션 코드를 변경할 필요가 없습니다. 영향을 받는 읽기 영역이 다시 온라인 상태가 되면 현재 쓰기 영역과 자동으로 동기화되고 읽기 요청을 다시 제공할 수 있습니다.
- 후속 읽기는 애플리케이션 코드를 변경하지 않고도 복구된 지역으로 리디렉션됩니다. 장애 조치와 이전에 실패한 리전의 재가입 동안 읽기 일관성 보장은 Cosmos DB에서 계속 적용됩니다.

> [!IMPORTANT]
> 읽기 영역이 두 개 이하인 강력한 일관성을 사용하는 Azure Cosmos 계정은 읽기 영역 중단 중에 쓰기 가용성을 잃지만 나머지 영역에 대한 읽기 가용성을 유지합니다.

- Azure 지역을 영구적으로 복구할 수 없는 드문 경우라도 다중 지역 Cosmos 계정이 *강력한* 일관성으로 구성된 경우 데이터 손실이 발생하지 않습니다. 영구적으로 복구할 수 없는 쓰기 영역인 경계가 없는 부실 일관성으로 구성된 다중 영역 Cosmos 계정의 경우 잠재적인 데이터 손실 창은 K=100,000 업데이트 및 T=5분의 부실*창(K* 또는 *T)으로*제한됩니다. 세션, 일관된 접두사 및 최종 일관성 수준의 경우 잠재적인 데이터 손실 기간은 최대 15분으로 제한됩니다. Azure Cosmos DB의 RTO 및 RPO 대상에 대한 자세한 내용은 [일관성 수준 및 데이터 내구성을](consistency-levels-tradeoffs.md#rto) 참조하세요.

## <a name="availability-zone-support"></a>가용성 영역 지원

이제 영역 간 복원력 외에도 Azure Cosmos 데이터베이스와 연결할 영역을 선택할 때 **영역 중복성을** 활성화할 수 있습니다.

가용성 영역 지원을 통해 Azure Cosmos DB는 영역 오류 발생 시 고가용성 및 복원력을 제공하기 위해 지정된 지역 내의 여러 영역에 복제본을 배치할 수 있도록 합니다. 이 구성에는 대기 시간 및 기타 SLA에 대한 변경 사항이 없습니다. 단일 영역 오류가 발생하는 경우 영역 중복은 RPO=0을 사용하여 전체 데이터 내구성을 제공하고 RTO=0을 사용할 수 있습니다.

영역 중복은 [다중 마스터 복제](how-to-multi-master.md) 기능에 대한 *추가 기능입니다.* 영역 중복성만으로는 지역 복원력을 달성할 수 없습니다. 예를 들어 지역 간 중단 또는 짧은 대기 시간 액세스의 경우 영역 중복 성 외에 여러 쓰기 영역을 두는 것이 좋습니다.

Azure Cosmos 계정에 대해 다중 지역 쓰기를 구성할 때 추가 비용 없이 영역 중복을 선택할 수 있습니다. 그렇지 않으면 영역 중복 지원에 대한 가격 책정에 대해서는 아래 참고 사항을 참조하십시오. 영역을 제거하고 영역 중복을 사용하도록 설정하여 Azure Cosmos 계정의 기존 영역에서 영역 중복을 활성화할 수 있습니다.

이 기능은 다음 Azure 지역에서 사용할 수 있습니다.

- 영국 남부

- 동남아시아

- 미국 동부

- 미국 동부 2

- 미국 중부

- 서유럽

- 미국 서부 2

> [!NOTE]
> 단일 리전 Azure Cosmos 계정에 대한 가용성 영역을 사용하도록 설정하면 계정에 추가 지역을 추가하는 것과 동일한 요금이 부과됩니다. 가격 책정에 대한 자세한 내용은 [Azure Cosmos DB](optimize-cost-regions.md) 문서의 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 및 다중 지역 비용을 참조하십시오.

다음 표에는 다양한 계정 구성의 고가용성 기능이 요약됩니다.

|KPI  |가용성 영역이 없는 단일 지역(AZ 제외)  |가용성 영역(AZ)이 있는 단일 지역  |가용성 영역(AZ, 2개 지역)이 있는 다중 리전 쓰기 – 가장 권장되는 설정 |
|---------|---------|---------|---------|
|가용성 SLA 작성 | 99.99% | 99.99% | 99.999% |
|가용성 읽기 SLA  | 99.99% | 99.99% | 99.999% |
|Price | 단일 지역 청구 요금 | 단일 지역 가용성 영역 청구 요금 | 다중 지역 청구 율 |
|영역 오류 – 데이터 손실 | 데이터 손실 | 데이터 손실 없음 | 데이터 손실 없음 |
|영역 오류 – 가용성 | 가용성 손실 | 가용성 손실 없음 | 가용성 손실 없음 |
|대기 시간 읽기 | 교차 지역 | 교차 지역 | 낮음 |
|쓰기 대기 시간 | 교차 지역 | 교차 지역 | 낮음 |
|지역 정전 – 데이터 손실 | 데이터 손실 |  데이터 손실 | 데이터 손실 <br/><br/> 다중 마스터 및 두 개 이상의 리전에서 경계가 지정된 부실 일관성을 사용하는 경우 데이터 손실은 계정에 구성된 경계되지 않는 부실로 제한됩니다. <br /><br />여러 리전과의 강력한 일관성을 구성하여 지역 중단 중에 데이터 손실을 방지할 수 있습니다. 이 옵션에는 가용성 및 성능에 영향을 주는 장단점이 함께 제공됩니다. 단일 지역 쓰기에 대해 구성된 계정에서만 구성할 수 있습니다. |
|지역 정전 – 가용성 | 가용성 손실 | 가용성 손실 | 가용성 손실 없음 |
|처리량 | X RU/s 프로비저닝 처리량 | X RU/s 프로비저닝 처리량 | 2X RU/s 프로비저닝 처리량 <br/><br/> 이 구성 모드는 두 개의 영역이 있기 때문에 가용성 영역이 있는 단일 리전과 비교할 때 처리량의 두 배가 필요합니다. |

> [!NOTE]
> 다중 지역 Azure Cosmos 계정에 대한 가용성 영역 지원을 사용하려면 계정에 다중 마스터 쓰기가 활성화되어 있어야 합니다.

새 또는 기존 Azure Cosmos 계정에 리전을 추가할 때 영역 중복을 활성화할 수 있습니다. Azure Cosmos 계정에서 영역 중복을 사용하려면 플래그를 `isZoneRedundant` 특정 `true` 위치에 대해 설정해야 합니다. 위치 속성 내에서 이 플래그를 설정할 수 있습니다. 예를 들어 다음 PowerShell 스니펫을 사용하면 "동남아시아" 지역에 대한 영역 중복이 가능합니다.

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

다음 명령은 "EastUS" 및 "WestUS2" 영역에 대한 영역 중복을 활성화하는 방법을 보여 주며 있습니다.

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Azure Cosmos 계정을 만들 때 Azure 포털을 사용하여 가용성 영역을 활성화할 수 있습니다. 계정을 만들 때 **지리적 중복성,** **다중 영역 쓰기를**사용하도록 설정하고 가용성 영역이 지원되는 지역을 선택해야 합니다.

![Azure 포털을 사용하여 가용성 영역 활성화](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>고가용성 애플리케이션 빌드

- 높은 쓰기 및 읽기 가용성을 보장하려면 여러 쓰기 지역을 사용하여 두 개 이상의 지역을 포괄하도록 Cosmos 계정을 구성합니다. 이 구성은 SLA가 지원하는 읽기 및 쓰기 모두에 대해 가장 높은 가용성, 가장 낮은 대기 시간 및 최상의 확장성을 제공합니다. 자세한 내용은 [다중 쓰기 지역으로 Cosmos 계정을 구성하는 방법](tutorial-global-distribution-sql-api.md)을 참조하세요.

- 단일 쓰기 영역으로 구성된 다중 영역 Cosmos 계정의 경우 [Azure CLI 또는 Azure 포털을 사용하여 자동 장애 조치(failover)를 사용하도록 설정합니다.](how-to-manage-database-account.md#automatic-failover) 자동 장애 조치(failover)를 사용하도록 설정하면, Cosmos DB는 지역 재해가 있을 때마다 자동으로 사용자 계정을 장애 조치(failover)합니다.  

- Azure Cosmos 계정이 고가용성인 경우에도 응용 프로그램이 가용성을 유지하도록 올바르게 설계되지 않았을 수 있습니다. 응용 프로그램의 종단 간 고가용성을 테스트하려면 응용 프로그램 테스트 또는 DR(재해 복구) 드릴의 일부로 계정에 대한 자동 장애 해제를 일시적으로 사용하지 않도록 설정하고 [PowerShell, Azure CLI 또는 Azure 포털을 사용하여 수동 장애 해제를](how-to-manage-database-account.md#manual-failover)호출한 다음 응용 프로그램의 장애 중지를 모니터링합니다. 완료되면 기본 지역으로 다시 장애 조치(failover)하고 계정에 대한 자동 장애 조치(failover)를 복원할 수 있습니다.

- 전역적으로 분산된 데이터베이스 환경 내에서 는 지역 전체의 가동 중단이 있을 때 일관성 수준과 데이터 내구성 사이에 직접적인 관계가 있습니다. 비즈니스 연속성 계획을 개발할 때는 중단 이벤트가 발생한 후 애플리케이션이 완전히 복구되기까지 허용되는 최대 시간을 이해해야 합니다. 애플리케이션을 완전히 복구하는 데 필요한 시간을 RTO(복구 시간 목표)라고 합니다. 또한 중단 이벤트가 발생한 후 복구될 때 애플리케이션에서 손실을 허용할 수 있는 최근 데이터 업데이트의 최대 기간도 이해해야 합니다. 손실될 수 있는 업데이트 기간을 RPO(복구 지점 목표)라고 합니다. Azure Cosmos DB의 RPO 및 RTO를 확인하려면 [일관성 수준 및 데이터 내구성](consistency-levels-tradeoffs.md#rto)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 읽을 수 있습니다.

- [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
- [전역적으로 프로비전된 처리량 크기 조정](scaling-throughput.md)
- [글로벌 배포 - 내부 살펴보기](global-dist-under-the-hood.md)
- [Azure 코스모스 DB의 일관성 수준](consistency-levels.md)
- [여러 쓰기 영역으로 코스모스 계정을 구성하는 방법](how-to-multi-master.md)
