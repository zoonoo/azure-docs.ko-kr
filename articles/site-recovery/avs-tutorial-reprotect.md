---
title: Azure Site Recovery를 사용하여 Azure VM을 Azure VMware Solution 프라이빗 클라우드에 다시 보호
description: Azure Site Recovery를 사용하여 Azure로 장애 조치(failover) 후 Azure VMware Solution VM을 다시 보호하는 방법을 알아봅니다.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814241"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>Azure에서 Azure VMware Solution 프라이빗 클라우드로 다시 보호

Azure VMware Solution VM을 Azure로 [장애 조치(failover)](avs-tutorial-failover.md)한 후, Azure VMware Solution 프라이빗 클라우드로 장애 복구(failback)하는 첫 번째 단계는 장애 조치(failover) 중에 생성된 Azure VM을 다시 보호하는 것입니다. 이 문서에서는 이를 수행하는 방법을 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

1. [이 문서](vmware-azure-prepare-failback.md)의 단계에 따라 Azure와 Azure VMware Solution 프라이빗 클라우드 마스터 대상 서버에서 프로세스 서버를 설정하고 장애 복구(failback)를 위한 사이트 간 VPN 또는 ExpressRoute 개인 피어링을 구성하는 것을 포함하여 다시 보호 및 장애 복구(failback)를 준비합니다.
2. Azure VMware Solution 프라이빗 클라우드 구성 서버가 실행 중이고 Azure에 연결되었는지 확인합니다. 장애 복구(failback) 중에 VM이 구성 서버 데이터베이스에 있어야 합니다. 그렇지 않으면 장애 복구가 실패합니다.
3. Azure VMware Solution 프라이빗 클라우드 마스터 대상 서버에서 스냅샷을 삭제합니다. 스냅샷이 있는 경우 다시 보호가 작동하지 않습니다.  VM의 스냅샷은 다시 보호 작업 중에 자동으로 병합됩니다.
4. 다중 VM 일관성을 위해 복제 그룹으로 수집된 VM을 다시 보호하는 경우에는 모든 VM의 운영 체제(Windows 또는 Linux)가 동일한지 확인하고 배포하는 마스터 대상 서버의 운영 체제가 동일한지 확인합니다. 복제 그룹의 모든 VM이 동일한 마스터 대상 서버를 사용해야 합니다.
5. 장애 복구(failback)에 [필요한 포트](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)를 엽니다.
6. 장애 복구(failback) 전에 vCenter Server가 연결되어 있는지 확인합니다. 연결되지 않은 경우 디스크의 연결을 끊고 가상 머신을 다시 연결하는 작업이 실패합니다.
7. vCenter Server에서 장애 복구(failback)하려는 VM을 관리하는 경우 필요한 권한이 있는지 확인합니다. 읽기 전용 사용자 vCenter 검색을 수행하고 가상 머신을 보호하면 보호에 성공하고 장애 조치가 작동합니다. 그러나 다시 보호 중에는 데이터 저장소를 검색할 수 없기 때문에 장애 조치(failover)가 실패하고, 다시 보호 중에는 나열되지 않습니다. 이 문제를 해결하려면 vCenter 자격 증명을 [적절한 계정/권한](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery)으로 업데이트한 다음, 작업을 다시 시도하면 됩니다. 
8. 템플릿을 사용하여 가상 머신을 만든 경우 각 VM에 디스크에 대한 고유의 UUID가 있는지 확인합니다. Azure VMware Solution VM UUID와 마스터 대상의 UUID가 모두 동일한 템플릿에서 만들어졌기 때문에 두 UUID가 충돌하는 경우 다시 보호가 실패합니다. 다른 템플릿에서 배포하세요.
9. 대체 vCenter Server로 장애 복구(failback)하려는 경우 새 vCenter Server 및 마스터 대상 서버가 검색되는지 확인합니다. 검색되지 않는 경우 일반적으로 데이터 저장소에 액세스할 수 없거나 **다시 보호**에 표시되지 않습니다.
10. 장애 복구(failback)할 수 없는 다음 시나리오를 확인합니다.
    - ESXi 5.5 체험판 버전 또는 vSphere 6 Hypervisor 체험판 버전을 사용 중인 경우. 다른 버전으로 업그레이드하세요.
    - Windows Server 2008 R2 SP1 물리적 서버가 있는 경우.
    - VMware VM을 Hyper-V로 장애 복구(failback)할 수 없습니다.
    - VM이 마이그레이션되었습니다.
    - VM이 다른 리소스 그룹으로 이동되었습니다.
    - 복제본 Azure VM이 삭제되었습니다.
    - 복제본 Azure VM이 보호되지 않습니다.
