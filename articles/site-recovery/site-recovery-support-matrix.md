---
title: "Azure Site Recovery 지원 매트릭스 | Microsoft Docs"
description: "Azure 사이트 복구에 대한 지원되는 운영 체제 및 구성 요소를 요약해서 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b9d269a780e9a4c61263208f26f440b1121c682
ms.openlocfilehash: f437598b612a145c5dd8b46a1ba340d298a76981
ms.lasthandoff: 03/01/2017


---
# <a name="azure-site-recovery-support-matrix"></a>Azure 사이트 복구 지원 매트릭스

이 문서에서는 Azure Site Recovery에 대한 지원되는 운영 체제 및 구성 요소를 요약해서 설명합니다. 지원되는 구성 요소 및 필수 구성 요소 목록을 해당하는 배포 문서의 각 배포 시나리오에 사용할 수 있지만 이 문서에서는 요약해서 설명합니다.

## <a name="support-for-azure-replication-scenarios"></a>Azure 복제 시나리오에 대한 지원

**배포웹사이트를** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
**Azure 포털** | Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 온-프레미스 VMware VM을 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다. | Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 VMM 클라우드 외의 온-프레미스 Hyper-V VM을 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다. | Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 MM 클라우드의 온-프레미스 Hyper-V VM을 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다.
**클래식 포털** | 유지 관리 모드에만 해당됩니다. 새 자격 증명 모음은 만들 수 없습니다. | 유지 관리 모드에만 해당됩니다. | 유지 관리 모드에만 해당됩니다.
**PowerShell** | 현재 지원되지 않습니다. | 지원됨 | 지원됨


## <a name="support-for-secondary-site-replication-scenarios"></a>보조 사이트 복제 시나리오에 대한 지원

**배포웹사이트를** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
**Azure 포털** | 보조 VMware 사이트에 온-프레미스 VMware VM을 복제합니다.<br/><br/> 도움말 가이드인 InMage Scout 사용자 가이드(http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)를 다운로드합니다. Azure 포털에서 사용할 수 없음 | 지원되지 않음 | VMM 클라우드의 온-프레미스 Hyper-V VM을 보조 VMM 클라우드에 복제<br/><br/> 표준 Hyper-V 복제만 해당, SAN은 지원되지 않음
**클래식 포털** | 유지 관리 모드에만 해당됩니다. 새 자격 증명 모음은 만들 수 없습니다. | 유지 관리 모드에만 해당됩니다. | 유지 관리 모드에만 해당됩니다.
**PowerShell** | 지원되지 않습니다. | 해당 없음 | 지원됨



## <a name="support-for-virtualization-server-operating-systems"></a>가상화 서버 운영 체제 지원

### <a name="host-servers-replicate-to-azure"></a>호스트 서버(Azure로 복제)

**VMware VM/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | ---
vCenter 5.5 또는 6.0(5.5 기능만 지원)  <br/><br/> 최신 업데이트가 있는 vSphere 6.0, 5.5 또는 5.1 | Windows Server 2016, 최신 업데이트가 포함된 Windows Server 2012 R2<br/><br/> Windows Server 2016 및 2012 R2를 실행하는 호스트가 혼합된 Hyper-V 사이트는 현재 지원되지 않습니다. | Windows Server 2016, 최신 업데이트가 포함된 Windows Server 2012 R2<br/><br/> Windows Server 2016 호스트는 System Center 2016을 실행하는 VMM에서 관리되어야 합니다.<br/><br/> Windows Server 2016 및 2012 R2 호스트가 혼합된 VMM 2016 클라우드는 현재 지원되지 않습니다.

### <a name="host-servers-replicate-to-secondary-site"></a>호스트 서버(보조 사이트에 복제)

**VMware VM/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
vCenter 5.5 또는 6.0(5.5 기능만 지원)  <br/><br/> 최신 업데이트가 있는 vSphere 6.0, 5.5 또는 5.1 | 최신 업데이트가 포함된 Windows Server 2012, Windows Server 2016, Windows Server 2012 R2<br/><br/> Windows Server 2016 호스트는 System Center 2016을 실행하는 VMM에서 관리되어야 합니다.<br/><br/> Windows Server 2016 및 이전 호스트가 혼합된 VMM 2016 클라우드는 현재 지원되지 않습니다.


