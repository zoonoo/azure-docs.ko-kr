---
title: Azure Site Recovery의 새로운 기능 | Microsoft Docs
description: Azure Site Recovery에 도입된 새 기능에 대한 요약 정보를 제공합니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 50e1cb95249f0108430e978ae3ffe23b6edc778d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418391"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery의 새로운 기능

[Azure Site Recovery](site-recovery-overview.md) 서비스는 지속적으로 업데이트되고 개선됩니다. 서비스를 최신 상태로 유지할 수 있도록 이 문서에서는 최신 릴리스, 새 기능 및 새 콘텐츠에 대한 정보를 제공합니다. 이 페이지는 정기적으로 업데이트됩니다.

Site Recovery 기능에 대한 [여러분의 의견](https://feedback.azure.com/forums/256299-site-recovery)은 언제든지 환영합니다.


## <a name="updates-march-2019"></a>업데이트 (2019 년 3 월)

### <a name="update-rollup-35"></a>업데이트 롤업 35

[업데이트 롤업 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 및 공급자 (롤업에 자세 함)에 대 한 업데이트
**문제 수정/개선 사항** | 다양 한 수정 및 개선 사항 (단계에서 설명한 대로 롤업)

#### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**관리되는 디스크** | 온-프레미스 VMware Vm 및 물리적 서버 복제를 Azure의 managed disks에 직접 되었습니다. 온-프레미스 데이터는 Azure에서 캐시 저장소 계정에 전송 되 고 대상 위치에서 관리 되는 디스크에 복구 지점이 생성 됩니다. 이렇게 하면 여러 대상 저장소 계정을 관리할 필요가 없습니다.
**구성 서버** | Site Recovery는 이제 여러 Nic 사용 하 여 구성 서버를 지원합니다. 자격 증명 모음에 구성 서버를 등록 하기 전에 구성 서버 VM에 추가 어댑터를 추가 해야 합니다. 나중에 추가 하는 경우 자격 증명 모음에 서버를 다시 등록 해야 합니다.


## <a name="updates-february-2019"></a>업데이트 (2019 년 2 월)

### <a name="update-rollup-34"></a>업데이트 롤업 34 

[업데이트 롤업 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.


### <a name="update-rollup-33"></a>업데이트 롤업 33 

[업데이트 롤업 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.


#### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구 
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**네트워크 매핑** | Azure VM 재해 복구를 위해 복제를 사용 하도록 설정 하면 모든 사용 가능한 대상 네트워크를 이제 사용할 수 있습니다. 
**표준 SSD** | 이제 다음을 사용 하 여 Azure Vm에 대 한 재해 복구를 설정할 수 있습니다 [표준 SSD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)합니다.
**저장소 공간 다이렉트** | 사용 하 여 Azure VM 앱에서 실행 중인 앱에 대 한 재해 복구를 설정할 수 있습니다 [저장소 공간 다이렉트](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 고가용성에 대 한 합니다.  저장소 공간 다이렉트 (s2d) Site Recovery와 함께 사용 하 여 Azure VM 워크 로드의 포괄적인 보호를 제공 합니다. S2D를 사용 하면 Azure에서 게스트 클러스터를 호스트할 수 있습니다. VM에 SAP ASCS 계층, SQL Server, 또는 스케일 아웃 파일 서버와 같은 중요 한 응용 프로그램을 호스트 하는 경우 특히 유용 합니다.


#### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/물리적 서버 재해 복구
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux BRTFS 파일 시스템** | Site Recovery는 이제 BRTFS 파일 시스템을 사용 하 여 VMware vm 복제를 지원합니다. 복제 하는 경우 지원 되지 않습니다.<br/><br/>-BTRFS 파일 시스템 하위 볼륨 복제를 사용 하도록 설정한 후 변경 됩니다.<br/><br/>-파일 시스템 여러 디스크에 걸쳐 분산 됩니다.<br/><br/>-BTRFS 파일 시스템에서 RAID를 지원 합니다.
**Windows Server 2019** | Windows Server 2019를 실행 하는 컴퓨터에 대 한 추가 지원 합니다.


## <a name="updates-january-2019"></a>업데이트 (2019 년 1 월)

### <a name="accelerated-networking-azure-vms"></a>가속된 네트워킹 (Azure Vm)

가속화된 네트워킹을 사용하면 VM에 대한 SR-IOV(단일 루트 I/O 가상화)를 구현하여 네트워킹 성능을 개선할 수 있습니다. Azure VM에 복제를 사용하도록 설정하면 Site Recovery는 가속화된 네트워킹의 사용 여부를 감지합니다. 사용되는 경우 장애 조치(failover) 후 Site Recovery는 자동으로 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 및 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)의 대상 복제본 Azure VM에서 가속화된 네트워킹을 구성합니다.

[자세히 알아보기](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>업데이트 롤업 32 

[업데이트 롤업 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.

#### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구

업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | Ubuntu, Debian, SUSE 한 RedHat 워크스테이션 6/7 및 새 커널 버전에 대 한 지원이 추가 되었습니다.
**저장소 공간 다이렉트** | Site Recovery는 저장소 공간 다이렉트 (s2d)를 사용 하 여 Azure Vm을 지원 합니다.

#### <a name="vmware-vmsphysical-servers-replication"></a>VMware v m/물리적 서버 복제 
**기능** | **세부 정보**
--- | ---
**Linux 지원** | Ubuntu, Debian, SUSE 한 Redhat Enterprise Linux 7.6, RedHat 워크스테이션 6/7, Oracle Linux 6.10/7.6, 및 새 커널 버전에 대 한 지원이 추가 되었습니다.


### <a name="update-rollup-31"></a>업데이트 롤업 31 

[업데이트 롤업 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.

#### <a name="vmware-vmsphysical-servers-replication"></a>VMware v m/물리적 서버 복제 
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | UEK5 커널 및 Oracle Linux 6.8 및 6.9/7.0에 대 한 지원이 추가 되었습니다.
**LVM** | LVM 및 LVM2 볼륨에 대 한 추가 지원 합니다.<br/><br/> LVM 볼륨에는 디스크 파티션에 /boot 디렉터리 이제 지원 됩니다.
**Directories** | 별도 파티션 또는 동일한 시스템 디스크에 없는 파일 시스템으로 설정 하는 이러한 디렉터리에 대 한 지원이 추가 되었습니다.<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | 동적 디스크에 대 한 추가 지원 합니다.
**장애 조치(Failover)** | Storvsc 및 vsbus 부팅 드라이버가 아닌 VMware Vm에 대 한 장애 조치 시간을 개선 합니다.
**UEFI 지원** | Azure Vm 부팅 유형이 UEFI를 지원 하지 않습니다. 이제 azure Site Recovery를 사용 하 여 UEFI 사용 하 여 온-프레미스 물리적 서버를 마이그레이션할 수 있습니다. Site Recovery는 마이그레이션을 시작 하기 전에 부팅 유형을 BIOS로 변환 하 여 서버를 마이그레이션합니다. Site Recovery 이전에이 변환을 vm만 지원 합니다. 지원은 이상 Windows Server 2012를 실행 중인 물리적 서버에서 사용할 수 있습니다.

#### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | 지원 되는 Oracle Linux 6.8 및 6.9/7.0; 추가한 한 UEK5 커널입니다.
**Linux BRTFS 파일 시스템** | Azure Vm에 대 한 지원.
**가용성 영역에서 azure Vm** | 가용성 영역에서 배포 된 Azure Vm에 대 한 다른 지역으로 복제를 사용할 수 있습니다. 이제 Azure VM에 대한 복제를 설정하고, 단일 VM 인스턴스, 가용성 집합의 VM 또는 가용성 영역의 VM으로의 장애 조치(failover) 대상을 설정할 수 있습니다. 이 설정은 복제에 영향을 주지 않습니다. 공지를 [읽어보세요](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/).
**방화벽 지원 저장소 (portal/PowerShell)** | 에 대 한 추가 지원이 [방화벽을 사용 하도록 설정 하는 저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-network-security)합니다.<br/><br/> 재해 복구에 대 한 다른 Azure 지역에 방화벽을 사용 하도록 설정 하는 저장소 계정에서 관리 되지 않는 디스크를 사용 하 여 Azure Vm을 복제할 수 있습니다.<br/><br/> 관리 되지 않는 디스크에 대 한 방화벽을 사용 하도록 설정 하는 저장소 계정을 대상 저장소 계정으로 사용할 수 있습니다.<br/><br/> 지원 되는 포털 및 PowerShell을 사용 합니다.

## <a name="updates-december-2018"></a>업데이트 (2018 년 12 월)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>모바일 서비스 (Azure Vm)에 대 한 자동 업데이트

Site Recovery는 모바일 서비스 확장의 자동 업데이트 옵션을 추가했습니다. 모바일 서비스 확장은 Site Recovery에서 복제한 각 Azure VM에 설치됩니다. 복제를 사용하도록 설정할 때 Site Recovery에서 확장 업데이트를 관리하도록 허용할 것인지 여부를 선택합니다.

업데이트 후 VM을 다시 시작할 필요가 없으며 복제에 영향을 주지 않습니다. [자세히 알아보기](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 재해 복구의 새 가격 책정 계산기

Azure Vm의 재해 복구 VM 라이선스 비용을 절감 하 고 네트워크 및 저장소 비용을 발생 시킵니다. Azure는 이러한 비용을 계산할 수 있는 [가격 책정 계산기](https://aka.ms/a2a-cost-estimator)를 제공합니다. 이제 Site Recovery는 표준 HDD 디스크 12개와 프리미엄 SSD 디스크 6개가 포함된 VM을 사용하는 3계층 앱을 기반으로 샘플 배포의 가격을 정하는 [가격 견적 예제](https://aka.ms/a2a-cost-estimator)를 제공합니다.

- 이 샘플에서는 일 표준 및 프리미엄 20GB에 10GB의 데이터를 변경할을 가정합니다.
- 특정 배포에 맞게 변수를 예상 비용으로 변경할 수 있습니다.
- VM 수, 관리 디스크의 수 및 유형, VM에서 예상되는 총 데이터 변경률을 지정할 수 있습니다.
- 또한 압축 비율을 적용하여 대역폭 비용을 예측할 수 있습니다.

공지를 [읽어보세요](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).


## <a name="updates-october-2018"></a>업데이트 (2018 년 10 월)

### <a name="update-rollup-30"></a>업데이트 롤업 30 

[업데이트 롤업 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.

#### <a name="azure-vm-disaster-recovery"></a>Azure VM 재해 복구
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**지역 지원** | Site Recovery 지원 오스트레일리아 중부 1 및 오스트레일리아 중부 2에 대 한 추가 합니다.
**디스크 암호화에 대 한 지원** | Azure AD 앱을 사용 하 여 Azure 디스크 암호화 (ADE)를 사용 하 여 암호화 하는 Azure Vm의 재해 복구에 대 한 추가 지원 합니다. [자세히 알아보기](azure-to-azure-how-to-enable-replication-ade-vms.md).
**디스크 제외** | 이제 Azure VM을 복제 하는 동안 초기화 되지 않은 디스크 자동으로 제외 됩니다.
**저장소 방화벽 사용 (PowerShell)** | 에 대 한 추가 지원이 [방화벽을 사용 하도록 설정 하는 저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-network-security)합니다.<br/><br/> 재해 복구에 대 한 다른 Azure 지역에 방화벽을 사용 하도록 설정 하는 저장소 계정에서 관리 되지 않는 디스크를 사용 하 여 Azure Vm을 복제할 수 있습니다.<br/><br/> 관리 되지 않는 디스크에 대 한 방화벽을 사용 하도록 설정 하는 저장소 계정을 대상 저장소 계정으로 사용할 수 있습니다.<br/><br/> PowerShell를 사용 하 여만 지원 합니다.


### <a name="update-rollup-29"></a>업데이트 롤업 29 

[업데이트 롤업 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.


## <a name="updates-august-2018"></a>업데이트 (2018 년 8 월)

### <a name="update-rollup-28"></a>업데이트 롤업 28 

[업데이트 롤업 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.

#### <a name="azure-vms-disaster-recovery"></a>Azure Vm 재해 복구 
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | RedHat Enterprise Linux 6.10;에 대 한 지원 추가 CentOS 6.10 합니다.<br/><br/>
**클라우드 지원** | Azure 독일 클라우드의 Vm에 대 한 재해 복구를 지원 합니다.
**구독 간 재해 복구** | 한 지역의 Azure Vm을 동일한 Azure Active Directory 테 넌 트 내에서 다른 구독에서 다른 지역으로 복제를 지원 합니다. [자세히 알아보기](https://aka.ms/cross-sub-blog).

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Vmware v M/물리적 서버 재해 복구 
업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | RedHat Enterprise Linux 6.10에 CentOS 6.10에 대 한 추가 지원 합니다.<br/><br/> Linux 기반 Vm GUID 파티션 테이블 (GPT) 파티션 스타일 레거시 BIOS 호환 모드에서 사용 하 여 이제 지원 됩니다. 검토 합니다 [Azure VM FAQ](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) 자세한 내용은 합니다. 
**마이그레이션 후 Vm에 대 한 재해 복구** | 지원은 Azure로 마이그레이션할 온-프레미스 VMware VM에 대 한 보조 지역으로 재해 복구 설정에 대 한 모바일을 제거 하지 않고도 서비스 VM에서 복제를 사용 하도록 설정 하기 전에 합니다.
**Windows Server 2008** | 마이그레이션에 대 한 지원에는 실행 중인 Windows Server 2008 R2 2008 64 비트 및 32 비트 컴퓨터입니다.<br/><br/> 마이그레이션에만 (복제 및 장애 조치)입니다. 장애 복구는 지원 되지 않습니다.

## <a name="updates-july-2018"></a>업데이트 (2018 년 7 월)

### <a name="update-rollup-27-july-2018"></a>업데이트 롤업 27 (2018 년 7 월)

[업데이트 롤업 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) 다음 업데이트를 제공 합니다.

**업데이트** | **세부 정보**
--- | ---
**공급자 및 에이전트** | Site Recovery 에이전트 공급자 (롤업에 자세 함)을 업데이트 합니다.
**문제 수정/개선 사항** | 수정 및 개선 사항 (자세히 설명한 것 처럼 롤업에) 수입니다.

#### <a name="azure-vms-disaster-recovery"></a>Azure Vm 재해 복구 

업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | Red Hat Enterprise Linux 7.5에 대 한 추가 지원 합니다.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Vmware v M/물리적 서버 재해 복구 

업데이트에 추가 된 새로운 기능입니다.

**기능** | **세부 정보**
--- | ---
**Linux 지원** | Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12에 대 한 추가 지원 합니다.



## <a name="next-steps"></a>다음 단계

[Azure 업데이트](https://azure.microsoft.com/updates/?product=site-recovery) 페이지에서 업데이트를 항상 최신 상태로 유지하세요.
