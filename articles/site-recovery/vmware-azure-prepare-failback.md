---
title: Azure Site Recovery를 사용 하 여 다시 보호 및 장애 복구를 위한 VMware Vm 준비
description: Azure Site Recovery로 장애 조치 (failover) 후 VMware Vm 장애 복구 준비
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362850"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>VMware Vm의 다시 보호 및 장애 복구 준비

온-프레미스 VMware Vm 또는 물리적 서버를 Azure로 [장애 조치](site-recovery-failover.md) (failover) 한 후 장애 조치 (failover) 후에 생성 된 azure vm을 다시 보호 하 여 온-프레미스 사이트로 다시 복제 합니다. Azure에서 온-프레미스로의 복제를 사용 하 여 준비가 되 면 Azure에서 온-프레미스로 장애 조치 (failover)를 실행 하 여 장애 복구 (failback)를 수행할 수 있습니다.

계속 하기 전에이 비디오를 통해 Azure에서 온-프레미스 사이트로 장애 복구 하는 방법에 대 한 간략 한 개요를 확인 하세요.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>다시 보호/장애 복구 구성 요소

Azure에서 다시 보호 하 고 장애 복구 (failback)를 수행 하려면 먼저 많은 구성 요소와 설정이 필요 합니다.

**구성 요소**| **세부 정보**
--- | ---
**온-프레미스 구성 서버** | 온-프레미스 구성 서버가 실행 중이 고 Azure에 연결 되어 있어야 합니다.<br/><br/> 장애 복구 하려는 VM이 구성 서버 데이터베이스에 있어야 합니다. 재해가 구성 서버에 영향을 주는 경우 장애 복구 (failback)가 작동 하도록 동일한 IP 주소를 사용 하 여 복원 합니다.<br/><br/>  장애 조치 (failover) 시 복제 된 컴퓨터의 IP 주소가 유지 되 면 Azure Vm 컴퓨터와 구성 서버의 장애 복구 NIC 간에 사이트 간 연결 (또는 Express 경로 연결)을 설정 해야 합니다. 유지 되는 IP 주소의 경우 구성 서버에는 원본 컴퓨터 연결용 Nic와 Azure 장애 복구 연결을 위한 두 개의 Nic가 필요 합니다. 이렇게 하면 원본 및 장애 조치 (failover) 된 Vm의 서브넷 주소 범위가 중복 되지 않습니다.
**Azure의 프로세스 서버** | 온-프레미스 사이트로 장애 복구 (failback)를 수행 하려면 Azure에 프로세스 서버가 필요 합니다.<br/><br/> 프로세스 서버는 보호 된 Azure VM에서 데이터를 수신 하 고이를 온-프레미스 사이트에 보냅니다.<br/><br/> 프로세스 서버와 보호 된 VM 간에 대기 시간이 짧은 네트워크가 필요 하므로 더 높은 복제 성능을 위해 Azure에 프로세스 서버를 배포 하는 것이 좋습니다.<br/><br/> 개념 증명의 경우 온-프레미스 프로세스 서버 및 개인 피어 링으로 Express 경로를 사용할 수 있습니다.<br/><br/> 프로세스 서버는 장애 조치 (failover) 된 VM이 있는 Azure 네트워크에 있어야 합니다. 프로세스 서버도 온-프레미스 구성 서버와 마스터 대상 서버와 통신할 수 있어야 합니다.
**별도의 마스터 대상 서버** | 마스터 대상 서버는 장애 복구 (failback) 데이터를 수신 하며 기본적으로 Windows 마스터 대상 서버는 온-프레미스 구성 서버에서 실행 됩니다.<br/><br/> 마스터 대상 서버에는 최대 60 개의 디스크가 연결 되어 있을 수 있습니다. 장애 복구 (failback) 된 Vm이 총 60 디스크를 초과 하거나 많은 양의 트래픽을 장애 복구 (failback) 하는 경우 장애 복구 (failback)를 위한 별도의 마스터 대상 서버를 만듭니다.<br/><br/> 다중 VM 일관성을 위해 컴퓨터를 복제 그룹에 수집 하는 경우 Vm은 모두 Windows 이거나 모두 Linux 여야 합니다. 이유 복제 그룹의 모든 Vm은 동일한 마스터 대상 서버를 사용 해야 하 고 마스터 대상 서버는 복제 된 컴퓨터와 동일한 운영 체제 (같거나 높은 버전)를 사용 해야 합니다.<br/><br/> 마스터 대상 서버의 디스크에는 스냅숏이 있어서는 안 됩니다. 그렇지 않으면 다시 보호 및 장애 복구가 작동 하지 않습니다.<br/><br/> 마스터 대상에는 Paravirtual SCSI 컨트롤러가 있을 수 없습니다. 컨트롤러는 LSI Logic 컨트롤러 일 수 있습니다. LSI Logic 컨트롤러가 없으면 다시 보호가 실패합니다.
**장애 복구 (Failback) 복제 정책** | 온-프레미스 사이트에 다시 복제 하려면 장애 복구 정책이 필요 합니다. 이 정책은 Azure에 대 한 복제 정책을 만들 때 자동으로 생성 됩니다.<br/><br/> 정책은 구성 서버와 자동으로 연결됩니다. RPO 임계값이 15 분, 복구 지점 보존을 24 시간으로, 앱 일치 스냅숏 빈도는 60 분으로 설정 됩니다. 정책을 편집할 수 없습니다. 
**사이트 간 VPN/Express 경로 개인 피어 링** | 다시 보호 및 장애 복구 (failback)에는 사이트 간 VPN 연결 또는 데이터를 복제 하기 위한 Express 경로 개인 피어 링이 필요 합니다. 


