---
title: Azure Site Recovery를 사용 하 여 Azure에서 VMware v m/물리적 서버 장애 복구 (failback)
description: Azure로 VMware VM 및 물리적 서버의 재해 복구 중에 Azure로 장애 조치(Failover)한 후 온-프레미스 사이트로 장애 복구(Failback)하는 방법을 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495333"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>VMware Vm을 온-프레미스 사이트로 장애 복구 (failback)

이 문서에서는 온-프레미스 Vm의 [장애 조치 (failover)](site-recovery-failover.md) 를 사용 하 [Azure Site Recovery](site-recovery-overview.md)여 azure vm을 온-프레미스 사이트로 장애 복구 하는 방법을 설명 합니다. 온-프레미스로 장애 복구 (failback) 한 후 온-프레미스 Vm이 Azure로 복제를 시작 하도록 복제를 사용 하도록 설정 합니다.

## <a name="before-you-start"></a>시작하기 전에

1. [VMware 장애 복구](failover-failback-overview.md#vmwarephysical-reprotectionfailback)에 대해 알아봅니다. 
2. [장애 복구를 준비](vmware-azure-prepare-failback.md)하 고 모든 필수 구성 요소를 배포 하는 단계를 검토 하 고 완료 했는지 확인 합니다. 구성 요소에는 장애 복구를 위해 Azure의 프로세스 서버, 온-프레미스 마스터 대상 서버, VPN 사이트 간 연결 (또는 Express 경로 개인 피어 링)이 포함 됩니다.
3. 다시 보호 및 장애 복구 (failback)에 대 한 [요구 사항을](vmware-azure-reprotect.md#before-you-begin) 완료 했으며 azure Vm이 azure에서 온-프레미스 사이트로 복제 되도록 [다시 보호를 사용 하도록 설정](vmware-azure-reprotect.md#enable-reprotection) 했는지 확인 합니다. 장애 복구 하려면 Vm이 복제 된 상태 여야 합니다.




## <a name="run-a-failover-to-fail-back"></a>장애 복구 (failback) 실행

1. Azure Vm이 다시 보호 되 고 온-프레미스 사이트로 복제 되는지 확인 합니다. 
    - 장애 복구 (failback)를 위해 VM에 하나 이상의 복구 지점이 필요 합니다.
    - 복구 계획을 장애 복구 (failback) 하는 경우 계획의 모든 컴퓨터에 하나 이상의 복구 지점이 있어야 합니다.
2. 자격 증명 모음 > **복제 된 항목**에서 VM을 선택 합니다. VM > **계획 되지 않은 장애 조치 (Failover)** 를 마우스 오른쪽 단추로 클릭 합니다.
3. **장애 조치(failover) 확인**에서 장애 조치(Failover) 방향이 Azure부터인지 확인합니다.
4. 장애 조치(Failover)에 사용할 복구 지점을 선택합니다.
    - **최신** 복구 지점을 사용 하는 것이 좋습니다. 앱 일치 지점이 최근 시점 보다 뒤에 있으며 일부 데이터 손실이 발생 합니다.
    - **최신 버전** 은 크래시 일치 복구 지점입니다.
    - **최신**버전에서는 VM이 사용 가능한 최신 시점으로 장애 조치 (failover) 됩니다. 복구 계획 내에서 다중 VM 일관성에 대 한 복제 그룹이 있는 경우 그룹의 각 VM은 해당 하는 최신 시점으로 장애 조치 (failover) 됩니다.
    - 앱 일치 복구 지점을 사용 하는 경우 각 VM은 사용 가능한 최신 지점으로 다시 장애 복구 됩니다. 복구 계획에 복제 그룹이 있는 경우 각 그룹은 사용 가능한 일반 복구 지점으로 복구 됩니다.
5. 장애 조치 (Failover)가 시작 됩니다. Site Recovery Azure Vm을 종료 합니다.
6. 장애 조치 (failover)가 완료 된 후 모든 것이 예상 대로 작동 하는지 확인 합니다. Azure Vm이 종료 되었는지 확인 합니다. 
7. 모든 것이 확인 되 면 VM > **커밋**을 마우스 오른쪽 단추로 클릭 하 여 장애 조치 (failover) 프로세스를 완료 합니다. Commit은 장애 조치 된 Azure VM을 제거 합니다. 

> [!NOTE]
> Windows Vm의 경우 장애 조치 중에 VMware 도구를 사용 하지 않도록 설정 Site Recovery. Windows VM의 장애 복구 (failback) 중에 VMware 도구를 다시 사용 하도록 설정 합니다. 




## <a name="reprotect-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 다시 보호

장애 복구를 커밋한 후에 Azure Vm이 삭제 됩니다. VM이 온-프레미스 사이트로 다시 이동 되지만 보호 되지 않습니다. Vm을 Azure로 다시 복제 하기 시작 하려면 다음과 같이 합니다.

1. 자격 증명 모음 > **복제 된 항목**에서 장애 복구 된 vm을 선택 하 고 **다시 보호**를 선택 합니다.
2. Azure로 데이터를 다시 전송 하는 데 사용 되는 프로세스 서버를 지정 합니다.
3. **확인**을 선택하여 다시 보호 작업을 시작합니다.

> [!NOTE]
> 온-프레미스 VM이 시작 된 후 에이전트에서 구성 서버에 다시 등록 하는 데 최대 15 분이 걸립니다. 이 시간 동안 다시 보호가 실패하고 에이전트가 설치되지 않았다는 오류 메시지가 반환됩니다. 이 문제가 발생 하는 경우 몇 분 정도 기다렸다가 다시 보호 합니다.

## <a name="next-steps"></a>다음 단계

다시 보호 작업이 완료 되 면 온-프레미스 VM이 Azure에 복제 됩니다. 필요에 따라 Azure에 대 한 [다른 장애 조치 (failover)를 실행할](site-recovery-failover.md) 수 있습니다.

