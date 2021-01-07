---
title: Azure Site Recovery를 사용 하 여 온-프레미스 사이트에 VMware Vm 다시 보호
description: Azure Site Recovery를 사용 하 여 Azure로 장애 조치 한 후 VMware Vm을 다시 보호 하는 방법을 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441496"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Azure에서 온-프레미스로 다시 보호

Azure로 온-프레미스 VMware VM 및 물리적 서버를 [장애 조치](site-recovery-failover.md)한 후에 온-프레미스 사이트를 장애 복구(Failback)하는 첫 번째 단계는 장애 조치(failover) 중에 생성된 Azure VM을 다시 보호하는 것입니다. 이 문서에서는 이를 수행하는 방법을 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

1. [이 문서의](vmware-azure-prepare-failback.md) 단계에 따라 Azure에서 프로세스 서버를 설정 하 고 온-프레미스 마스터 대상 서버를 설정 하 고 장애 복구 (failback)를 위해 사이트 간 VPN 또는 express 경로 개인 피어 링을 구성 하는 등 다시 보호 및 장애 복구 (failback)를 준비 합니다.
2. 온-프레미스 구성 서버가 실행 중이 고 Azure에 연결 되어 있는지 확인 합니다. Azure로 장애 조치 (failover) 하는 동안 온-프레미스 사이트에 액세스할 수 없으며 구성 서버가 사용할 수 없거나 종료 될 수 있습니다. 장애 복구 (failback) 중에 VM이 구성 서버 데이터베이스에 있어야 합니다. 그렇지 않으면 장애 복구가 실패합니다.
3. 온-프레미스 마스터 대상 서버에서 스냅숏을 삭제 합니다. 스냅숏이 있는 경우 다시 보호가 작동 하지 않습니다.  VM의 스냅숏은 다시 보호 작업 중에 자동으로 병합 됩니다.
4. 다중 VM 일관성을 위해 복제 그룹으로 수집 된 Vm을 다시 보호 하는 경우에는 모두 동일한 운영 체제 (Windows 또는 Linux)가 있는지 확인 하 고 배포 하는 마스터 대상 서버에 동일한 유형의 운영 체제가 있는지 확인 합니다. 복제 그룹의 모든 Vm은 동일한 마스터 대상 서버를 사용 해야 합니다.
5. 장애 복구 (failback)에 [필요한 포트를](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) 엽니다.
6. 장애 복구 전에 vCenter Server 연결 되어 있는지 확인 합니다. 연결되지 않은 경우 디스크의 연결을 끊고 가상 머신을 다시 연결하는 작업이 실패합니다.
7. VCenter server에서 장애 복구 (failback) 할 Vm을 관리 하는 경우 필요한 권한이 있는지 확인 합니다. 읽기 전용 사용자 vCenter 검색을 수행하고 가상 머신을 보호하면 보호에 성공하고 장애 조치가 작동합니다. 그러나 다시 보호 중에는 데이터 저장소를 검색할 수 없고 다시 보호 중에 나열 되지 않으므로 장애 조치 (failover)가 실패 합니다. 이 문제를 해결 하려면 [적절 한 계정/권한을](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)사용 하 여 vCenter 자격 증명을 업데이트 한 후 작업을 다시 시도 하십시오. 
8. 템플릿을 사용 하 여 가상 머신을 만든 경우 각 VM에 디스크에 대 한 고유한 UUID가 있는지 확인 합니다. 둘 다 동일한 템플릿에서 만들어졌으므로 온-프레미스 VM UUID가 마스터 대상 서버의 UUID와 충돌 하는 경우 다시 보호가 실패 합니다. 다른 템플릿에서 배포 합니다.
9. 대체 vCenter Server 장애 복구 (failback) 하는 경우 새 vCenter Server와 마스터 대상 서버가 검색 되었는지 확인 합니다. 일반적으로 데이터 저장소에 액세스할 수 없거나 다시 보호에 표시 되지 않는 경우 **입니다.**
10. 장애 복구 (failback) 할 수 없는 다음 시나리오를 확인 합니다.
    - ESXi 5.5 free edition 또는 vSphere 6 하이퍼바이저 무료 버전을 사용 하 고 있는 경우 다른 버전으로 업그레이드 합니다.
    - Windows Server 2008 R2 SP1 물리적 서버가 있는 경우
    - VMware Vm은 Hyper-v로 장애 복구 (failback) 할 수 없습니다.
    - 마이그레이션된 Vm입니다.
    - 다른 리소스 그룹으로 이동 된 VM입니다.
    - 삭제 된 복제본 Azure VM입니다.
    - 보호 되지 않는 복제본 Azure VM (온-프레미스 사이트에 복제)
