---
title: Azure의 Vm에 대 한 격리
description: Azure에서 VM 격리 작동에 대해 알아봅니다.
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/18/2019
ms.author: sttsinar
ms.openlocfilehash: d6a77cac049ef32949eeba4cbd6fe9217100a360
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666843"
---
# <a name="virtual-machine-isolation-in-azure"></a>Azure의 가상 머신 격리

Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다. 격리 된 크기는 라이브 및 특정 하드웨어 생성에 대해 작동 하며 하드웨어 생성이 사용 중지 되 면 사용 되지 않습니다.

격리 된 가상 머신 크기는 다른 고객의 워크 로드와의 높은 수준의 격리를 필요로 하는 작업에 가장 적합 하며,이는 회의 규정 준수 및 규제 요구 사항을 포함 합니다.  격리 크기를 사용하면 가상 머신이 특정 서버 인스턴스에서 하나만 실행되도록 보장합니다. 


또한 격리 된 크기의 Vm이 큰 경우 고객은 [중첩 된 가상 컴퓨터에 대 한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용 하 여 이러한 vm의 리소스를 세분 하도록 선택할 수 있습니다.

현재 격리 가상 머신 제품에는 다음이 포함됩니다.
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_F72s_v2

> [!NOTE]
> 격리 된 VM 크기는 하드웨어의 수명이 제한적입니다. 자세한 내용은 아래를 참조 하세요.

## <a name="deprecation-of-isolated-vm-sizes"></a>격리 된 VM 크기의 사용 중단
격리 된 VM 크기는 하드웨어를 사용 하는 크기 이므로 Azure는 크기의 공식 사용 중단 전에 미리 알림을 12 개월 전에 제공 합니다.  또한 Azure는 고객이 워크 로드를 이동 하는 것으로 간주할 수 있는 다음 하드웨어 버전에 업데이트 된 격리 된 크기를 제공 합니다.

| 크기 | 격리 사용 중지 날짜 | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 2020년 5월 15일 |
| Standard_D15_v2<sup>1</sup>  | 2020년 5월 15일 |

<sup>1</sup>  Standard_DS15_v2 및 Standard_D15_v2 격리 사용 중지 프로그램에 대 한 자세한 내용은 faq를 참조 하세요.


## <a name="faq"></a>FAQ
### <a name="q-is-the-size-going-to-get-retired-or-only-isolation-feature-is"></a>Q: 크기는 사용 중지 되거나 "격리" 기능만 사용 되나요?
**A**: 가상 컴퓨터 크기에 "i" 첨자가 없으면 "격리" 기능만 사용이 중지 됩니다. 격리가 필요 하지 않은 경우에는 수행할 작업이 없으며 VM은 예상 대로 계속 작동 합니다. Standard_DS15_v2, Standard_D15_v2, Standard_M128ms 등의 예가 포함 됩니다. 가상 컴퓨터 크기에 "i" 첨자가 포함 되어 있으면 크기는 사용 중지 됩니다.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q: vm이 격리 되지 않은 하드웨어에 있는 경우 가동 중지 시간이 있나요?
**A**: 격리가 필요 하지 않은 경우에는 작업이 필요 하지 않으며 가동 중지 시간이 발생 하지 않습니다.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>Q: 격리 되지 않은 가상 머신으로 전환 하는 데 비용 델타가 있나요?
**A**: 아니요

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q: 다른 격리 된 크기는 언제 사용 중지 될 예정 인가요?
**A**: 격리 된 크기의 공식 사용 중단 전에 미리 알림을 12 개월 후에 제공 합니다.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q: 실버 또는 골드 내구성 계층에 의존 하는 Azure Service Fabric 고객입니다. 이 변경 내용이 영향을 미칩니까?
**A**: 아니요. 이 변경 후에도 Service Fabric의 [내구성 계층](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) 에서 제공 하는 보장은 계속 작동 합니다. 다른 이유로 인해 물리적 하드웨어 격리가 필요한 경우에도 위에서 설명한 작업 중 하나를 수행 해야 할 수 있습니다. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>Q: D15_v2 또는 DS15_v2 격리 사용 중지에 대 한 마일스 톤은 무엇 인가요? 
**A**: 
 
| Date | 작업 |
|---|---| 
| 2019년 11월 18일 | D/DS15i_v2의 가용성 (PAYG, 1 년 RI) | 
| 5 월 14 일, 2020 | D/DS15i_v2 1 년 RI의 최근 구매 날짜 | 
| 2020년 5월 15일 | D/DS15_v2 격리 보장 제거 | 
| 5 월 15 일, 2021 | D/DS15i_v2 사용 중지 (2019 년 11 월 18 일 이전에 3 년 DS15_v2 RI를 구매한 사용자를 제외한 모든 고객)| 
| 2022 년 11 월 17 일 | 3 년 RIs가 완료 될 때 D/DS15i_v2 사용 중지 (2019 년 11 월 18 일 이전에 3 년 RI의 D/DS15_v2를 구매한 고객의 경우) |

## <a name="next-steps"></a>다음 단계

고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다.