10. 사용 가능한 [장애 복구(failback) 유형](concepts-types-of-failback.md) - 원래 위치 복구 및 대체 위치 복구를 검토합니다.


## <a name="enable-reprotection"></a>다시 보호 사용

복제를 활성화합니다. 특정 VM 또는 복구 계획을 다시 보호할 수 있습니다.

- 복구 계획을 다시 보호하는 경우 보호된 모든 머신의 값을 입력해야 합니다.
- VM이 다중 VM 일관성을 위한 복제 그룹에 속하는 경우 복구 계획을 사용해야만 VM을 다시 보호할 수 있습니다. 복제 그룹의 모든 VM은 동일한 마스터 대상 서버를 사용해야 합니다.

>[!NOTE]
>다시 보호 중에 Azure에서 이전 원본으로 전송된 데이터의 양은 0바이트부터 모든 보호된 머신의 디스크 합계 사이이며, 계산할 수는 없습니다.

### <a name="before-you-start"></a>시작하기 전에

- 장애 조치(failover)가 끝나고 Azure에서 VM이 부팅된 후 에이전트가 구성 서버에 다시 등록하는 데 약간의 시간(최대 15분)이 걸립니다. 이 시간 동안 다시 보호를 사용할 수 없으며 에이전트가 설치되지 않았다는 오류 메시지가 표시됩니다. 몇 분 정보 기다렸다가 다시 보호를 시도해 보세요.
- Azure VM을 기존 Azure VMware Solution VM으로 장애 복구(failback)하려면 마스터 대상 서버의 ESXi 호스트에 대한 읽기/쓰기 액세스 권한이 있는 VM 데이터 저장소를 탑재합니다.
- 대체 위치로 장애 복구(failback)하려는 경우(예를 들어 Azure VMware Solution VM이 없어서) 마스터 대상 서버에 대해 구성된 보존 드라이브와 데이터 저장소를 선택합니다. Azure VMware Solution 프라이빗 클라우드로 장애 복구(failback)할 때 장애 복구(failback) 보호 계획의 가상 머신이 마스터 대상 서버와 동일한 데이터 저장소를 사용합니다. 그 후 새 VM이 vCenter에 만들어집니다.

다음과 같이 다시 보호를 사용하도록 설정합니다.

1. **자격 증명 모음** > **복제된 항목**을 선택합니다. 장애 조치된 가상 머신을 마우스 오른쪽 단추로 클릭한 다음, **다시 보호**를 선택합니다. 또는 명령 단추에서 컴퓨터를 선택한 다음, **다시 보호**를 선택합니다.
2. 보호 방향이 **Azure에서 온-프레미스**로 선택되어 있는지 확인합니다.
3. **마스터 대상 서버** 및 **프로세스 서버**에서 온-프레미스 마스터 대상 서버 및 프로세스 서버를 선택합니다.  
4. **데이터 저장소**로는 Azure VMware Solution에서 디스크를 복구하려는 데이터 저장소를 선택합니다. 이 옵션은 Azure VMware Solution VM이 삭제되어 새 디스크를 만들어야 하는 경우에 사용합니다. 디스크가 이미 존재하는 경우 이 옵션은 무시됩니다. 값을 지정해야 합니다.
5. 보존 드라이브를 선택합니다.
6. 장애 복구 정책이 자동으로 선택됩니다.
7. **확인**을 선택하여 다시 보호를 시작합니다.

    ![다시 보호 대화 상자](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. 작업에서 Azure VM을 Azure VMware Solution 프라이빗 클라우드에 복제하기 시작합니다. **작업** 탭에서 진행률을 추적할 수 있습니다.
    - 다시 보호가 성공하면 VM은 보호된 상태가 됩니다.
    - Azure VMware Solution VM은 다시 보호 중에 꺼집니다. 이렇게 하면 복제 중 데이터 일관성을 보장할 수 있습니다.
    - 다시 보호가 완료된 후 Azure VMware Solution VM을 켜지 마세요.
   

## <a name="next-steps"></a>다음 단계

- 문제가 발생할 경우 [문제 해결 문서](vmware-azure-troubleshoot-failback-reprotect.md)를 검토하세요.
- Azure VM을 보호한 후에는 [장애 복구(failback)를 실행](avs-tutorial-failback.md)할 수 있습니다. 장애 복구(failback)는 Azure VM을 종료하고 Azure VMware Solution VM을 부팅합니다. 애플리케이션의 가동 중지 시간이 약간 있습니다. 그에 맞게 장애 복구(failback) 시간을 선택합니다.


