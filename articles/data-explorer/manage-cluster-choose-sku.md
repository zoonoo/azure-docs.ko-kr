---
title: Azure 데이터 탐색기 클러스터에 올바른 VM SKU를 선택 합니다.
description: 이 문서에서는 Azure 데이터 탐색기 클러스터에 대해 최적의 SKU 크기를 선택 하는 방법을 설명 합니다.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 0239111ca56dfe431a00eee83c79eedccc66c927
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226142"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 올바른 VM SKU를 선택 합니다. 

Azure 데이터 탐색기에는 새 클러스터를 만들거나 변경 작업을 위해 클러스터를 최적화할 때 선택할 수 있는 여러 VM Sku가 있습니다. 모든 워크 로드에 가장 적합 한 비용을 허용 하도록 Vm을 신중 하 게 선택 했습니다. 

데이터 관리 클러스터의 크기 및 VM SKU는 Azure 데이터 탐색기 서비스에 의해 완벽 하 게 관리 됩니다. 엔진의 VM 크기 및 수집 워크 로드와 같은 요인에 따라 결정 됩니다. 

엔진 클러스터에 대 한 VM SKU는 [클러스터를 확장](manage-cluster-vertical-scaling.md)하 여 언제 든 지 변경할 수 있습니다. 따라서 초기 시나리오에 맞는 최소 SKU 크기로 시작 하는 것이 가장 좋습니다. 클러스터를 확장 하면 새 VM SKU를 사용 하 여 클러스터를 다시 만드는 동안 최대 30 분의 가동 중지 시간이 발생 합니다.

> [!TIP]
> 계산 [RI (예약 된 인스턴스)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) 는 Azure 데이터 탐색기 클러스터에 적용 됩니다.  

이 문서에서는 다양 한 VM SKU 옵션을 설명 하 고 최적의 선택을 가능 하 게 하는 데 도움이 되는 기술 세부 정보를 제공 합니다.

## <a name="select-the-cluster-type"></a>클러스터 유형 선택

Azure 데이터 탐색기는 두 가지 유형의 클러스터를 제공 합니다.

* **Production**: 프로덕션 클러스터는 엔진 및 데이터 관리 클러스터에 대 한 두 개의 노드를 포함 하며 Azure 데이터 탐색기 [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)로 작동 합니다.

* **개발/테스트 (SLA 없음)** : 개발/테스트 클러스터에는 엔진 클러스터용 단일 D11_v2 노드와 데이터 관리 클러스터에 대 한 단일 D1 노드가 있습니다. 이 클러스터 유형은 낮은 인스턴스 수 및 엔진 태그 요금이 없으므로 가장 저렴 한 비용 구성입니다. 중복성이 부족 하기 때문에이 클러스터 구성에 대 한 SLA는 없습니다.

## <a name="sku-types"></a>SKU 유형

Azure 데이터 탐색기 클러스터를 만들 때 계획 된 워크 로드에 대 한 *최적의* VM SKU를 선택 합니다. Azure 데이터 탐색기에는 선택할 수 있는 두 가지 SKU 패밀리가 있습니다.

* **D_V2**: **D** SKU는 계산에 최적화 되 고 두 가지 특색으로 제공 됩니다.
    * VM 자체
    * Premium storage 디스크와 함께 제공 되는 VM

* **LS**: **L** SKU는 저장소에 최적화 되어 있습니다. 비슷한 가격 책정 **D** SKU 보다 훨씬 더 큰 SSD 크기를 가집니다.

다음 표에서는 사용 가능한 SKU 유형 간의 주요 차이점을 보여 줍니다.
 
|**특성** | **D SKU** | **L SKU**
|---|---|---
|**소형 Sku**|최소 크기는 2, 2 코어|최소 크기는 4 개 코어의 ' L4 '입니다.
|**가용성**|모든 지역에서 사용 가능 (DS + PS 버전의 가용성이 제한 됨)|몇 개 지역에서 사용 가능
|**코어 당 GB 당 캐시 비용**|D SKU와 함께 사용, DS + PS 버전의 낮은 수준|*종 량* 제 옵션으로 비용이 저렴 합니다.
|**RI (Reserved Instances) 가격 책정**|높은 할인율 (3 년 약정의 경우 55% 초과)|낮은 할인율 (3 년 약정의 경우 20%)  

