---
title: Azure Site Recovery의 새로운 기능 | Microsoft Docs
description: Azure Site Recovery에 도입된 새 기능에 대한 요약 정보를 제공합니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211120"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery의 새로운 기능

[Azure Site Recovery](site-recovery-overview.md) 서비스는 지속적으로 업데이트되고 개선됩니다. 서비스를 최신 상태로 유지할 수 있도록 이 문서에서는 최신 릴리스, 새 기능 및 새 콘텐츠에 대한 정보를 제공합니다. 이 페이지는 정기적으로 업데이트됩니다.

Site Recovery 기능에 대한 [여러분의 의견](https://feedback.azure.com/forums/256299-site-recovery)은 언제든지 환영합니다.

## <a name="q1-2019"></a>2019년 1분기

### <a name="linux-support"></a>Linux 지원

[업데이트 롤업 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery)는 Site Recovery 에이전트 및 공급자에 대한 업데이트를 제공합니다. 이 업데이트는 다음과 같은 Linux 지원을 추가합니다.

- **Azure VM의 재해 복구**: RedHat Workstation 6/7, Ubuntu/Debian/SUSE를 위한 새 커널 버전.
- **VMware VM/물리적 서버를 Azure로 재해 복구**: RedHat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10/7.6 새 커널 버전 Ubuntu, Debian 및 SUSE.



## <a name="q4-2018"></a>2018년 4분기

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 재해 복구의 새 가격 책정 계산기

Azure VM 재해 복구에는 VM 라이선스 비용과 네트워크 및 스토리지 비용이 발생합니다. Azure는 이러한 비용을 계산할 수 있는 [가격 책정 계산기](https://aka.ms/a2a-cost-estimator)를 제공합니다. 이제 Site Recovery는 표준 HDD 디스크 12개와 프리미엄 SSD 디스크 6개가 포함된 VM을 사용하는 3계층 앱을 기반으로 샘플 배포의 가격을 정하는 [가격 견적 예제](https://aka.ms/a2a-cost-estimator)를 제공합니다. 샘플은 표준은 하루 10GB, 프리미엄은 20GB의 데이터가 변경되는 것으로 가정합니다. 특정 배포에 맞게 변수를 예상 비용으로 변경할 수 있습니다. VM 수, 관리 디스크의 수 및 유형, VM에서 예상되는 총 데이터 변경률을 지정할 수 있습니다. 또한 압축 비율을 적용하여 대역폭 비용을 예측할 수 있습니다. 공지를 [읽어보세요](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="support-for-azure-vms-in-zones"></a>영역의 Azure VM 지원

Azure 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 이제 Azure VM에 대한 복제를 설정하고, 단일 VM 인스턴스, 가용성 집합의 VM 또는 가용성 영역의 VM으로의 장애 조치(failover) 대상을 설정할 수 있습니다. 이 설정은 복제에 영향을 주지 않습니다. 공지를 [읽어보세요](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/).
 
### <a name="disaster-recovery-for-encrypted-vms"></a>암호화된 VM의 재해 복구

Site Recovery는 Azure AD 앱을 사용하여 ADE(Azure Disk Encryption)로 암호화된 Azure VM을 지원합니다. [자세히 알아보기](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>가속화된 네트워킹을 사용하여 VM 재해 복구

가속화된 네트워킹을 사용하면 VM에 대한 SR-IOV(단일 루트 I/O 가상화)를 구현하여 네트워킹 성능을 개선할 수 있습니다. Azure VM에 복제를 사용하도록 설정하면 Site Recovery는 가속화된 네트워킹의 사용 여부를 감지합니다. 사용되는 경우 장애 조치(failover) 후 Site Recovery는 자동으로 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 및 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)의 대상 복제본 Azure VM에서 가속화된 네트워킹을 구성합니다. [자세히 알아보기](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="automatic-updates-for-the-mobility-service-extension"></a>모바일 서비스 확장의 자동 업데이트

Site Recovery는 모바일 서비스 확장의 자동 업데이트 옵션을 추가했습니다. 모바일 서비스 확장은 Site Recovery에서 복제한 각 Azure VM에 설치됩니다. 복제를 사용하도록 설정할 때 Site Recovery에서 확장 업데이트를 관리하도록 허용할 것인지 여부를 선택합니다. 업데이트 후 VM을 다시 시작할 필요가 없으며 복제에 영향을 주지 않습니다. [자세히 알아보기](azure-to-azure-autoupdate.md).

### <a name="support-for-standard-ssd-disks"></a>표준 SSD 디스크 지원

Azure는 일관적인 성능이 필요하지만 디스크 IOPS가 높지 않은 웹 서버 같은 앱을 위한 비용 효율적인 스토리지 솔루션을 제공하기 위해 [표준 SSD(반도체 드라이브)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)를 도입했습니다. 프리미엄 SSD와 표준 HDD 디스크의 장점을 결합한 것입니다. Site Recovery는 표준 SSD 디스크를 사용하여 Azure VM에 대한 재해 복구를 제공합니다. 기본적으로 대상 Azure 지역으로 장애 조치(failover) 후에도 디스크 유형이 유지됩니다.

### <a name="support-for-azure-storage-firewall"></a>Azure 스토리지 방화벽 지원

계정에 대한 방화벽 규칙을 설정하여 Azure 스토리지 계정을 특정 네트워크 세트로 제한할 수 있습니다. 기본적으로 내부 네트워크 및 인터넷에서 들어오는 트래픽을 거부하도록 스토리지 계정을 구성한 다음, 특정 VNet에서 트래픽에 액세스하는 권한을 부여합니다. Site Recovery는 방화벽이 설정된 스토리지 계정에서 비관리형 디스크를 사용하는 VM을 보조 지역으로 복제하는 것을 지원합니다. 대상 Azure 지역의 비관리형 디스크에서, 방화벽이 설정된 스토리지 계정을 선택할 수 있습니다. 네트워크 액세스를 원본 VM이 있는 네트워크로 제한하여 캐시 스토리지 계정에 대한 액세스를 제한할 수도 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대한 [액세스를 허용](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)해야 합니다.

## <a name="q3-2018"></a>2018년 3분기 

### <a name="linux-support"></a>Linux 지원

#### <a name="update-rollup-28"></a>업데이트 롤업 28

[업데이트 롤업 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)은 Site Recovery 에이전트 및 공급자에 대한 업데이트를 제공합니다. 이 업데이트는 다음과 같은 Linux 지원을 추가합니다.

- **Azure VM의 재해 복구**: 이제 RedHat Enterprise Linux 6.10, CentOS 6.10, 레거시 BIOS 호환성 모드에서 GPT(GUID 파티션 테이블) 파티션 스타일을 사용하는 Linux 기반 VM이 지원됩니다.
- **VMware VM/물리적 서버를 Azure로 재해 복구**: 이제 RedHat Enterprise Linux 6.10, CentOS 6.10, 레거시 BIOS 호환성 모드에서 GPT(GUID 파티션 테이블) 파티션 스타일을 사용하는 Linux 기반 VM이 지원됩니다.

#### <a name="update-rollup-27"></a>업데이트 롤업 27

[업데이트 롤업 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)은 Site Recovery 에이전트 및 공급자에 대한 업데이트를 제공합니다. 이 업데이트는 다음과 같은 Linux 지원을 추가합니다.

- **Azure VM의 재해 복구**: Red Hat Enterprise Linux 7.5
- **VMware VM/물리적 서버를 Azure로 재해 복구**: SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Windows Server 2016에서 실행되는 Azure VM 지원

Windows Server 2016에서 실행되는 Azure VM은 Azure Site Recovery를 사용하여 Azure 지역에 복제할 수 있습니다.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>스토리지 공간 다이렉트를 실행하는 Azure VM 지원

[스토리지 공간 다이렉트](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)(Windows Server 2016 이상에서 사용 가능)는 드라이브를 스토리지 풀로 그룹화한 다음, 풀의 용량을 사용하여 스토리지 공간을 만듭니다. 스토리지 공간은 독립 실행형 VM, 각 클러스터 노드에서 로컬 스토리지를 사용하는 [Azure VM의 게스트 클러스터](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 또는 클러스터 전체의 공유 스토리지에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지에서 업데이트를 항상 최신 상태로 유지하세요.