10. 사용할 수 있는 장애 복구 유형 (원래 위치 복구 및 대체 위치 복구 [)을 검토](concepts-types-of-failback.md) 합니다.


## <a name="enable-reprotection"></a>다시 보호 사용

복제를 활성화합니다. 특정 Vm 또는 복구 계획을 다시 보호할 수 있습니다.

- 복구 계획을 다시 보호 하는 경우 보호 된 모든 컴퓨터에 대 한 값을 제공 해야 합니다.
- Vm이 다중 VM 일관성을 위해 복제 그룹에 속하는 경우 복구 계획을 사용 하 여 다시 보호 수 있습니다. 복제 그룹의 Vm은 동일한 마스터 대상 서버를 사용 해야 합니다.

>[!NOTE]
>다시 보호 중에는 Azure에서 erstwhile로 전송 되는 데이터의 양은 0 바이트에서 모든 보호 된 컴퓨터에 대 한 디스크 크기의 합계 일 수 있으며 계산할 수 없습니다.

### <a name="before-you-start"></a>시작하기 전에

- 장애 조치 (failover) 후 Azure에서 VM을 부팅 한 후 에이전트에서 구성 서버에 다시 등록 하는 데 약간의 시간이 걸립니다 (최대 15 분). 이 시간 동안 다시 보호를 사용할 수 없으며 에이전트가 설치되지 않았다는 오류 메시지가 표시됩니다. 이 문제가 발생 하는 경우 몇 분 정도 기다린 후 다시 보호 합니다.
- Azure VM을 기존 온-프레미스 VM으로 장애 복구 (failback) 하려는 경우 마스터 대상 서버의 ESXi 호스트에 대 한 읽기/쓰기 액세스 권한으로 온-프레미스 VM 데이터 저장소를 탑재 합니다.
- 다른 위치로 장애 복구 (failback) 하려는 경우, 예를 들어 온-프레미스 VM이 없는 경우 마스터 대상 서버에 대해 구성 된 보존 드라이브 및 데이터 저장소를 선택 합니다. 온-프레미스 사이트로 장애 복구할 때 장애 복구 보호 계획에 있는 VMware 가상 머신에서 마스터 대상 서버와 동일한 데이터 저장소를 사용합니다. 새 VM이 vCenter에 만들어집니다.

다음과 같이 다시 보호를 사용 하도록 설정 합니다.

1. **자격 증명 모음**  >  **복제 된 항목**을 선택 합니다. 장애 조치된 가상 머신을 마우스 오른쪽 단추로 클릭한 다음, **다시 보호**를 선택합니다. 또는 명령 단추에서 컴퓨터를 선택한 다음, **다시 보호**를 선택합니다.
2. 보호 방향이 **Azure에서 온-프레미스**로 선택되어 있는지 확인합니다.
3. **마스터 대상 서버** 및 **프로세스 서버**에서 온-프레미스 마스터 대상 서버 및 프로세스 서버를 선택합니다.  
4. **데이터 저장소**로는 온-프레미스에서 디스크를 복구하려는 데이터 저장소를 선택합니다. 이 옵션은 온-프레미스 가상 머신이 삭제되고 새 디스크를 만들어야 하는 경우에 사용합니다. 디스크가 이미 존재하는 경우 이 옵션은 무시됩니다. 값을 지정해야 합니다.
5. 보존 드라이브를 선택합니다.
6. 장애 복구 정책이 자동으로 선택됩니다.
7. **확인**을 선택하여 다시 보호를 시작합니다.

    ![다시 보호 대화 상자](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. 작업은 Azure VM을 온-프레미스 사이트에 복제 하기 시작 합니다. **작업** 탭에서 진행률을 추적할 수 있습니다.
    - 다시 보호 성공적으로 완료 되 면 VM이 보호 된 상태가 됩니다.
    - 온-프레미스 VM이 다시 보호 중에 꺼집니다. 이렇게 하면 복제 중 데이터 일관성을 보장할 수 있습니다.
    - 다시 보호 완료 된 후 온-프레미스 VM을 켜지 마세요.
   

## <a name="next-steps"></a>다음 단계

- 문제가 발생 하는 경우 [문제 해결 문서](vmware-azure-troubleshoot-failback-reprotect.md)를 검토 합니다.
- Azure Vm을 보호 한 후 [장애 복구를 실행할](vmware-azure-failback.md)수 있습니다. 장애 복구는 Azure VM을 종료 하 고 온-프레미스 VM을 부팅 합니다. 응용 프로그램의 가동 중지 시간이 발생 하 고 그에 따라 장애 복구 시간을 선택 합니다.


