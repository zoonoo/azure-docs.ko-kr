---
title: Azure 쌍을 이루는 지역을 사용하여 재해 복구를 & 비즈니스 연속성 보장
description: Azure 지역 페어링을 사용하여 응용 프로그램 복원력 보장
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248256"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>BCDR(무중단 업무 방식 및 재해 복구): Azure 쌍을 이루는 지역

## <a name="what-are-paired-regions"></a>쌍을 이루는 지역이란?

Azure 영역은 대기 시간 정의 경계 내에 배포되고 전용 저지연 네트워크를 통해 연결된 데이터 센터 집합으로 구성됩니다.  이렇게 하면 Azure 지역 내의 Azure 서비스가 최상의 성능과 보안을 제공합니다.  

Azure 지리적 으로 정의 하는 하나 이상의 Azure 영역을 포함 하는 세계의 영역입니다. 지역은 일반적으로 데이터 상주 및 규정 준수 경계를 보존하는 두 개 이상의 지역을 포함하는 개별 시장을 정의합니다.  Azure의 글로벌 인프라에 대한 자세한 내용은 [여기를 참조하세요.](https://azure.microsoft.com/global-infrastructure/regions/)

지역 쌍은 동일한 지역 내에서 두 지역으로 구성됩니다. Azure는 지역 쌍 간에 플랫폼 업데이트(계획된 유지 관리)를 직렬화하여 각 쌍의 한 지역만 한 번에 업데이트하도록 합니다. 중단이 여러 지역에 영향을 미치는 경우 각 쌍에 하나 이상의 영역이 복구에 우선 순위가 지정됩니다.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

일부 Azure 서비스는 비즈니스 연속성을 보장하고 데이터 손실을 방지하기 위해 쌍을 이루는 지역을 추가로 활용합니다.  Azure는 데이터 가용성을 보장하기 위해 쌍을 이루는 지역을 활용하는 여러 [저장소 솔루션을](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 제공합니다. 예를 들어 [GRS(Azure Geo-중복 저장소)는](./storage/common/storage-redundancy.md#geo-redundant-storage) 데이터를 보조 지역으로 자동으로 복제하여 기본 지역을 복구할 수 없는 경우에도 데이터의 내구성을 보장합니다. 

모든 Azure 서비스가 자동으로 데이터를 복제하는 것은 아니며 모든 Azure 서비스가 실패한 리전에서 해당 쌍으로 자동으로 대체되지는 않습니다.  이러한 경우 고객이 복구 및 복제를 구성해야 합니다.

## <a name="can-i-select-my-regional-pairs"></a>지역 쌍을 선택할 수 있나요?

아니요. 일부 Azure 서비스는 Azure의 [중복 저장소와](./storage/common/storage-redundancy.md)같은 지역 쌍에 의존합니다. 이러한 서비스는 새 지역 쌍을 만들 수 없습니다.  마찬가지로 Azure는 지역 쌍에 대해 계획된 유지 관리 및 복구 우선 순위를 제어하므로 이러한 서비스를 활용하기 위해 고유한 지역 쌍을 정의할 수 없습니다. 그러나 여러 지역에서 서비스를 빌드하고 Azure 서비스를 활용하여 페어링하여 고유한 재해 복구 솔루션을 만들 수 있습니다. 

예를 들어 [AzCopy와](./storage/common/storage-use-azcopy-v10.md) 같은 Azure 서비스를 사용하여 다른 지역의 저장소 계정에 대한 데이터 백업을 예약할 수 있습니다.  [고객은 Azure DNS 및 Azure 트래픽 관리자를](./networking/disaster-recovery-dns-traffic-manager.md)사용하여 기본 지역의 손실에서 살아남을 수 있는 응용 프로그램에 대한 복원력 있는 아키텍처를 디자인할 수 있습니다.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>지역 쌍 내에서 서비스를 사용할 수 있나요?

아니요. 지정된 Azure 서비스는 지역 쌍에 의존할 수 있지만 비즈니스 요구 사항을 충족하는 모든 지역에서 다른 서비스를 호스팅할 수 있습니다.  Azure GRS 저장소 솔루션은 미국 동부에 위치한 Compute 리소스를 사용하는 동안 캐나다 센트럴의 데이터를 캐나다 동부의 피어와 페어링할 수 있습니다.  

## <a name="must-i-use-azure-regional-pairs"></a>Azure 지역 쌍을 사용해야 합니까?

아니요. 고객은 Azure 의 지역 쌍에 의존하지 않고 Azure 서비스를 활용하여 탄력적인 서비스를 설계할 수 있습니다.  그러나 [격리의](./security/fundamentals/isolation-choices.md) 이점을 누리고 [가용성을](./availability-zones/az-overview.md)개선하기 위해 지역 쌍 간에 비즈니스 연속성 재해 복구(BCDR)를 구성하는 것이 좋습니다. 여러 활성 지역을 지원하는 애플리케이션의 경우 가능하면 한 지역 쌍의 두 지역을 모두 사용하는 것이 좋습니다. 이렇게 하면 응용 프로그램에 대한 최적의 가용성을 보장하고 재해 발생 시 복구 시간을 최소화할 수 있습니다. 가능하면 [최대 복원력과](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) [재해 복구의](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)용이성을 위해 응용 프로그램을 디자인합니다.

## <a name="azure-regional-pairs"></a>Azure 지역 쌍

| Geography | 지역 쌍 A | 지역 쌍 B  |
|:--- |:--- |:--- |
| 아시아 태평양 지역 |동아시아(홍콩) | 동남아시아(싱가포르) |
| 오스트레일리아 |오스트레일리아 동부 |오스트레일리아 남동부 |
| 오스트레일리아 |오스트레일리아 중부 |오스트레일리아 중부 2 |
| 브라질 |브라질 남부 |미국 중남부 |
| Canada |캐나다 중부 |캐나다 동부 |
| 중국 |중국 북부 |중국 동부|
| 중국 |중국 북부 2 |중국 동부 2|
| 유럽 |북유럽(아일랜드) |서유럽(네덜란드) |
| 프랑스 |프랑스 중부|프랑스 남부|
| 독일 |독일 중부 |독일 북동부 |
| 인도 |인도 중부 |인도 남부 |
| 인도 |인도 서부 |인도 남부 |
| 일본 |일본 동부 |일본 서부 |
| 한국 |한국 중부 |한국 남부 |
| 북아메리카 |미국 동부 |미국 서부 |
| 북아메리카 |미국 동부 2 |미국 중부 |
| 북아메리카 |미국 중북부 |미국 중남부 |
| 북아메리카 |미국 서부 2 |미국 중서부 |
| 노르웨이 | 노르웨이 동부 | 노르웨이 웨스트 |
| 남아프리카 | 남아프리카 북부 |남아프리카 공화국 서부 |
| 스위스 | 스위스 북부 |스위스 웨스트 |
| 영국 |영국 서부 |영국 남부 |
| 아랍에미리트 | 아랍에미리트 북부 | 아랍에미리트 중부
| 미국 국방부 |US DoD 동부 |US DoD 중부 |
| 미국 정부 |US Gov 애리조나 |US Gov 텍사스 |
| 미국 정부 |US Gov 아이오와 |US Gov 버지니아 |
| 미국 정부 |US Gov 버지니아 |US Gov 텍사스 |

> [!Important]
> - 서인도는 한 방향으로만 페어링됩니다. 인도 서부의 보조 지역은 인도 남부이지만 인도 남부의 보조 지역은 인도 중부입니다.
> - 브라질 남부는 지역 외부의 지역과 쌍을 이루고 있기 때문에 독특합니다. 브라질 남부의 보조 지역은 미국 중남부입니다. 미국 중남부의 보조 지역은 브라질 남부가 아닙니다.


## <a name="an-example-of-paired-regions"></a>쌍을 이루는 지역의 예
아래 이미지는 재해 복구를 위해 지역 쌍을 사용하는 가상 응용 프로그램을 보여 줍니다. 녹색 숫자는 세 가지 Azure 서비스(Azure 계산, 저장소 및 데이터베이스)의 지역 간 활동과 지역 간에 복제하도록 구성된 방법을 강조 표시합니다. 쌍을 이루는 지역에 걸친 배포의 고유한 이점은 주황색 숫자로 강조 표시되어 있습니다.

![쌍을 이루는 지역의 이점 개요](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

그림 2 – 가상의 Azure 지역 쌍

## <a name="cross-region-activities"></a>지역 간 활동
그림 2 참조

1. **Azure Compute(IaaS)** – 재해 발생 시 다른 지역에서 리소스를 사용할 수 있도록 하려면 추가 계산 리소스를 미리 프로비전해야 합니다. 자세한 내용은 [Azure 복원력 기술 지침](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)을 참조하세요. 

2. **Azure Storage** - 관리 디스크를 사용하는 경우 Azure Backup을 사용하여 [지역 간 백업에](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) 대해 알아보고 Azure 사이트 복구를 사용하여 한 리전에서 다른 리전으로 [VM을 복제합니다.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) 저장소 계정을 사용하는 경우 Azure Storage 계정을 만들 때 기본적으로 GRS(지역 중복 저장소)가 구성됩니다. GRS를 사용하면 주 지역 및 쌍을 이루는 지역에서 각각 세 번씩 데이터가 자동으로 복제됩니다. 자세한 내용은 [Azure Storage 중복 옵션](storage/common/storage-redundancy.md)을 참조하세요.

3. **Azure SQL 데이터베이스** - Azure SQL Database 지리적 복제를 사용하여 전 세계 모든 지역에 트랜잭션의 비동기 복제를 구성할 수 있습니다. 그러나 대부분의 재해 복구 시나리오에 대해 쌍을 이루는 지역에 이러한 리소스를 배포하는 것이 좋습니다. 자세한 내용은 [Azure SQL Database의 지역에서 복제](sql-database/sql-database-geo-replication-overview.md)를 참조하세요.

4. **Azure Resource Manager** - Resource Manager는 본질적으로 지역 간에 구성 요소를 논리적으로 격리합니다. 따라서 하나의 지역에서 발생한 논리적 오류가 다른 지역에 영향을 줄 가능성이 거의 없습니다.

## <a name="benefits-of-paired-regions"></a>쌍을 이루는 지역의 이점

5. **물리적 격리** – 가능하면 Azure는 모든 지역에서 실용적이지 않거나 가능하지는 않지만 지역 쌍에 있는 데이터 센터 간에 최소 300마일의 분리를 선호합니다. 물리적 데이터 센터 격리는 자연 재해, 내전, 정전 또는 물리적 네트워크 중단으로 인해 두 지역 모두가 동시에 영향을 받을 수 있는 가능성을 줄여 줍니다. 격리는 지리적 위치 내의 제약 조건(지리적 크기, 전력/네트워크 인프라 가용성, 규정 등)에 따라 달라집니다.  

6. **플랫폼 제공 복제** - 지리적 중복 저장소와 같은 일부 서비스는 쌍을 이루는 지역에 자동 복제를 제공합니다.

7. **지역 복구 순서** - 광범위한 중단이 발생하면 한 지역의 복구가 모든 쌍에서 우선 순위가 지정됩니다. 쌍을 이루는 지역에 배포된 애플리케이션은 지역 중 하나가 우선 순위에 따라 복구되도록 해줍니다. 쌍을 이루지 않는 지역에 애플리케이션이 배포된 경우에는 복구가 지연될 수 있으며, 최악의 경우 선택한 지역이 마지막에 복구되는 두 지역이 될 수 있습니다.

8. **순차적 업데이트** – 계획된 Azure 시스템 업데이트는 페어링된 지역으로 순차적으로(동시에 출시되지 않음) 가동 중지 시간, 버그의 영향 및 드물게 잘못된 업데이트의 경우 논리적 오류를 최소화합니다.

9. **데이터 거주** – 지역은 세금 및 법 집행 관할권에 대한 데이터 거주 요건을 충족하기 위해 쌍(브라질 남부 제외)과 동일한 지역 내에 있습니다.
