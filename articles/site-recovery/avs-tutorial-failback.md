---
title: Azure Site Recovery를 사용하여 Azure에서 Azure VMware Solution VM 장애 복구(failback)
description: 재해 복구를 수행하는 동안 Azure로 장애 조치(failover) 후 Azure VMware Solution 프라이빗 클라우드로 장애 복구(failback)하는 방법을 알아봅니다.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814233"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>VM을 Azure VMware Solution 프라이빗 클라우드로 장애 복구(failback)

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure VM을 Azure VMware Solution 프라이빗 클라우드로 장애 복구(failback)한 다음, Azure VMware Solution VM을 Azure로 [장애 조치(failover)](avs-tutorial-failover.md)하는 방법을 설명합니다. 장애 복구(failback) 후에는 Azure VMware Solution VM이 Azure로 복제를 시작하도록 복제를 활성화합니다.

## <a name="before-you-start"></a>시작하기 전에

1. [VMware 장애 복구(failback)](failover-failback-overview.md#vmwarephysical-reprotectionfailback)에 대해 알아봅니다. 
2. [장애 복구(failback) 준비](vmware-azure-prepare-failback.md) 단계를 검토 및 완료하고, 필요한 구성 요소를 모두 배포했는지 확인합니다. 구성 요소에는 Azure의 프로세스 서버, 마스터 대상 서버, 장애 복구(failback)를 위한 VPN 사이트 간 연결(또는 ExpressRoute 프라이빗 피어링)이 포함됩니다.
3. Azure에서 Azure VMware Solution 프라이빗 클라우드로 복제하도록 다시 보호 및 장애 복구(failback)를 위한 [요구 사항](avs-tutorial-reprotect.md#before-you-begin)을 완료했으며 Azure VM의 [다시 보호를 사용하도록 설정](avs-tutorial-reprotect.md#enable-reprotection)했는지 확인합니다. 장애 복구(failback)하려면 VM이 복제된 상태여야 합니다.




## <a name="run-a-failover-to-fail-back"></a>장애 복구(failback)하도록 장애 조치(failover) 실행

1. Azure VM이 다시 보호되고 Azure VMware Solution 프라이빗 클라우드에 복제하는지 확인합니다.
    - 장애 복구(failback)하려면 VM에 하나 이상의 복구 지점이 필요합니다.
    - 복구 계획을 장애 복구(failback)하는 경우 계획의 모든 머신에 하나 이상의 복구 지점이 있어야 합니다.
2. 자격 증명 모음 > **복제된 항목**에서 VM을 선택합니다. VM을 마우스 오른쪽 단추로 클릭하고 **계획되지 않은 장애 조치(failover)** 를 선택합니다.
3. **장애 조치(failover) 확인**에서 장애 조치(Failover) 방향이 Azure부터인지 확인합니다.
4. 장애 조치(Failover)에 사용할 복구 지점을 선택합니다.
    - **최신** 복구 지점을 사용하는 것이 좋습니다. 앱 일치 시점이 최신 지점보다 늦으면 데이터 손실이 발생합니다.
    - **최신**은 크래시 일치 복구 지점입니다.
    - **최신**을 사용하면 VM이 사용 가능한 최근 지점으로 장애 조치(failover)됩니다. 복구 계획 내에 다중 VM 일관성에 대한 복제 그룹이 있는 경우 복제 그룹의 각 VM이 독립적인 최신 지점으로 장애 조치(failover)됩니다.
    - 앱 일치 복구 지점을 사용하는 경우 각 VM은 사용 가능한 최신 지점으로 장애 복구(failback)됩니다. 복구 계획에 복제 그룹이 있는 경우 각 그룹은 사용 가능한 일반 복구 지점으로 복구됩니다.
5. 장애 조치(failover)가 시작됩니다. Site Recovery가 Azure VM을 종료합니다.
6. 장애 조치(failover)가 완료된 후 모든 것이 예상대로 작동하는지 확인합니다. Azure VM이 종료되었는지 확인합니다. 
7. 모든 것이 확인되면 VM을 마우스 오른쪽 단추로 클릭하고 **커밋**을 선택하여 장애 조치(failover) 프로세스를 완료합니다. 커밋하면 장애 조치(failover)된 Azure VM이 제거됩니다. 

> [!NOTE]
> Windows VM의 경우 Site Recovery는 장애 조치(failover) 중에 VMware 도구를 사용하지 않도록 설정합니다. Windows VM의 장애 복구(failback) 중에는 VMware 도구가 다시 사용하도록 설정됩니다. 




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>Azure VMware Solution에서 Azure로 다시 보호

장애 복구(failback)를 커밋한 후 Azure VM이 삭제됩니다. VM이 다시 Azure VMware Solution 프라이빗 클라우드로 이동되지만 보호되지는 않습니다. VM을 다시 Azure로 복제하려면 다음을 수행합니다.

1. 자격 증명 모음 > **복제된 항목**에서 장애 복구(failback)된 VM을 선택한 다음, **다시 보호**를 선택합니다.
2. 데이터를 다시 Azure로 보내는 데 사용되는 프로세스 서버를 지정합니다.
3. **확인**을 선택하여 다시 보호 작업을 시작합니다.

> [!NOTE]
> Azure VMware Solution VM이 시작된 후 에이전트가 구성 서버에 다시 등록하는 데 최대 15분이 걸립니다. 이 시간 동안 다시 보호가 실패하고 에이전트가 설치되지 않았다는 오류 메시지가 반환됩니다. 몇 분 동안 기다렸다가 다시 보호하세요.

## <a name="next-steps"></a>다음 단계

다시 보호 작업이 완료되면 Azure VMware Solution VM이 Azure로 복제됩니다. 필요에 따라 Azure로 [또 다른 장애 조치(failover)를 실행](avs-tutorial-failover.md)할 수 있습니다.

