---
title: Azure 마이그레이션에서 VM웨어 마이그레이션 지원
description: Azure 마이그레이션에서 VMware VM 마이그레이션에 대한 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: bf9cc471eef31edd513358a97d2ece17015ba781
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314004"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 마이그레이션을 위한 지원 매트릭스

이 문서에서는 [Azure 마이그레이션: 서버 마이그레이션을](migrate-services-overview.md#azure-migrate-server-migration-tool) 사용 하 고 VM웨어 VM 마이그레이션에 대 한 지원 설정 및 제한 을 요약 합니다. Azure로 마이그레이션하기 위해 VMware VM을 평가하는 방법을 찾고 있는 경우 [평가 지원 매트릭스를](migrate-support-matrix-vmware.md)검토하십시오.


## <a name="migration-options"></a>마이그레이션 옵션

다음과 같은 몇 가지 방법으로 VMware VM을 마이그레이션할 수 있습니다.

- 에이전트 없는 마이그레이션: VM에 아무 것도 설치할 필요 없이 VM을 마이그레이션합니다. 에이전트 없는 마이그레이션을 위해 [Azure 마이그레이션 어플라이언스를](migrate-appliance.md) 배포합니다.
- 에이전트 기반 마이그레이션: 복제를 위해 VM에 에이전트를 설치합니다. 에이전트 기반 마이그레이션의 경우 복제 [어플라이언스를](migrate-replication-appliance.md)배포해야 합니다.

[이 문서를](server-migrate-overview.md) 검토하여 사용할 방법을 알아내보도록 합니다.

## <a name="migration-limitations"></a>마이그레이션 제한 사항

- 복제를 위해 한 번에 최대 10개의 VM을 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려면 10개의 그룹으로 복제합니다.
- VMware 에이전트리스 마이그레이션의 경우 최대 100개의 복제를 동시에 실행할 수 있습니다.

## <a name="agentless-vmware-servers"></a>에이전트 리스-VM웨어 서버

**VMware** | **세부 정보**
--- | ---
**VMware vCenter 서버** | 버전 5.5, 6.0, 6.5 또는 6.7.
**VMware vSphere ESXI 호스트** | 버전 5.5, 6.0, 6.5 또는 6.7.
**v센터 서버 권한** | 에이전트 없는 마이그레이션은 [마이그레이션 어플라이언스를](migrate-appliance.md)사용합니다. 어플라이언스에는 다음 권한이 필요합니다.<br/><br/> - **Datastore.Browse**: VM 로그 파일을 탐색하여 스냅샷 생성 및 삭제 문제를 해결할 수 있습니다.<br/><br/> - **Datastore.LowLevelFileOperations**: 데이터스토어 브라우저에서 읽기/쓰기/삭제/이름 바꾸기 작업을 허용하여 스냅샷 생성 및 삭제 문제를 해결합니다.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**: VM 디스크의 변경 사항 추적을 활성화 하거나 비활성화하여 스냅샷 간에 변경된 데이터 블록을 가져올 수 있습니다.<br/><br/> - **VirtualMachine.Configuration.DiskLease**: VM에 대한 디스크 임대 작업을 허용하여 VMware vSphere 가상 디스크 개발 키트(VDDK)를 사용하여 디스크를 읽을 수 있습니다.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (특히 vSphere 6.0 이상) VDDK를 사용하여 디스크에서 임의읽기 액세스를 위해 VM에서 디스크를 열 수 있습니다.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**: VM에서 디스크를 열때 VDDK를 사용하여 디스크를 읽을 수 있습니다.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: VM에서 디스크를 열때 VDDK를 사용하여 디스크를 읽을 수 있습니다.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**: VM과 연결된 파일에 대한 읽기 작업을 허용하여 로그를 다운로드하고 오류가 발생할 경우 문제를 해결할 수 있습니다.<br/><br/> - ** 가상 머신.스냅 샷 관리.***: 복제를 위한 VM 스냅샷 생성 및 관리를 허용합니다.<br/><br/> - **가상 컴퓨터.상호 작용.전원 끄기**: Azure로 마이그레이션하는 동안 VM의 전원을 끌 수 있습니다.



## <a name="agentless-vmware-vms"></a>에이전트리스 VM웨어 VM

**지원** | **세부 정보**
--- | ---
**지원되는 운영 체제** | Azure에서 지원하는 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제는 에이전트 없는 마이그레이션을 사용하여 마이그레이션할 수 있습니다.
**Azure에 필요한 변경 사항** | 일부 VM은 Azure에서 실행될 수 있도록 변경해야 할 수 있습니다. Azure Migrate는 다음 운영 체제에 대해 이러한 변경 사항을 자동으로 변경합니다.<br/> - 레드 햇 엔터프라이즈 리눅스 6.5 +, 7.0+<br/> - 센트OS 6.5+, 7.0+</br> - 수지 리눅스 엔터프라이즈 서버 12 SP1+<br/> - 우분투 14.04LTS, 16.04LTS, 18.04LTS<br/> - 데비안 7, 8<br/><br/> 다른 운영 체제의 경우 마이그레이션하기 전에 수동으로 조정해야 합니다. 관련 문서에는 이 작업을 수행하는 방법에 대한 지침이 포함되어 있습니다.
**리눅스 부팅** | /boot가 전용 파티션에 있는 경우 OS 디스크에 상주해야 하며 여러 디스크에 분산되지 않아야 합니다.<br/> /boot가 루트(/) 파티션의 일부인 경우 '/' 파티션은 OS 디스크에 있어야 하며 다른 디스크에 걸쳐 서는 안 됩니다.
**UEFI 부츠** | UEFI 부팅이 있는 VM은 마이그레이션에 지원되지 않습니다.
**디스크 크기** | 2 TB OS 디스크; 데이터 디스크의 경우 8TB입니다.
**디스크 제한** |  VM당 최대 60개의 디스크.
**암호화된 디스크/볼륨** | 암호화된 디스크/볼륨이 있는 VM은 마이그레이션에 지원되지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원되지 않습니다.
**RDM/패스스루 디스크** | VM에 RDM 또는 통과 디스크가 있는 경우 이러한 디스크는 Azure에 복제되지 않습니다.
**NFS** | VM의 볼륨으로 탑재된 NFS 볼륨은 복제되지 않습니다.
**iSCSI 타겟** | iSCSI 대상이 있는 VM은 에이전트 없는 마이그레이션에 대해 지원되지 않습니다.
**멀티패스 IO** | 지원되지 않습니다.
**스토리지 vMotion** | 지원되지 않습니다. VM에서 저장소 vMotion을 사용하는 경우 복제가 작동하지 않습니다.
**팀식 NIC** | 지원되지 않습니다.
**IPv6** | 지원되지 않습니다.
**대상 디스크** | VM은 Azure에서 관리되는 디스크(표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**동시 복제** | vCenter 서버당 100VM. 더 많은 경우 100의 일괄 처리로 마이그레이션합니다.


## <a name="agentless-azure-migrate-appliance"></a>에이전트 없는 Azure 마이그레이션 어플라이언스 
에이전트 없는 마이그레이션은 VMware VM에 배포된 Azure 마이그레이션 어플라이언스를 사용합니다.

- VMware의 [어플라이언스 요구 사항에](migrate-appliance.md#appliance---vmware) 대해 알아봅니다.
- 어플라이언스가 액세스해야 하는 [URL에](migrate-appliance.md#url-access) 대해 알아봅니다.

## <a name="agentless-ports"></a>에이전트 없는 포트

**디바이스** | **연결**
--- | ---
어플라이언스 | 포트 443의 아웃바운드 연결은 복제된 데이터를 Azure에 업로드하고 복제 및 마이그레이션을 오케스트레이션하는 Azure Migrate 서비스와 통신합니다.
vCenter Server | 포트 443의 인바운드 연결로 어플라이언스가 복제를 오케스트레이션할 수 있도록 - 스냅샷 생성, 데이터 복사, 릴리스 스냅샷
vSphere/EXSI 호스트 | 스냅샷에서 데이터를 복제하는 어플라이언스에 대한 TCP 포트 902에 대한 인바운드입니다.


## <a name="agent-based-vmware-servers"></a>에이전트 기반 VMware 서버
이 표에는 VMware 가상화 서버에 대한 평가 지원 및 제한 사항이 요약되어 있습니다.

**VMware 요구 사항** | **세부 정보**
--- | ---
**VMware vCenter 서버** | 버전 5.5, 6.0, 6.5 또는 6.7.
**VMware vSphere ESXI 호스트** | 버전 5.5, 6.0, 6.5 또는 6.7.
**v센터 서버 권한** | vCenter 서버에 대한 읽기 전용 계정입니다.

## <a name="agent-based-vmware-vms"></a>에이전트 기반 VM웨어 VM

이 표에는 에이전트 기반 마이그레이션을 사용하여 마이그레이션하려는 VMware VM에 대한 VMware VM 지원이 요약되어 있습니다.

**지원** | **세부 정보**
--- | ---
**머신 워크로드** | Azure Migrate는 지원되는 컴퓨터에서 실행되는 모든 워크로드(예: Active Directory, SQL 서버 등)의 마이그레이션을 지원합니다.
**운영 체제** | 최신 정보는 사이트 복구에 대한 [운영 체제 지원을](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 검토합니다. Azure 마이그레이션은 동일한 VM 운영 체제 지원을 제공합니다.
**리눅스 파일 시스템 /게스트 스토리지** | 최신 정보는 사이트 복구에 대한 [Linux 파일 시스템 지원을](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 검토합니다. Azure 마이그레이션에는 동일한 Linux 파일 시스템 지원이 있습니다.
**네트워크/스토리지** | 최신 정보는 사이트 복구의 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 및 [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 필수 구성 조건을 검토합니다. Azure 마이그레이션은 동일한 네트워크/저장소 요구 사항을 제공합니다.
**Azure 요구 사항** | 최신 정보는 [Azure 네트워크,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)및 사이트 복구에 대한 계산 요구 사항을 [검토합니다.](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) Azure 마이그레이션에는 VMware 마이그레이션에 대한 동일한 요구 사항이 있습니다.
**모바일 서비스** | 마이그레이션하려는 각 VM에 모빌리티 서비스 에이전트를 설치해야 합니다.
**UEFI 부츠** | Azure에서 마이그레이션된 VM은 BIOS 부팅 VM으로 자동으로 변환됩니다.<br/><br/> OS 디스크에는 최대 4개의 파티션이 있어야 하며 볼륨은 NTFS로 포맷되어야 합니다.
**대상 디스크** | VM은 Azure에서 관리되는 디스크(표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**디스크 크기** | 2 TB OS 디스크; 데이터 디스크의 경우 8TB입니다.
**디스크 제한** |  VM당 최대 63개의 디스크.
**암호화된 디스크/볼륨** | 암호화된 디스크/볼륨이 있는 VM은 마이그레이션에 지원되지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원됩니다.
**통과 디스크** | 지원됩니다.
**NFS** | VM의 볼륨으로 탑재된 NFS 볼륨은 복제되지 않습니다.
**iSCSI 타겟** | iSCSI 대상이 있는 VM은 에이전트 없는 마이그레이션에 대해 지원되지 않습니다.
**멀티패스 IO** | 지원되지 않습니다.
**스토리지 vMotion** | 지원됨
**팀식 NIC** | 지원되지 않습니다.
**IPv6** | 지원되지 않습니다.




## <a name="agent-based-replication-appliance"></a>에이전트 기반 복제 어플라이언스 

Azure 마이그레이션 허브에서 제공 하는 OVA 템플릿을 사용 하 여 복제 어플라이언스를 설정 하는 경우 응용 프로그램은 Windows Server 2016를 실행 하 고 지원 요구 사항을 준수 합니다. 실제 서버에서 복제 어플라이언스를 수동으로 설정한 경우 요구 사항을 준수하는지 확인합니다.

- VMware의 [복제 어플라이언스 요구 사항에](migrate-replication-appliance.md#appliance-requirements) 대해 알아봅니다.
- MySQL은 어플라이언스에 설치해야 합니다. 설치 [옵션에](migrate-replication-appliance.md#mysql-installation)대해 자세히 알아봅니다.
- 복제 어플라이언스에 액세스하는 데 필요한 [URL](migrate-replication-appliance.md#url-access) 및 [포트에](migrate-replication-appliance.md#port-access) 대해 알아봅니다.

## <a name="agent-based-ports"></a>에이전트 기반 포트

**디바이스** | **연결**
--- | ---
VM | VM에서 실행되는 모빌리티 서비스는 복제 관리를 위해 포트 HTTPS 443 인바운드의 온-프레미스 복제 어플라이언스(구성 서버)와 통신합니다.<br/><br/> VM은 HTTPS 9443 인바운드 포트의 프로세스 서버(구성 서버 컴퓨터에서 실행)로 복제 데이터를 전송합니다. 이 포트는 수정할 수 있습니다.
복제 어플라이언스 | 복제 어플라이언스는 포트 HTTPS 443 아웃바운드를 통해 Azure를 통해 복제를 오케스트레이션합니다.
프로세스 서버 | 프로세스 서버는 복제 데이터를 수신하고 최적화하고 암호화한 다음 포트 443 아웃바운드를 통해 Azure 저장소로 보냅니다.<br/> 기본적으로 프로세스 서버는 복제 어플라이언스에서 실행됩니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure에 복제된 모든 온-프레미스 VM은 이 테이블에 요약된 Azure VM 요구 사항을 충족해야 합니다. 사이트 복구가 복제에 대한 필수 구성 조건 검사를 실행하면 일부 요구 사항이 충족되지 않으면 검사가 실패합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 마이그레이션을 위해 지원되는 VMware VM 운영 체제를 확인합니다.<br/> 지원되는 운영 체제에서 실행 중인 모든 워크로드를 마이그레이션할 수 있습니다. | 지원되지 않는 경우 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 64개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 최대 4,095GB | 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
VM 이름 | 1~63자 사이입니다.<br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
마이그레이션 후 연결-Windows | 마이그레이션 후 Windows를 실행하는 Azure VM에 연결하려면 다음을 수행하십시오.<br/> - 마이그레이션 전에 온-프레미스 VM에서 RDP를 사용할 수 있습니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.<br/> 사이트 간 VPN 액세스를 위해 RDP를 사용하도록 설정하고 **Windows 방화벽에서** -> RDP허용**앱 및** 도메인 및 **개인** 네트워크에 대한 기능을 허용합니다. 또한 운영 체제의 SAN 정책이 **OnlineAll로**설정되어 있는지 확인합니다. [자세히 알아보기](prepare-for-migration.md). |
마이그레이션 후 연결-리눅스 | SSH를 사용하여 마이그레이션한 후 Azure VM에 연결하려면 다음을 수행합니다.<br/> 온-프레미스 컴퓨터에서 마이그레이션하기 전에 보안 셸 서비스가 시작으로 설정되어 있는지, 방화벽 규칙에서 SSH 연결을 허용하는지 확인합니다.<br/> 장애 조치 후 Azure VM에서 VM을 통해 실패한 네트워크 보안 그룹 규칙및 연결된 Azure 서브넷에 대한 SSH 포트에 대한 들어오는 연결을 허용합니다. 또한 VM에 대한 공용 IP 주소를 추가합니다. |  


## <a name="next-steps"></a>다음 단계

VMware 마이그레이션 옵션을 [선택합니다.](server-migrate-overview.md)