## <a name="ports-for-reprotectionfailback"></a>다시 보호/장애 복구를 위한 포트

다시 보호/장애 복구 (failback)를 위해 여러 포트를 열어야 합니다. 다음 그림은 포트 및 다시 보호/장애 복구 흐름을 보여 줍니다.

![장애 조치 및 장애 복구용 포트](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Azure에서 프로세스 서버 배포

1. 장애 복구를 위해 Azure에서 [프로세스 서버를 설정](vmware-azure-set-up-process-server-azure.md) 합니다.
2. Azure Vm이 프로세스 서버에 연결할 수 있는지 확인 합니다. 
3. 사이트 간 VPN 연결 또는 Express 경로 개인 피어 링 네트워크에 프로세스 서버에서 온-프레미스 사이트로 데이터를 보낼 수 있는 충분 한 대역폭이 있는지 확인 합니다.

## <a name="deploy-a-separate-master-target-server"></a>개별 마스터 대상 서버 배포

1. 마스터 대상 서버 [요구 사항 및 제한](#reprotectionfailback-components)사항에 유의 하십시오.
2. 다시 보호 하 고 장애 복구 하려는 Vm의 운영 체제와 일치 하는 [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) 또는 [Linux](vmware-azure-install-linux-master-target.md) 마스터 대상 서버를 만듭니다.
3. 마스터 대상 서버에 저장소 vMotion을 사용 하지 않도록 설정 하거나 장애 복구 (failback)가 실패할 수 있습니다. 디스크를 사용할 수 없기 때문에 VM 컴퓨터를 시작할 수 없습니다.
    - 이를 방지 하려면 vMotion 목록에서 마스터 대상 서버를 제외 합니다.
    - 마스터 대상이 다시 보호 후 저장소 vMotion 작업을 수행 하는 경우 마스터 대상 서버에 연결 된 보호 된 VM 디스크는 vMotion 작업의 대상으로 마이그레이션됩니다. 이 후 장애 복구 (failback)를 시도 하면 디스크가 없기 때문에 disk 분리가 실패 합니다. 그러면 저장소 계정에서 디스크를 찾기가 어렵습니다. 이 문제가 발생 하면 수동으로 찾아서 VM에 연결 합니다. 그 후에는 온-프레미스 VM을 부팅할 수 있습니다.

4. 기존 Windows 마스터 대상 서버에 보존 드라이브를 추가 합니다. 새 디스크를 추가하고 드라이브를 포맷합니다. 보존 드라이브는 VM이 온-프레미스 사이트에 다시 복제 되는 시점을 중지 하는 데 사용 됩니다. 이러한 조건을 확인 합니다. 이러한 조건이 충족 되지 않으면 드라이브가 마스터 대상 서버에 대해 나열 되지 않습니다.
    - 볼륨이 복제 대상과 같은 다른 용도로 사용 되지 않으며 잠금 모드가 아닙니다.
    - 볼륨은 캐시 볼륨이 아닙니다. 프로세스 서버와 마스터 대상에 대한 사용자 지정 설치 볼륨은 보존 볼륨에 적합하지 않습니다. 프로세스 서버와 마스터 대상이 볼륨에 설치되면 이 볼륨은 마스터 대상의 캐시 볼륨입니다.
    - 볼륨의 파일 시스템 형식은 FAT 또는 FAT32가 아닙니다.
    - 볼륨 용량은 0이 아닌 값입니다.
    - Windows의 기본 보존 볼륨은 R 볼륨입니다.
    - Linux의 기본 보존 볼륨은 /mnt/retention입니다.

5. 기존 프로세스 서버를 사용 하는 경우 드라이브를 추가 합니다. 새 드라이브가 마지막 단계에서 요구 사항을 충족 해야 합니다. 보존 드라이브가 없으면 포털의 드롭다운 목록에 선택 사항이 나열되지 않습니다. 온-프레미스 마스터 대상에 드라이브를 추가하고 나면 드라이브가 포털의 선택 사항에 나타나는 데 최대 15분이 걸립니다. 15 분 후에도 드라이브가 표시 되지 않으면 구성 서버를 새로 고칠 수 있습니다.
6. 마스터 대상 서버에 VMware 도구 또는 open-vm-tools를 설치합니다. 도구가 없으면 마스터 대상의 ESXi 호스트에 있는 데이터 저장소를 검색할 수 없습니다.
7. 디스크를 설정 합니다. VMware의 마스터 대상 VM 구성 매개 변수에서 EnableUUID = true 설정 이 행이 없는 경우 추가합니다. 올바르게 탑재할 수 있도록 VMDK에 일관성 있는 UUID를 제공하려면 이 설정이 필요합니다.
8. VCenter Server 액세스 요구 사항 확인:
    - 장애 복구 (failback) 할 VM이 VMware vCenter Server에서 관리 하는 ESXi 호스트에 있는 경우 마스터 대상 서버는 가상 컴퓨터의 디스크에 복제 된 데이터를 쓰기 위해 온-프레미스 VM .VMDK (가상 머신 디스크) 파일에 대 한 액세스 권한이 필요 합니다. 읽기/쓰기 액세스 권한으로 온-프레미스 VM 데이터 저장소가 마스터 대상 호스트에 탑재 되어 있는지 확인 합니다.
    - VMware vCenter Server에서 관리 하는 ESXi 호스트에 VM이 없는 경우는 다시 보호 중에 새 VM을 만듭니다 Site Recovery 이 VM은 마스터 대상 서버 VM을 만드는 ESXi 호스트에 만들어집니다. ESXi 호스트를 신중 하 게 선택 하 여 원하는 호스트에 VM을 만듭니다. VM의 하드 디스크는 마스터 대상 서버가 실행 중인 호스트에서 액세스할 수 있는 데이터 저장소에 있어야 합니다.
    - 장애 복구(Failback)를 위한 온-프레미스 VM이 이미 있는 경우의 또 다른 옵션은 장애 복구(Failback)를 수행하기 전에 해당 VM을 삭제하는 것입니다. 그런 다음 장애 복구 (Failback)는 마스터 대상 ESXi 호스트와 동일한 호스트에 새 VM을 만듭니다. 대체 위치로 장애 복구 (failback) 하는 경우 데이터는 온-프레미스 마스터 대상 서버에서 사용 하는 것과 동일한 데이터 저장소 및 동일한 ESXi 호스트에 복구 됩니다.
9. VMware Vm으로 장애 복구 (failback) 하는 물리적 컴퓨터의 경우 컴퓨터를 다시 보호 하기 전에 마스터 대상 서버가 실행 되는 호스트의 검색을 완료 해야 합니다.
10. 마스터 대상 VM에 하나 이상의 VMFS (가상 머신 파일 시스템) 데이터 저장소가 연결 되어 있는 ESXi 호스트를 확인 합니다. VMFS 데이터 저장소가 연결 되어 있지 않으면 다시 보호 설정의 데이터 저장소 입력이 비어 있으므로 계속할 수 없습니다.


## <a name="next-steps"></a>다음 단계

VM [을 다시 보호 합니다.](vmware-azure-reprotect.md)
