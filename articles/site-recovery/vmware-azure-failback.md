---
title: Azure 사이트 복구를 사용 하 고 Azure에서 VM웨어 VM/물리적 서버 장애 복구
description: Azure로 VMware VM 및 물리적 서버의 재해 복구 중에 Azure로 장애 조치(Failover)한 후 온-프레미스 사이트로 장애 복구(Failback)하는 방법을 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495333"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>VMware VM을 온-프레미스 사이트로 장애 조치

이 문서에서는 Azure [사이트 복구를](site-recovery-overview.md)사용하여 온-프레미스 VM에서 Azure로의 [장애 조치(failover)에](site-recovery-failover.md) 따라 온-프레미스 사이트에 Azure VM을 장애 조치하는 방법을 설명합니다. 온-프레미스에 대한 장애 조치 후 온-프레미스 VM이 Azure에 복제를 시작하도록 복제를 사용하도록 설정합니다.

## <a name="before-you-start"></a>시작하기 전에

1. [VMware 장애 조치에](failover-failback-overview.md#vmwarephysical-reprotectionfailback)대해 자세히 알아봅니다. 
2. [장애 조치 대비](vmware-azure-prepare-failback.md)단계를 검토하고 완료했는지 확인하고 필요한 모든 구성 요소가 배포되었는지 확인합니다. 구성 요소에는 Azure의 프로세스 서버, 온-프레미스 마스터 대상 서버 및 장애 복구를 위한 VPN 사이트 간 연결(또는 ExpressRoute 개인 피어링)이 포함됩니다.
3. 다시 보호 및 장애 복구에 대한 [요구 사항을](vmware-azure-reprotect.md#before-you-begin) 완료했는지, Azure VM의 [재보호를 사용하도록 설정하여](vmware-azure-reprotect.md#enable-reprotection) Azure에서 온-프레미스 사이트로 복제할 수 있도록 해야 합니다. VM은 다시 장애 조치하기 위해 복제된 상태에 있어야 합니다.




## <a name="run-a-failover-to-fail-back"></a>장애 조치 실행으로 장애 조치(failover)가 다시 장애 조치

1. Azure VM이 다시 보호되고 온-프레미스 사이트로 복제되는지 확인합니다. 
    - VM은 장애 복구를 위해 하나 이상의 복구 지점이 필요합니다.
    - 복구 계획을 다시 실패하면 계획의 모든 컴퓨터에 하나 이상의 복구 지점이 있어야 합니다.
2. **복제된 항목>** 볼트에서 VM을 선택합니다. VM > **계획되지 않은 장애 조치**중 에서 마우스 오른쪽 단추를 클릭합니다.
3. **장애 조치(failover) 확인**에서 장애 조치(Failover) 방향이 Azure부터인지 확인합니다.
4. 장애 조치(Failover)에 사용할 복구 지점을 선택합니다.
    - **최신** 복구 지점을 사용하는 것이 좋습니다. 앱 일관된 지점은 최신 시점의 뒤에 있으며 일부 데이터 손실을 일으킵니다.
    - **최신** 은 충돌 일관된 복구 지점입니다.
    - **최신**을 사용하면 VM이 사용 가능한 최신 지점으로 실패합니다. 복구 계획 내에서 다중 VM 일관성에 대한 복제 그룹이 있는 경우 그룹의 각 VM은 독립적인 최신 시점으로 장애 발생합니다.
    - 앱 일관된 복구 지점을 사용하는 경우 각 VM은 사용 가능한 최신 지점으로 다시 실패합니다. 복구 계획에 복제 그룹이 있는 경우 각 그룹은 사용 가능한 공통 복구 지점으로 복구됩니다.
5. 장애 조치(failover)가 시작됩니다. 사이트 복구는 Azure VM을 종료합니다.
6. 장애 조치(failover)가 완료되면 모든 것이 예상대로 작동하는지 확인합니다. Azure VM이 종료되어 있는지 확인합니다. 
7. 모든 것이 확인되면 VM > **커밋을**마우스 오른쪽 버튼으로 클릭하여 장애 조치 프로세스를 완료합니다. 커밋은 실패한 Azure VM을 제거합니다. 

> [!NOTE]
> Windows VM의 경우 사이트 복구는 장애 조치 중에 VMware 도구를 사용하지 않도록 설정합니다. Windows VM을 장애 조치하는 동안 VMware 도구를 다시 사용할 수 있습니다. 




## <a name="reprotect-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 다시 보호

장애 복구를 커밋하면 Azure VM이 삭제됩니다. VM은 온-프레미스 사이트로 돌아왔지만 보호되지 는 않습니다. 다음과 같이 VM을 Azure로 다시 복제하기 시작하려면 다음을 수행합니다.

1. **복제된 항목을**> 볼트에서 실패한 VM을 선택한 다음 **다시 보호**를 선택합니다.
2. Azure로 데이터를 다시 보내는 데 사용되는 프로세스 서버를 지정합니다.
3. **확인**을 선택하여 다시 보호 작업을 시작합니다.

> [!NOTE]
> 온-프레미스 VM이 시작된 후 에이전트가 구성 서버에 다시 등록하는 데 최대 15분이 걸립니다. 이 시간 동안 다시 보호가 실패하고 에이전트가 설치되지 않았다는 오류 메시지가 반환됩니다. 이 경우 몇 분 동안 기다렸다가 다시 보호합니다.

## <a name="next-steps"></a>다음 단계

다시 보호 작업이 완료되면 온-프레미스 VM이 Azure로 복제됩니다. 필요에 따라 [Azure에 대한 또 다른 장애 조치(failover)를 실행할](site-recovery-failover.md) 수 있습니다.