## <a name="select-your-cluster-vm"></a>클러스터 VM 선택 

클러스터 VM을 선택 하려면 [수직 크기 조정을 구성](manage-cluster-vertical-scaling.md#configure-vertical-scaling)합니다. 

다른 VM SKU 옵션을 사용 하면 원하는 시나리오에 필요한 성능 및 핫 캐시 요구 사항에 대 한 비용을 최적화할 수 있습니다. 시나리오에서 높은 쿼리 볼륨에 가장 적합 한 성능이 필요한 경우에는 적절 한 SKU가 계산에 최적화 되어야 합니다. 반면, 시나리오에서 비교적 낮은 쿼리 로드를 사용 하 여 대량의 데이터를 쿼리해야 하는 경우 저장소에 최적화 된 SKU는 뛰어난 성능을 제공 하면서도 비용을 절감 합니다.

Small Sku에 대 한 클러스터당 인스턴스 수는 제한 되므로 RAM이 더 큰 Vm을 사용 하는 것이 좋습니다. Ram 크기는를 사용 `joins`하는 쿼리와 같이 ram 리소스에 더 많은 수요를 주는 일부 쿼리 유형에 필요 합니다. 따라서 크기 조정 옵션을 고려할 때 더 많은 인스턴스를 추가 하 여 스케일 아웃 보다 큰 SKU로 확장 하는 것이 좋습니다.

## <a name="vm-options"></a>VM 옵션

다음 표에서는 Azure 데이터 탐색기 클러스터 Vm에 대 한 기술 사양을 제공 합니다.

|**이름**| **범주** | **SSD 크기** | **코어 수** | **RAM** | **Premium storage 디스크 (1TB)**| **클러스터당 최소 인스턴스 수** | **클러스터당 최대 인스턴스 수**
|---|---|---|---|---|---|---|---
|D11_v2| 계산에 최적화 됨 | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (개발/테스트 SKU가 1 인 경우 제외)
|D12_v2| 계산에 최적화 됨 | 150GB   | 4 | 28GB | 0 | 2 | 16
|D13_v2| 계산에 최적화 됨 | 307 GB   | 8 | 56GB | 0 | 2 | 1,000
|D14_v2| 계산에 최적화 됨 | 614 GB   | 16| 112GB | 0 | 2 | 1,000
|DS13_v2 + 1TB PS| 저장소 최적화 | 1TB | 8 | 56GB | 1 | 2 | 1,000
|DS13_v2 + 2TB PS| 저장소 최적화 | 2TB | 8 | 56GB | 2 | 2 | 1,000
|DS14_v2 + 3TB PS| 저장소 최적화 | 3 TB | 16 | 112GB | 2 | 2 | 1,000
|DS14_v2 + 4TB PS| 저장소 최적화 | 4 TB | 16 | 112GB | 4 | 2 | 1,000
|L4s_v1| 저장소 최적화 | 650 GB | 4 | 32GB | 0 | 2 | 16
|L8s_v1| 저장소 최적화 | 1.3 TB | 8 | 64GB | 0 | 2 | 1,000
|L16s_1| 저장소 최적화 | 2.6 TB | 16| 128GB | 0 | 2 | 1,000

* Azure 데이터 탐색기 [Listskus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)를 사용 하 여 지역별 업데이트 된 VM SKU 목록을 봅니다. 
* [다른 계산 sku](/azure/virtual-machines/windows/sizes-compute)에 대해 자세히 알아보세요. 

## <a name="next-steps"></a>다음 단계

* 서로 다른 요구 사항에 대 한 VM SKU를 변경 하 여 언제 든 지 엔진 클러스터를 [확장 하거나](manage-cluster-vertical-scaling.md) 축소할 수 있습니다. 

* 엔진 클러스터 크기를 [확장 하 고 확장](manage-cluster-horizontal-scaling.md) 하 여 수요를 변경 하 여 용량을 변경할 수 있습니다.

