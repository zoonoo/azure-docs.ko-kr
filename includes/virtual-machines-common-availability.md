---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4860dcac666f790fed199536338e50a967113c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76748915"
---
이 문서에서는 Azure 가상 시스템(VM)의 가용성 기능에 대한 개요를 제공합니다.

## <a name="high-availability"></a>고가용성

워크로드는 일반적으로 높은 처리량, 성능을 얻고 업데이트 또는 기타 이벤트로 인해 VM이 영향을 받을 경우 중복성을 만들기 위해 여러 가상 시스템에 분산됩니다. 

Azure에서 고가용성을 달성하기 위해 제공하는 옵션은 거의 없습니다. 먼저 기본 구문에 대해 이야기해 보겠습니다. 

### <a name="availability-zones"></a>가용성 영역

[가용성 영역은](../articles/availability-zones/az-overview.md) VM에서 응용 프로그램 및 데이터의 가용성을 유지하는 데 필요한 제어 수준을 확장합니다. 가용성 영역은 Azure 지역 내에서 물리적으로 분리된 영역입니다. 지원되는 Azure 지역당 3개의 가용성 영역이 있습니다. 

각 가용성 영역에는 고유한 소스, 네트워크 및 냉각 장치가 있습니다. 영역에 복제된 VM을 사용하는 솔루션을 설계하여 데이터 센터 손실로부터 앱과 데이터를 보호할 수 있습니다. 하나의 영역이 손상되면 다른 영역에서 복제된 앱 및 데이터를 즉시 사용할 수 있습니다. 

