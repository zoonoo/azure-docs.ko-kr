---
title: Azure 마이그레이션의 물리적 서버 마이그레이션 지원
description: Azure 마이그레이션에서 물리적 서버 마이그레이션에 대한 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 01/07/2020
ms.openlocfilehash: 8f8b94ab77a1eef8e771384f5d69da98a1d7ae6c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520275"
---
# <a name="support-matrix-for-physical-server-migration"></a>물리적 서버 마이그레이션을 위한 지원 매트릭스

이 문서에서는 [Azure 마이그레이션: 서버 마이그레이션을](migrate-services-overview.md#azure-migrate-server-migration-tool) 사용 하 고 실제 서버를 마이그레이션하기 위한 지원 설정 및 제한 사항에 대해 요약합니다. Azure로 마이그레이션할 물리적 서버 평가에 대한 정보를 찾고 있는 경우 [평가 지원 매트릭스를](migrate-support-matrix-physical.md)검토합니다.


## <a name="overview"></a>개요

에이전트 기반 복제를 사용하여 온-프레미스 컴퓨터를 물리적 서버로 마이그레이션할 수 있습니다. 이 도구를 사용하면 다음과 같은 다양한 머신을 Azure로 마이그레이션할 수 있습니다.

- 온-프레미스 물리적 서버.
- Xen, KVM 과 같은 플랫폼으로 가상화 된 VM.
- 어떤 이유로 표준 [Hyper-V](tutorial-migrate-hyper-v.md) 또는 VMware 흐름을 사용하지 않으려는 경우 하이퍼 VM 또는 [VM웨어 VM입니다.](server-migrate-overview.md)
- 프라이빗 클라우드에서 실행되는 VM입니다.
- 아마존 웹 서비스(AWS) 또는 구글 클라우드 플랫폼(GCP)과 같은 퍼블릭 클라우드에서 실행되는 VM입니다.


## <a name="migration-limitations"></a>마이그레이션 제한 사항

복제를 위해 한 번에 최대 10대의 컴퓨터를 선택할 수 있습니다. 더 많은 컴퓨터를 마이그레이션하려면 10개의 그룹으로 복제합니다.


## <a name="physical-server-requirements"></a>물리적 서버 요구 사항

이 표에는 에이전트 기반 마이그레이션을 사용하여 마이그레이션하려는 실제 서버에 대한 지원이 요약되어 있습니다.

**지원** | **세부 정보**
--- | ---
**머신 워크로드** | Azure Migrate는 지원되는 컴퓨터에서 실행되는 모든 워크로드(예: Active Directory, SQL 서버 등)의 마이그레이션을 지원합니다.
**운영 체제** | 최신 정보는 사이트 복구에 대한 [운영 체제 지원을](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 검토합니다. Azure 마이그레이션은 동일한 운영 체제 지원을 제공합니다.
**리눅스 파일 시스템 /게스트 스토리지** | 최신 정보는 사이트 복구에 대한 [Linux 파일 시스템 지원을](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) 검토합니다. Azure 마이그레이션은 동일한 Linux 파일 시스템 지원을 제공합니다.
**네트워크/스토리지** | 최신 정보는 사이트 복구의 [네트워크](../site-recovery/vmware-physical-azure-support-matrix.md#network) 및 [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#storage) 필수 구성 조건을 검토합니다. Azure 마이그레이션은 동일한 네트워크/저장소 요구 사항을 제공합니다.
**Azure 요구 사항** | 최신 정보는 [Azure 네트워크,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [저장소](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)및 사이트 복구에 대한 계산 요구 사항을 [검토합니다.](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) Azure 마이그레이션에는 물리적 서버 마이그레이션에 대한 동일한 요구 사항이 있습니다.
**모빌리티 서비스** | 마이그레이션하려는 각 컴퓨터에 모빌리티 서비스 에이전트를 설치해야 합니다.
**UEFI 부츠** | Azure에서 마이그레이션된 컴퓨터는 BIOS 부팅 Azure VM으로 자동으로 변환됩니다. Windows Server 2012를 실행하는 서버만 나중에 지원됩니다.<br/><br/> OS 디스크에는 최대 4개의 파티션이 있어야 하며 볼륨은 NTFS로 포맷되어야 합니다.
**대상 디스크** | 컴퓨터는 Azure에서 관리되는 디스크(표준 HDD, 프리미엄 SSD)로만 마이그레이션할 수 있습니다.
**디스크 크기** | 2 TB OS 디스크; 데이터 디스크의 경우 8TB입니다.
**디스크 제한** |  컴퓨터당 최대 63개의 디스크.
**암호화된 디스크/볼륨** |  암호화된 디스크/볼륨이 있는 컴퓨터는 마이그레이션을 위해 지원되지 않습니다.
**공유된 디스크 클러스터** | 지원되지 않습니다.
**독립 디스크** | 지원됩니다.
**통과 디스크** | 지원됩니다.
**NFS** | 컴퓨터의 볼륨으로 탑재된 NFS 볼륨은 복제되지 않습니다.
**iSCSI 타겟** | iSCSI 대상이 있는 컴퓨터는 에이전트 없는 마이그레이션에 대해 지원되지 않습니다.
**멀티패스 IO** | 지원되지 않습니다.
**스토리지 vMotion** | 지원됨
**팀식 NIC** | 지원되지 않습니다.
**IPv6** | 지원되지 않습니다.



## <a name="replication-appliance-requirements"></a>복제 어플라이언스 요구 사항

실제 서버에서 복제 어플라이언스를 수동으로 설정한 경우 테이블에 요약된 요구 사항을 준수하는지 확인합니다. Azure 마이그레이션 허브에서 제공 하는 OVA 템플릿을 사용 하 여 VM웨어 VM으로 Azure 마이그레이션 복제 어플라이언스를 설정 하는 경우 어플라이언스는 Windows Server 2016으로 설정 되 고 지원 요구 사항을 준수 합니다. 

- 복제 [어플라이언스 요구 사항에](migrate-replication-appliance.md#appliance-requirements)대해 자세히 알아봅니다.
- MySQL은 어플라이언스에 설치해야 합니다. 설치 [옵션에](migrate-replication-appliance.md#mysql-installation)대해 자세히 알아봅니다.
- 복제 어플라이언스에 액세스해야 하는 [URL에](migrate-replication-appliance.md#url-access) 대해 알아봅니다.

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure에 복제된 모든 온-프레미스 VM은 이 테이블에 요약된 Azure VM 요구 사항을 충족해야 합니다. 사이트 복구가 복제에 대한 필수 구성 조건 검사를 실행하면 일부 요구 사항이 충족되지 않으면 검사가 실패합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 지원되는 운영 체제를 확인합니다.<br/> 지원되는 운영 체제에서 실행 중인 모든 워크로드를 마이그레이션할 수 있습니다. | 지원되지 않는 경우 확인이 실패합니다.
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

실제 [서버를 마이그레이션합니다.](tutorial-migrate-physical-virtual-machines.md)