## <a name="support-for-replicated-machines"></a>복제된 컴퓨터에 대한 지원

### <a name="machines-replicate-to-azure"></a>컴퓨터(Azure에 복제)

가상 컴퓨터는 [Azure 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 충족해야 합니다.

**요구 사항** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
복제되는 대상 | Windows 또는 Linux VM의 모든 워크로드 | 모든 워크로드 | 모든 워크로드
호스트 OS | 64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 필수 저장소: 파일 시스템(EXT3, ETX4, ReiserFS, XFS), 소프트웨어 장치 매퍼(다중 경로), 볼륨 관리자(LVM2). HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다. ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다. | [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868.aspx) 모든 게스트 OS | [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868.aspx) 모든 게스트 OS


### <a name="machines-replicate-to-secondary-site"></a>컴퓨터(보조 사이트에 복제)

**요구 사항** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
복제되는 대상 | Windows 또는 Linux VM의 모든 워크로드 | 모든 워크로드 | 모든 워크로드
호스트 OS | 64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 필수 저장소: 파일 시스템(EXT3, ETX4, ReiserFS, XFS), 소프트웨어 장치 매퍼(다중 경로), 볼륨 관리자(LVM2).<br/><br/> HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다. ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다. | Hyper-V에서 지원되는 모든 게스트 OS](https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>공급자 및 에이전트에 대한 지원

**Name** | **설명** | **최신 버전** | **세부 정보**
--- | --- | --- | --- | ---
**Azure Site Recovery 공급자** | 온-프레미스 서버와 Azure/보조 사이트 간 통신 조정  <br/><br/> 온-프레미스 VMM 서버에 설치, VMM 서버가 없는 경우 Hyper-V 서버에 설치 | 5.1.1700(포털에서 사용 가능) | [최신 기능 및 수정](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 통합 설치(VMware-Azure)** | 온-프레미스 VMware 서버와 Azure 간 통신 조정  <br/><br/> 온-프레미스 VMware 서버에 설치 | 9.3.4246.1(포털에서 사용 가능) | [최신 기능 및 수정](https://support.microsoft.com/kb/3155002)
**모바일 서비스** | 온-프레미스 VMware 서버/물리적 서버 및 Azure/보조 사이트 간 복제 조정<br/><br/> 복제하려는 VMware VM 또는 물리적 서버에 설치  | NA(포털에서 사용 가능) | 을 참조하세요.
**MARS(Microsoft Azure Recovery Services) 에이전트** | Hyper-V VM과 Azure 간 복제 조정<br/><br/> 온-프레미스 Hyper-V 서버에 설치(VMM 서버 존재 여부는 관계 없음) | |

## <a name="support-for-networking"></a>네트워킹에 대한 지원

### <a name="networking-replicate-to-azure"></a>네트워킹(Azure에 복제)

**호스트 네트워킹** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
NIC 팀 | 예<br/><br/>물리적 컴퓨터에서 지원되지 않음| 예 | 예
VLAN | 예 | 예 | 예
IPv4 | 예 | 예 | 예
IPv6 | 아니요 | 아니요 | 아니요

**게스트 VM 네트워킹** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
NIC 팀 | 아니요 | 아니요 | 아니요
IPv4 | 예 | 예 | 예
IPv6 | 아니요 | 아니요 | 아니요
고정 IP(Windows) | 예 | 예 | 예
고정 IP(Linux) | 아니요 | 아니요 | 아니요
다중 NIC | 예 | 예 | 예

**Azure 네트워킹** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
Express 경로 | 예 | 예 | 예
ILB | 예 | 예 | 예
ELB | 예 |  |
트래픽 관리자 | 예 | 예 | 예
다중 NIC | 예 | 예 | 예
예약된 IP | 예 | 예 | 예
IPv4 | 예 | 예 | 예
원본 IP 유지 | 예 | 예 | 예

### <a name="networking-replicate-to-secondary-site"></a>네트워킹(보조 사이트에 복제)

**호스트 네트워킹** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
NIC 팀 | 예 | 예
VLAN | 예 | 예
IPv4 | 예 | 예
IPv6 | 아니요 | 아니요

**게스트 VM 네트워킹** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
NIC 팀 | 아니요 | 아니요
IPv4 | 예 | 예
IPv6 | 아니요 | 아니요
고정 IP(Windows) | 예 | 예
고정 IP(Linux) | 예 | 예
다중 NIC | 예 | 예


## <a name="support-for-storage"></a>저장소에 대한 지원

### <a name="storage-replicate-to-azure"></a>저장소(Azure에 복제)

**저장소(호스트)** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
NFS | VMware의 경우 예<br/><br/> 물리적 서버의 경우 아니요 | 해당 없음 | 해당 없음
SMB 3.0 | 해당 없음 | 예 | 예
SAN(ISCSI) | 예 | 예 | 예
다중 경로(MPIO) | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음 | 예 | 예

**저장소(게스트 VM/물리적 서버)** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
VMDK | 예 | 해당 없음 | 해당 없음
VHD/VHDX | 해당 없음 | 예 | 예
2세대 VM | 해당 없음 | 예 | 예
공유 클러스터 디스크 | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음 | 아니요 | 아니요
암호화된 디스크 | 아니요 | 아니요 | 아니요
EFI/UEFI| 아니요 | 예 | 예
NFS | 아니요 | 아니요 | 아니요
SMB 3.0 | 아니요 | 아니요 | 아니요
RDM | 예<br/><br/> 물리적 서버의 경우 해당 없음 | 해당 없음 | 해당 없음
디스크 > 1TB | 아니요 | 아니요 | 아니요
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM | 예 | 예 | 예
저장소 공간 | 아니요 | 예 | 예
디스크 핫 추가/제거 | 아니요 | 아니요 | 아니요
디스크 제외 | 예 | 예 | 예
다중 경로(MPIO) | 해당 없음 | 예 | 예

**Azure 저장소** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
LRS | 예 | 예 | 예
GRS(표준 저장소에만 해당) | 예 | 예 | 예
쿨 저장소 | 아니요 | 아니요 | 아니요
핫 저장소| 아니요 | 아니요 | 아니요
휴지 상태의 암호화 | 예 | 예 | 예
Premium Storage | 예 | 예 | 예
Import/Export 서비스 | 아니요 | 아니요 | 아니요


### <a name="storage-replicate-to-secondary-site"></a>저장소(보조 사이트에 복제)

**저장소(호스트)** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
NFS | 예 | 해당 없음
SMB 3.0 | 해당 없음 | 예
SAN(ISCSI) | 예 | 예
다중 경로(MPIO) | 예 | 예

**저장소(게스트 VM/물리적 서버)** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
VMDK | 예 | 해당 없음
VHD/VHDX | 해당 없음 | 예(최대 16개 디스크)
2세대 VM | 해당 없음 | 예
공유 클러스터 디스크 | 예  | 아니요
암호화된 디스크 | 아니요 | 아니요
UEFI| 아니요 | 해당 없음
NFS | 아니요 | 아니요
SMB 3.0 | 아니요 | 아니요
RDM | 예 | 해당 없음
디스크 > 1TB | 아니요 | 예
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM | 예 | 예
저장소 공간 | 아니요 | 예
디스크 핫 추가/제거 | 아니요 | 아니요
디스크 제외 | 아니요 | 아니요
다중 경로(MPIO) | 해당 없음 | 예

## <a name="support-for-recovery-services-vault-actions"></a>Recovery Services 자격 증명 모음 작업 지원

### <a name="vaults-replicate-to-azure"></a>자격 증명 모음(Azure에 복제)

**작업** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
리소스 그룹 간 자격 증명 모음 이동<br/><br/> 구독 내 및 구독 간 | 아니요 | 아니요 | 아니요
저장소 그룹 간 저장소, 네트워크, Azure VM 이동<br/><br/> 구독 내 및 구독 간 | 아니요 | 아니요 | 아니요

### <a name="vaults-replicate-to-secondary-site"></a>자격 증명 모음(보조 사이트에 복제)

**작업** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
리소스 그룹 간 자격 증명 모음 이동<br/><br/> 구독 내 및 구독 간 | 아니요 | 아니요
저장소 그룹 간 저장소, 네트워크, Azure VM 이동<br/><br/> 구독 내 및 구독 간 | 아니요 | 아니요


## <a name="support-for-azure-compute-replicate-to-azure"></a>Azure Compute에 대한 지원(Azure에 복제)

**Compute 기능** | **VMware/물리적 서버** | **Hyper-V(VMM 없음)** | **Hyper-V(VMM 포함)**
--- | --- | --- | ---
공유 디스크 게스트 클러스터 | 아니요 | 아니요 | 아니요
가용성 집합 | 아니요 | 아니요 | 아니요
HUB | 예 | 예 | 예

## <a name="support-for-azure-vms"></a>Azure VM에 대한 지원

Azure에서 지원하는 운영 체제를 실행하는 가상 컴퓨터와 물리적 서버를 복제하기 위해 Site Recovery를 배포할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다. 복제할 온-프레미스 VM이 Azure 요구 사항을 준수해야 합니다.

**기능** | **요구 사항** | **세부 정보**
--- | --- | ---
**Hyper-V 호스트** | Windows Server 2012 R2 이상을 실행 중이어야 합니다. | 운영 체제가 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**VMware 하이퍼바이저** | 지원되는 운영 체제 | [요구 사항 확인](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**게스트 운영 체제** | Hyper-V에서 Azure로 복제: Site Recovery에서는 [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)모든 운영 체제를 지원합니다. <br/><br/> VMware 및 물리적 서버 복제의 경우: Windows 및 Linux [필수 구성 요소](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**게스트 운영 체제 아키텍처** | 64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**운영 체제 디스크 크기** | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**운영 체제 디스크 수** | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**데이터 디스크 수** | **Azure에 VMware VM**을 복제하는 경우 64개 이하, **Azure에 Hyper-V VM**을 복제하는 경우 16개 이하 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**데이터 디스크 VHD 크기** | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**네트워크 어댑터** | 여러 어댑터가 지원됩니다. |
**고정 IP 주소** | 지원됨 | 주 가상 컴퓨터가 고정 IP 주소를 사용하는 경우 Azure에서 생성되는 가상 컴퓨터에 대해 고정 IP 주소를 지정할 수 있습니다.<br/><br/> **Hyper-V에서 실행되는 Linux VM**에 대한 고정 IP 주소는 지원되지 않습니다.
**iSCSI 디스크** | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**공유 VHD** | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**FC 디스크** | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**하드 디스크 형식** | VHD  <br/><br/> VHDX | 현재 Azure에서는 VHDX가 지원되지 않지만 Azure로 장애 조치하면 Site Recovery가  자동으로 VHDX를 VHD로 변환합니다. 온-프레미스에 장애 복구 시 가상 컴퓨터에서 계속해서 VHDX 형식을 사용합니다.
**Bitlocker** | 지원되지 않음 | 가상 컴퓨터를 보호하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
**VM 이름** | 1 자에서 63자 사이입니다. 문자, 숫자 및 하이픈으로 제한됩니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. | Site Recovery에서 가상 컴퓨터 속성 내 값 업데이트
**VM 유형** | 1세대<br/><br/> 2세대 - Windows | 기본 OS 디스크 형식이 있는 2세대 VM - VHDX로 포맷된 한 두 개의 데이터 볼륨을 포함하며 300GB 미만이 지원됩니다.<br/><br/>을 참조하세요. Linux 2세대 VM은 지원되지 않습니다. [자세히 알아보기](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |








## <a name="next-steps"></a>다음 단계
[필수 구성 요소](site-recovery-prereq.md) 확인