![가용성 영역](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

가용성 영역에서 [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) 또는 [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM을 배포하는 방법에 대해 자세히 알아봅니다.


### <a name="fault-domains"></a>장애 도메인

장애 도메인은 온-프레미스 데이터 센터 내의 랙과 비슷하게 공통 전원 및 네트워크 스위치를 공유하는 기본 하드웨어의 논리적 그룹입니다. 

### <a name="update-domains"></a>업데이트 도메인

업데이트 도메인은 동시에 유지 관리를 진행하거나 다시 부팅될 수 있는 기본 하드웨어의 논리적 그룹입니다. 

이 방법을 통해 Azure 플랫폼이 정기적으로 유지 관리를 거치는 동안 애플리케이션에 있는 하나 이상의 인스턴스가 항상 실행됩니다. 재부팅되는 업데이트 도메인의 순서는 유지 관리 중에 순차적으로 진행되지 않을 수 있지만 한 번에 하나의 업데이트 도메인만 재부팅됩니다.


## <a name="virtual-machines-scale-sets"></a>Virtual Machines 크기 집합 

Azure 가상 시스템 크기 집합을 사용하면 부하 분산 VM 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다. 확장 집합은 응용 프로그램에 고가용성을 제공하고 많은 VM을 중앙에서 관리, 구성 및 업데이트할 수 있도록 합니다. 가용성이 높은 응용 프로그램을 제공하고 [99.95% Azure SLA를](https://azure.microsoft.com/support/legal/sla/virtual-machines/)충족하기 위해 확장 집합 내에서 두 개 이상의 VM을 만드는 것이 좋습니다. 배율 집합 자체에 대한 비용은 없으며 만든 각 VM 인스턴스에 대해서만 비용을 지불합니다. 단일 VM이 [Azure 프리미엄 SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)를 사용하는 경우, 계획되지 않은 유지 관리 이벤트에 대해 Azure SLA가 적용됩니다. 규모 집합의 가상 시스템을 여러 업데이트 도메인 및 장애 도메인에 배포하여 데이터 센터 중단 및 계획되거나 계획되지 않은 유지 관리 이벤트로 인한 가동 중단에 대한 가용성과 복원력을 극대화할 수 있습니다. 규모 집합의 가상 컴퓨터를 단일 가용성 영역 또는 지역적으로 배포할 수도 있습니다. 가용성 영역 배포 옵션은 오케스트레이션 모드에 따라 다를 수 있습니다.

### <a name="preview-orchestration-mode-preview"></a>미리 보기: 오케스트레이션 모드 미리 보기
가상 컴퓨터 배율 집합을 사용하면 오케스트레이션 모드를 지정할 수 있습니다.  이제 가상 시스템 스케일 세트 오케스트레이션 모드(미리 보기)를 사용하면 축척 집합이 축척 집합 구성 모델 외부에서 명시적으로 생성된 가상 컴퓨터를 오케스트레이션할지 또는 암시적으로 생성된 가상 시스템 인스턴스를 오케스트레이션할지 여부를 선택할 수 있습니다. 구성 모델에 따라 다다. VM 오케스트레이션 모델을 사용하면 명시적으로 정의된 가상 컴퓨터를 리전 또는 가용성 영역에서 함께 그룹화할 수 있는 오케스트레이션 모드를 선택합니다. 가용성 영역에 배포된 가상 시스템은 VM에 구역 격리를 제공하며 가용성 영역 경계에 바인딩되며 해당 지역의 다른 가용성 영역에서 발생할 수 있는 오류의 대상이 되지 않습니다. 

|   | "오케스트레이션 모드": "VM" (가상 머신)| "오케스트레이션 모드": "ScaleSetVM" (가상 머신스케일세트VM) |
|----|----|----|
| VM 구성 모델| 없음 가상머신프로파일은 축척 집합 모델에서 정의되지 않습니다. | 필수 사항입니다. 가상머신프로파일은 축척 집합 모델에 채워집니다. |
| 배율 조정 세트에 새 VM 추가| VM을 만들 때 확장 집합에 VM이 명시적으로 추가됩니다. | VM은 VM 구성 모델, 인스턴스 수 및 자동 크기 조정 규칙을 기반으로 축척 집합에 암시적으로 생성되고 추가됩니다. |
| 가용성 영역| 하나의 가용성 영역에서 지역 배포 또는 VM 지원| 지역 배포 또는 여러 가용성 영역을 지원합니다. 영역 균형 전략을 정의할 수 있습니다. |
| 장애 도메인| 오류 도메인 수를 정의할 수 있습니다. 지역 지원 기준 2개 또는 3개, 가용성 영역에 대해 5개 입니다. 할당된 VM 오류 도메인은 할당 할당 및 다시 시작을 포함하여 VM 수명 주기와 함께 유지됩니다. | 비구역 배포에 대해 1, 2 또는 3개의 장애 도메인을 정의하고 가용성 영역 배포에 대해 5를 정의할 수 있습니다. 할당된 VM 오류 도메인은 VM 수명 주기와 함께 유지되지 않으며, 가상 시스템은 할당 시 장애 도메인이 할당됩니다. |
| 업데이트 도메인| 해당 없음. 업데이트 도메인이 장애 도메인에 자동으로 매핑됩니다.| 해당 없음. 업데이트 도메인이 장애 도메인에 자동으로 매핑됩니다. |

**장애 도메인 및 업데이트 도메인**

가상 시스템 크기 집합은 장애 도메인을 정렬하고 도메인을 업데이트하여 고가용성을 위한 설계를 단순화합니다. 스케일 집합에 대한 오류 도메인 개수만 정의하면 됩니다. 스케일 집합에 사용할 수 있는 장애 도메인의 수는 지역에 따라 다를 수 있습니다. [Azure에서 가상 시스템의 가용성 관리를](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)참조하십시오.


## <a name="availability-sets"></a>가용성 집합
가용성 세트은 중복성과 가용성을 제공하기 위해 Azure에서 애플리케이션이 빌드되는 방식을 이해할 수 있도록 하는 데이터 센터에 있는 VM의 논리적 그룹입니다. 고가용성 애플리케이션을 제공하고 [99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 충족할 수 있도록 가용성 집합 내에 둘 이상의 VM을 만드는 것이 좋습니다. 가용성 집합 자체에 대한 비용은 없으므로 만드는 각 VM 인스턴스에 대해서만 요금을 지불합니다. 단일 VM이 [Azure 프리미엄 SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)를 사용하는 경우, 계획되지 않은 유지 관리 이벤트에 대해 Azure SLA가 적용됩니다.

가용성 집합에서 VM은 이러한 장애 도메인에 자동으로 분산됩니다. 이 방법은 잠재적인 물리적 하드웨어 오류, 네트워크 중단 또는 전원 중단의 영향을 제한합니다.

[Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md)를 사용하는 VM의 경우, 관리 가용성 집합을 사용할 때 VM은 관리 디스크 장애 도메인에 맞춰집니다. 이러한 정렬은 VM에 연결된 모든 관리 디스크가 동일한 관리 디스크 장애 도메인 내에 있도록 합니다. 

관리 디스크의 VM만 관리 가용성 집합에서 만들어질 수 있습니다. 관리 디스크 장애 도메인의 수는 지역에 따라 다릅니다. 즉, 지역당 2개 또는 3개의 관리 디스크 장애 도메인이 있을 수 있습니다. 이러한 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) 또는 [Windows VM](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set)용 관리 디스크 장애 도메인에 대한 자세한 내용을 참조할 수 있습니다.

![관리 가용성 집합](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


가용성 집합 내의 VM도 업데이트 도메인 간에 자동으로 배포됩니다. 

![가용성 집합](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>다음 단계
이제 이러한 가용성 및 중복 기능을 사용하여 Azure 환경을 빌드하기 시작할 수 있습니다. 모범 사례 정보는 [Azure 가용성 모범 사례](/azure/architecture/checklist/resiliency-per-service)를 참조하세요.

