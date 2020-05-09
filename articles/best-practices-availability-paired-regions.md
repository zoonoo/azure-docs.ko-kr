---
title: Azure 쌍을 이루는 지역을 사용 하 여 비즈니스 연속성 & 재해 복구 보장
description: Azure 지역 페어링을 사용 하 여 응용 프로그램 복원 력 보장
author: barbkess
manager: barbkess
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: barbkess
ms.openlocfilehash: be6d2fb590dfefb170b3f23378c507b4d6b2295d
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980051"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>BCDR(무중단 업무 방식 및 재해 복구): Azure 쌍을 이루는 지역

## <a name="what-are-paired-regions"></a>쌍을 이루는 지역이란?

Azure 지역은 대기 시간이 정의 된 경계 내에 배포 되 고 전용 대기 시간이 짧은 네트워크를 통해 연결 된 데이터 센터 집합으로 구성 됩니다.  이렇게 하면 Azure 지역 내의 Azure 서비스가 가능한 최상의 성능 및 보안을 제공 합니다.  

Azure 지리는 하나 이상의 Azure 지역을 포함 하는 전 세계 영역을 정의 합니다. 지리적 위치는 데이터 상주 및 준수 경계를 유지 하는 두 개 이상의 지역을 포함 하는 불연속 시장을 정의 합니다.  Azure의 전역 인프라에 대 한 자세한 내용은 여기를 참조 [하세요](https://azure.microsoft.com/global-infrastructure/regions/) .

지역 쌍은 동일한 지리 내의 두 지역으로 구성 됩니다. Azure는 지역 쌍에서 플랫폼 업데이트 (계획 된 유지 관리)를 직렬화 하 여 각 쌍의 한 지역이 한 번에 업데이트 되도록 합니다. 중단이 여러 지역에 영향을 주는 경우 각 쌍에서 하나 이상의 지역이 복구를 위해 우선 순위가 지정 됩니다.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

일부 Azure 서비스는 비즈니스 연속성을 보장 하 고 데이터 손실을 방지 하기 위해 쌍을 이루는 지역의 추가 이점을 활용 합니다.  Azure는 데이터 가용성을 보장 하기 위해 쌍을 이루는 지역을 활용 하는 여러 [저장소 솔루션](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 을 제공 합니다. 예를 들어 [Azure GRS (지역 중복 저장소](./storage/common/storage-redundancy.md#geo-redundant-storage) )는 데이터를 보조 지역에 자동으로 복제 하 여 주 지역이 복구 되지 않는 경우에도 데이터가 지속 되도록 합니다. 

모든 Azure 서비스에서 자동으로 데이터를 복제 하는 것은 아니며, 모든 Azure 서비스가 실패 한 지역에서 해당 쌍으로 자동으로 복구 되지 않습니다.  이러한 경우 고객은 복구 및 복제를 구성 해야 합니다.

## <a name="can-i-select-my-regional-pairs"></a>내 지역 쌍을 선택할 수 있나요?

아니요. 일부 Azure 서비스는 Azure의 [중복 저장소](./storage/common/storage-redundancy.md)와 같은 지역 쌍을 기반으로 합니다. 이러한 서비스에서는 새 지역 쌍을 만들 수 없습니다.  마찬가지로, Azure는 지역 쌍에 대 한 계획 된 유지 관리 및 복구 우선 순위 지정을 제어 하므로 이러한 서비스를 활용 하기 위해 고유한 지역 쌍을 정의할 수 없습니다. 그러나 원하는 수의 지역에 서비스를 구축 하 고 Azure 서비스를 활용 하 여 사용자 고유의 재해 복구 솔루션을 만들 수 있습니다. 

예를 들어 [AzCopy](./storage/common/storage-use-azcopy-v10.md) 와 같은 Azure 서비스를 사용 하 여 다른 지역의 저장소 계정에 대 한 데이터 백업을 예약할 수 있습니다.  [Azure DNS 및 Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)를 사용 하 여 고객은 주 지역의 손실을 감당할 수 있는 응용 프로그램에 대 한 복원 력 있는 아키텍처를 디자인할 수 있습니다.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>내 지역 쌍 내에서 서비스를 사용 하는 것으로 제한 됩니까?

아니요. 지정 된 Azure 서비스가 지역 쌍을 사용 하는 경우 비즈니스 요구에 맞는 모든 지역에서 다른 서비스를 호스트할 수 있습니다.  Azure GRS 저장소 솔루션은 미국 동부에 있는 계산 리소스를 사용 하는 동안 캐나다 동부의 피어와 캐나다 중부의 데이터를 쌍으로 연결할 수 있습니다.  

## <a name="must-i-use-azure-regional-pairs"></a>Azure 지역 쌍을 사용 해야 하나요?

아니요. 고객은 azure의 지역 쌍에 의존 하지 않고 Azure 서비스를 활용 하 여 복원 력 있는 서비스를 설계할 수 있습니다.  그러나 [격리](./security/fundamentals/isolation-choices.md) 를 활용 하 고 [가용성](./availability-zones/az-overview.md)을 향상 시키기 위해 지역 쌍에서 비즈니스 연속성 재해 복구 (BCDR)를 구성 하는 것이 좋습니다. 여러 활성 지역을 지원하는 애플리케이션의 경우 가능하면 한 지역 쌍의 두 지역을 모두 사용하는 것이 좋습니다. 이렇게 하면 재해 발생 시 응용 프로그램의 가용성을 최적화 하 고 복구 시간을 최소화할 수가 있습니다. 가능 하면 [최대 복원 력](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) 과 [재해 복구](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)의 용이성을 위해 응용 프로그램을 설계 합니다.

## <a name="azure-regional-pairs"></a>Azure 지역 쌍

| Geography | 지역 쌍 A | 지역 쌍 B  |
|:--- |:--- |:--- |
| 아시아 태평양 |동아시아 (홍콩) | 동남 아시아 (싱가포르) |
| 오스트레일리아 |오스트레일리아 동부 |오스트레일리아 남동부 |
| 오스트레일리아 |오스트레일리아 중부 |오스트레일리아 중부 2 |
| 브라질 |브라질 남부 |미국 중남부 |
| Canada |캐나다 중부 |캐나다 동부 |
| 중국 |중국 북부 |중국 동부|
| 중국 |중국 북부 2 |중국 동부 2|
| Europe |유럽 북부 (아일랜드) |유럽 서부 (네덜란드) |
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
| 노르웨이 | 노르웨이 동부 | 노르웨이 서 부 |
| 남아프리카 | 남아프리카 북부 |남아프리카 공화국 서 부 |
| 스위스 | 스위스 북부 |스위스 서부 |
| 영국 |영국 서부 |영국 남부 |
| 아랍에미리트 | 아랍에미리트 북부 | 아랍에미리트 중부
| 미국 국방부 |US DoD 동부 |US DoD 중부 |
| US Government |US Gov 애리조나 |US Gov 텍사스 |
| US Government |US Gov 아이오와 |US Gov 버지니아 |
| US Government |US Gov 버지니아 |US Gov 텍사스 |

> [!Important]
> - 인도 서 부는 한 방향 으로만 쌍을 이룹니다. 인도 서부의 보조 지역은 인도 남부이지만 인도 남부의 보조 지역은 인도 중부입니다.
> - 브라질 남부는 해당 지리 외부에 있는 지역과 쌍을 이루기 때문에 고유 합니다. 브라질 남부의 보조 지역은 미국 중 남부입니다. 남부 중부 미국의 보조 지역은 브라질 남부이 아닙니다.


## <a name="an-example-of-paired-regions"></a>쌍을 이루는 지역의 예
아래 이미지는 재해 복구를 위해 지역 쌍을 사용 하는 가상 응용 프로그램을 보여 줍니다. 녹색 숫자는 세 개의 Azure 서비스 (Azure compute, storage 및 데이터베이스)의 지역 간 활동과 지역 간에 복제 하도록 구성 하는 방법을 강조 표시 합니다. 쌍을 이루는 지역에 걸친 배포의 고유한 이점은 주황색 숫자로 강조 표시되어 있습니다.

![쌍을 이루는 지역의 이점 개요](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

그림 2 – 가상의 Azure 지역 쌍

## <a name="cross-region-activities"></a>지역 간 활동
그림 2 참조

1. **Azure Compute (IaaS)** – 재해 중에 다른 지역에서 리소스를 사용할 수 있도록 사전에 추가 계산 리소스를 프로 비전 해야 합니다. 자세한 내용은 [Azure 복원력 기술 지침](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)을 참조하세요. 

2. **Azure Storage** -managed disks를 사용 하는 경우 Azure Backup를 사용 하 여 [지역 간 백업](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) 에 대해 알아보고 Azure Site Recovery를 사용 하 여 한 지역에서 다른 지역으로 [vm을 복제](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) 합니다. 저장소 계정을 사용 하는 경우 Azure Storage 계정을 만들 때 기본적으로 지역 중복 저장소 (GRS)가 구성 됩니다. GRS를 사용하면 주 지역 및 쌍을 이루는 지역에서 각각 세 번씩 데이터가 자동으로 복제됩니다. 자세한 내용은 [Azure Storage 중복 옵션](storage/common/storage-redundancy.md)을 참조하세요.

3. **Azure SQL Database** – 지역에서 복제를 Azure SQL Database 하 여 전 세계 모든 지역에 대 한 트랜잭션 비동기 복제를 구성할 수 있습니다. 그러나 대부분의 재해 복구 시나리오의 경우 쌍을 이루는 지역에 이러한 리소스를 배포 하는 것이 좋습니다. 자세한 내용은 [Azure SQL Database의 지역에서 복제](sql-database/sql-database-geo-replication-overview.md)를 참조하세요.

4. **Azure Resource Manager** - Resource Manager는 본질적으로 지역 간에 구성 요소를 논리적으로 격리합니다. 따라서 하나의 지역에서 발생한 논리적 오류가 다른 지역에 영향을 줄 가능성이 거의 없습니다.

## <a name="benefits-of-paired-regions"></a>쌍을 이루는 지역의 이점

5. **물리적 격리** – 가능한 경우 Azure는 지역 쌍의 데이터 센터 간에 최소 300 마일의 분리를 선호 하지만이는 실용적이 지 않거나 모든 지역에서 가능 하지 않습니다. 물리적 데이터 센터 격리는 자연 재해, 내전, 정전 또는 물리적 네트워크 중단으로 인해 두 지역 모두가 동시에 영향을 받을 수 있는 가능성을 줄여 줍니다. 격리는 지리적 위치 내의 제약 조건(지리적 크기, 전력/네트워크 인프라 가용성, 규정 등)에 따라 달라집니다.  

6. **플랫폼에서 제공** 하는 복제-지역 중복 저장소와 같은 일부 서비스는 쌍을 이루는 지역에 자동 복제를 제공 합니다.

7. **지역 복구 순서** – 광범위 한 중단 시 한 지역의 복구가 모든 쌍에서 우선 순위가 지정 됩니다. 쌍을 이루는 지역에 배포된 애플리케이션은 지역 중 하나가 우선 순위에 따라 복구되도록 해줍니다. 쌍을 이루지 않는 지역에 애플리케이션이 배포된 경우에는 복구가 지연될 수 있으며, 최악의 경우 선택한 지역이 마지막에 복구되는 두 지역이 될 수 있습니다.

8. **순차적 업데이트** – 계획 된 Azure 시스템 업데이트는 가동 중지 시간을 최소화 하 고, 버그의 영향을 최소화 하 고, 잘못 된 업데이트가 발생 하는 드문 경우에 논리적으로 발생 하기 위해 쌍을 이루는 지역으로 롤아웃 됩니다.

9. **데이터 상주** – 지역은 세금 및 법률 집행 행정의 데이터 상주 요구 사항을 충족 하기 위해 쌍 (브라질 남부 제외)과 동일한 지리 내에 상주 합니다.
