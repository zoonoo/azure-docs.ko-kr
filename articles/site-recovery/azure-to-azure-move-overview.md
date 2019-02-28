---
title: Azure Site Recovery 서비스를 사용하여 다른 Azure 지역으로 Azure IaaS VM 이동 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS VM을 이동합니다.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: a73eac1dea731bbf1ffb903ddf2438e791fec9d5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726452"
---
# <a name="move-azure-vms-to-another-region"></a>다른 지역으로 Azure VM 이동

Azure는 고객 기반을 광범위하게 확장하고 있으며 수요가 증가하고 있는 새 Azure 지역에 대한 지원을 추가하고 있습니다. 또한 서비스 전반에 걸쳐 매월 최신 기능이 추가됩니다. 이러한 이유로 가용성 향상을 위해 VM을 다른 Azure 지역 또는 가용성 영역으로 이동하려는 경우가 있습니다.

이 문서에서는 VM을 이동하려는 다양한 시나리오를 설명하고, 고가용성을 달성하려면 대상에서 아키텍처를 어떻게 구성해야 하는지 안내합니다. 
> [!div class="checklist"]
> * [Azure VM을 이동하려는 이유](#why-would-you-move-azure-vms)
> * [Azure VM을 이동하는 방법](#how-to-move-azure-vms)
> * [일반적인 아키텍처](#typical-architectures-for-a-multi-tier-deployment)
> * [VM을 있는 그대로 대상 Azure 지역으로 이동](#move-azure-vms-to-another-region)
> * [VM을 이동하여 가용성 향상](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Azure VM을 이동하려는 이유

고객은 다음과 같은 이유로 VM을 이동합니다.

- 이미 한 Azure 지역에 배포했는데 애플리케이션 또는 서비스의 최종 사용자와 더 가까운 위치에 새 Azure 지역이 추가된 경우 **VM을 있는 그대로 새 Azure 지역으로 이동**하여 대기 시간을 줄이고 싶을 것입니다. 구독을 통합하려는 경우 또는 이동해야 하는 거버넌스/조직 규칙이 있는 경우에도 동일한 방식을 사용합니다. 
- VM이 단일 인스턴스 VM으로 또는 가용성 집합의 일부로 배포되었고 가용성 SLA를 높이고 싶은 경우 **VM을 가용성 영역으로 이동**하면 됩니다. 

## <a name="how-to-move-azure-vms"></a>Azure VM을 이동하는 방법
VM을 이동하려면 다음 단계를 수행해야 합니다.

1. 필수 조건 확인 
2. 원본 VM 준비 
3. 대상 Azure 지역 준비 
4. 대상 Azure 지역으로 데이터 복사 - Azure Site Recovery 복제 기술을 사용하여 원본 VM에서 대상 Azure 지역으로 데이터를 복사합니다.
5. 구성 테스트: 복제가 완료되면 비 프로덕션 네트워크로 테스트 장애 조치(failover)를 수행하여 구성을 테스트합니다.
6. 이동 수행 
7. 원본 Azure 지역의 리소스 삭제 


> [!IMPORTANT]
> 현재 Azure Site Recovery는 한 Azure 지역에서 다른 Azure 지역으로 VM 이동을 지원하지만, 같은 Azure 지역 내 이동은 지원하지 않습니다. 

> [!NOTE]
> 이러한 단계에 대한 자세한 지침은 [여기](#next-steps)에 언급된 각 시나리오의 설명서에 제공됩니다.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>다중 계층 배포를 위한 일반적인 아키텍처
아래 섹션에서는 고객이 Azure에서 다중 계층 애플리케이션에 가장 많이 선택하는 배포 아키텍처를 살펴봅니다. 공용 IP를 사용하는 3계층 애플리케이션을 예로 들어 설명하겠습니다. 계층마다(웹, 애플리케이션 및 데이터베이스) VM 2대가 있으며 Load Balancer를 통해 다른 계층과 연결됩니다. 데이터베이스 계층에서는 HA(고가용성)를 위해 VM 간에 SQL Always ON 복제가 사용됩니다.

1.  **다양한 계층에 배포되는 단일 인스턴스 VM** - 계층의 각 VM은 단일 인스턴스 VM으로 구성되고, 부하 분산 장치를 통해 다른 계층과 연결됩니다. 이 구성은 고객이 채택하는 가장 단순한 구성입니다.

       ![단일 VM](media/move-vm-overview/regular-deployment.PNG)

2. **가용성 집합에 배포되는 각 계층의 VM** - 계층의 각 VM은 가용성 집합에 구성됩니다. [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)을 사용하면 Azure에 배포한 VM이 클러스터의 격리된 여러 하드웨어 노드에 분산되도록 할 수 있습니다. 이렇게 하면 Azure 내의 하드웨어 또는 소프트웨어 오류가 발생할 때 VM의 하위 집합에만 영향을 주며 전체 솔루션을 사용 가능한 운영 상태로 유지할 수 있습니다. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **가용성 집합에 배포되는 각 계층의 VM** - 계층의 각 VM은 [가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)에 구성됩니다. Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 예를 들어 Azure 지역의 3개 영역에 VM을 3개 이상 만들면 장애 도메인 3개와 업데이트 도메인 3개에 VM이 효과적으로 분산됩니다. Azure 플랫폼은 업데이트 도메인에 분산된 VM을 인식하여 다른 영역에 있는 VM이 동시에 업데이트되지 않게 합니다.

      ![영역-배포](media/move-vm-overview/zone.PNG)



## <a name="move-vms-as-is-to-a-target-region"></a>VM을 있는 그대로 대상 Azure 지역으로 이동

위에서 언급한 [아키텍처](#typical-architectures-for-a-multi-tier-deployment)에 따라, VM을 있는 그대로 대상 Azure 지역으로 이동했을 때 배포의 모양이 결정됩니다.


1. **다양한 계층에 단일 인스턴스 VM으로 배포** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **가용성 집합에 배포되는 각 계층의 VM**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **가용성 영역에 배포되는 각 계층의 VM**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>VM을 이동하여 가용성 향상

1. **다양한 계층에 단일 인스턴스 VM으로 배포** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **가용성 집합에 배포되는 각 계층의 VM** - Azure Site Recovery를 사용하여 VM에 복제를 사용하도록 선택할 때 가용성 집합의 VM을 별도의 가용성 영역에 배치하도록 구성할 수 있습니다. 이동 작업을 완료하면 가용성 SLA는 99.9%가 됩니다.

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>다음 단계

이 문서에서는 VM 이동에 대한 일반 지침을 알아보았습니다. 단계별 실행 방법은 다음 문서를 참조하세요.


> [!div class="nextstepaction"]
> * [다른 지역으로 Azure VM 이동](azure-to-azure-tutorial-migrate.md)

> * [가용성 영역으로 Azure VM 이동](move-azure-VMs-AVset-Azone.md)

