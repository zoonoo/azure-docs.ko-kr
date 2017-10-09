---
title: "Azure에 복제하기 위한 Azure Site Recovery 지원 매트릭스 | Microsoft Docs"
description: "Azure 사이트 복구에 대한 지원되는 운영 체제 및 구성 요소를 요약해서 설명합니다."
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajanaki
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 103643ace17961cd4e2e37313f75c775e3dcd8cd
ms.contentlocale: ko-kr
ms.lasthandoff: 09/26/2017

---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 복제하기 위한 Azure Site Recovery 지원 매트릭스


이 문서에서는 Azure로 복제 및 복구할 때 Azure Site Recovery에 대해 지원되는 구성 및 구성 요소를 요약하여 설명합니다. Azure Site Recovery 요구 사항에 대한 자세한 내용은 [필수 구성 요소](site-recovery-prereq.md)를 참조하세요.


## <a name="support-for-deployment-options"></a>배포 옵션에 대한 지원

**배포웹사이트를** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)** |
--- | --- | ---
**Azure 포털** | Azure Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 온-프레미스 VMware VM을 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다. | Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 온-프레미스 Hyper-V VM을 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다.
**클래식 포털** | 유지 관리 모드에만 해당됩니다. 새 자격 증명 모음은 만들 수 없습니다. | 유지 관리 모드에만 해당됩니다.
**PowerShell** | 현재 지원되지 않습니다. | 지원됨


## <a name="support-for-datacenter-management-servers"></a>데이터 센터 관리 서버에 대한 지원

### <a name="virtualization-management-entities"></a>가상화 관리 엔터티

**배포웹사이트를** | **지원**
--- | ---
**VMware VM/물리적 서버** | vCenter 6.5, 6.0 또는 5.5
**Hyper-V(Virtual Machine Manager 있음)** | System Center Virtual Machine Manager 2016 및 System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Windows Server 2016 및 2012 R2 호스트가 혼합된 System Center Virtual Machine Manager 2016 클라우드는 현재 지원되지 않습니다.
  > 기존 SCVMM 2012 R2를 2016으로 업그레이드하는 구성은 현재 지원되지 않습니다.
### <a name="host-servers"></a>호스트 서버

**배포웹사이트를** | **지원**
--- | ---
**VMware VM/물리적 서버** | vSphere 6.5, 6.0, 5.5
**Hyper-V(Virtual Machine Manager 있음/없음)** | Windows Server 2016, 최신 업데이트가 포함된 Windows Server 2012 R2<br></br>SCVMM을 사용하는 경우 Windows Server 2016 호스트는 SCVMM 2016을 통해 관리되어야 합니다.


  >[!Note]
  >Windows Server 2016 및 2012 R2를 실행하는 호스트가 혼합된 Hyper-V 사이트는 현재 지원되지 않습니다. Windows Server 2016 호스트에서 VM에 대한 대체 위치로 복구하는 방식은 현재 지원되지 않습니다.

## <a name="support-for-replicated-machine-os-versions"></a>복제된 컴퓨터 운영 체제 버전에 대한 지원

Azure에 복제하는 경우 보호되는 가상 컴퓨터가 [Azure 요구 사항](#failed-over-azure-vm-requirements)을 충족해야 합니다.
다음은 Azure Site Recovery를 사용하는 경우 다양한 배포 시나리오에서 지원되는 복제된 운영 체제를 요약한 테이블입니다. 이 지원은 언급된 OS에서 실행되는 모든 워크로드에 적용됩니다.

 **VMware/물리적 서버** | **Hyper-V(VMM 포함/제외)** |
--- | --- |
64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상<br/>*Windows Server 2016* - VMware 가상 컴퓨터 및 물리적 서버에서 현재 지원되지 않습니다. <br/><br/> Red Hat Enterprise Linux : 5.2 ~ 5.11, 6.1 ~ 6.9, 7.0 to 7.3 <br/><br/>CentOS : 5.2 ~ 5.11, 6.1 ~ 6.9, 7.0 ~ 7.3 <br/><br/>Ubuntu 14.04 LTS 서버[(지원되는 커널 버전)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS 서버[(지원되는 커널 버전)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>복제 컴퓨터를 SLES 11 SP3에서 SLES 11 SP4로 업그레이드하는 것은 지원되지 않습니다. 복제된 컴퓨터가 SLES 11SP3에서 SLES 11 SP4로 업그레이드된 경우 복제를 비활성화하고 업그레이드 후 컴퓨터를 다시 보호해야 합니다. | [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868.aspx) 모든 게스트 OS


>[!IMPORTANT]
>(Azure에 복제하는 VMware/물리적 서버에 적용 가능)
>
> Red Hat Enterprise Linux Server 7+ 및 CentOS 7+ 서버에서 커널 버전 3.10.0-514는 Azure Site Recovery 모바일 서비스 9.8 버전부터 지원됩니다.<br/><br/>
> 버전 9.8보다 낮은 모바일 서비스 버전을 사용하는 3.10.0-514 커널의 고객은 복제를 사용하지 않도록 설정하고 모바일 서비스 버전을 버전 9.8로 업데이트한 다음 복제를 다시 사용하도록 설정해야 합니다.


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>VMware/물리적 서버에 대해 지원되는 Ubuntu 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic에서 3.13.0-117-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic에서 3.13.0-121-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic에서 3.13.0-128-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-91-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic에서 4.4.0-81-generic<br/>4.8.0-34-generic에서 4.8.0-56-generic<br/>4.10.0-14-generic에서 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic에서 4.4.0-91-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-32-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Linux(VMware/물리적 서버)에서 지원되는 파일 시스템 및 게스트 저장소 구성

다음 파일 시스템 및 저장소 구성 소프트웨어는 VMware 또는 물리적 서버에서 실행되는 Linux 서버에서 지원됩니다.
* 파일 시스템: ext3, ext4, ReiserFS(Suse Linux Enterprise Server만), XFS
* 볼륨 관리자: LVM2
* 다중 경로 소프트웨어: 장치 매퍼

반가상화된 저장 장치(반가상화된 드라이버에서 내보낸 장치)는 지원되지 않습니다.<br/>
다중 큐 블록 IO 장치는 지원되지 않습니다.<br/>
HP CCISS 저장소 컨트롤러가 있는 물리적 서버는 지원되지 않습니다.<br/>

>[!Note]
> Linux 서버에서 /(root), /boot, /usr, /usr/local, /var, /etc 디렉터리(별도의 파티션/파일 시스템으로 설정된 경우)는 모두 원본 서버의 동일한 디스크(OS 디스크)에 있어야 합니다.<br/><br/>
> 버전 9.10의 모바일 서비스부터 메타데이터 체크섬과 같은 XFS 파일 시스템의 XFSv5 기능이 지원됩니다. XFSv5 기능을 사용하는 경우 9.10 이상 버전의 모바일 서비스를 실행하고 있어야 합니다. xfs_info 유틸리티를 사용하여 파티션에 대한 XFS 수퍼 블록을 확인할 수 있습니다. ftype이 1로 설정되면 XFSv5 기능이 사용됩니다.
>


## <a name="support-for-network-configuration"></a>네트워크 구성 지원
다음은 Azure Site Recovery를 사용하여 Azure에 복제하는 다양한 배포 시나리오에서 지원되는 네트워크 구성을 요약한 테이블입니다.

### <a name="host-network-configuration"></a>호스트 네트워크 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)**
--- | --- | ---
NIC 팀 | 예<br/><br/>물리적 컴퓨터가 복제되는 경우 지원되지 않음| 예
VLAN | 예 | 예
IPv4 | 예 | 예
IPv6 | 아니요 | 아니요

### <a name="guest-vm-network-configuration"></a>게스트 VM 네트워크 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)**
--- | --- | ---
NIC 팀 | 아니요 | 아니요
IPv4 | 예 | 예
IPv6 | 아니요 | 아니요
고정 IP(Windows) | 예 | 예
고정 IP(Linux) | 예 <br/><br/>가상 컴퓨터가 장애 복구(failback) 시 DHCP를 사용하도록 구성됨  | 아니요
다중 NIC | 예 | 예

### <a name="failed-over-azure-vm-network-configuration"></a>장애 조치(Failover)된 Azure VM 네트워크 구성

**Azure 네트워킹** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)**
--- | --- | ---
Express 경로 | 예 | 예
ILB | 예 | 예
ELB | 예 | 예
트래픽 관리자 | 예 | 예
다중 NIC | 예 | 예
예약된 IP | 예 | 예
IPv4 | 예 | 예
원본 IP 유지 | 예 | 예


## <a name="support-for-storage"></a>저장소에 대한 지원
다음은 Azure Site Recovery를 사용하여 Azure에 복제하는 다양한 배포 시나리오에서 지원되는 저장소 구성을 요약한 테이블입니다.

### <a name="host-storage-configuration"></a>호스트 저장소 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)**
--- | --- | --- | ---
NFS | VMware의 경우 예<br/><br/> 물리적 서버의 경우 아니요 | 해당 없음
SMB 3.0 | 해당 없음 | 예
SAN(ISCSI) | 예 | 예
다중 경로(MPIO)<br></br>테스트 제품: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON | 예 | 예

### <a name="guest-or-physical-server-storage-configuration"></a>게스트 또는 물리적 서버 저장소 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)**
--- | --- | ---
VMDK | 예 | 해당 없음
VHD/VHDX | 해당 없음 | 예
2세대 VM | 해당 없음 | 예
EFI/UEFI| 아니요 | 예
공유 클러스터 디스크 | 아니요 | 아니요
암호화된 디스크 | 아니요 | 아니요
NFS | 아니요 | 해당 없음
SMB 3.0 | 아니요 | 아니요
RDM | 예<br/><br/> 물리적 서버의 경우 해당 없음 | 해당 없음
디스크 > 1TB | 예<br/><br/>최대 4095GB | 예<br/><br/>최대 4095GB
4K 섹터 크기 디스크 | 예 | 예, 1세대 VM에 지원됨<br/><br/>2세대 VM에 지원되지 않음
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM 논리 볼륨 관리 | 예 | 예
저장소 공간 | 아니요 | 예
디스크 핫 추가/제거 | 아니요 | 아니요
디스크 제외 | 예 | 예
다중 경로(MPIO) | 해당 없음 | 예

**Azure 저장소** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)**
--- | --- | ---
LRS | 예 | 예
GRS | 예 | 예
RA-GRS | 예 | 예
쿨 저장소 | 아니요 | 아니요
핫 저장소| 아니요 | 아니요
휴지 상태의 암호화(SSE)| 예 | 예
Premium Storage | 예 | 예
Import/Export 서비스 | 아니요 | 아니요


## <a name="support-for-azure-compute-configuration"></a>Azure 계산 구성에 대한 지원

**Compute 기능** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 있음/없음)**
--- | --- | ---
가용성 집합 | 예 | 예
HUB | 예 | 예  
관리 디스크 | 예 | 예<br/><br/>관리되는 디스크를 사용한 Azure VM에서 온-프레미스로의 장애 복구(failback)는 현재 지원되지 않습니다.

## <a name="failed-over-azure-vm-requirements"></a>장애 조치(Failover)된 Azure VM 요구 사항

Azure에서 지원하는 운영 체제를 실행하는 가상 컴퓨터와 물리적 서버를 복제하기 위해 Site Recovery를 배포할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다. 복제하려는 온-프레미스 VM이 Azure에 복제하는 동안 아래의 Azure 요구 사항을 준수해야 합니다.

**엔터티** | **요구 사항** | **세부 정보**
--- | --- | ---
**게스트 운영 체제** | Hyper-V에서 Azure로 복제: Site Recovery에서는 [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)모든 운영 체제를 지원합니다. <br/><br/> VMware 및 물리적 서버 복제의 경우: Windows 및 Linux [필수 구성 요소](site-recovery-vmware-to-azure-classic.md) | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**게스트 운영 체제 아키텍처** | 64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**운영 체제 디스크 크기** | **VMware VM 또는 물리적 서버를 Azure**로 복제하는 경우 최대 2048GB<br/><br/>**Hyper-V 1세대** VM에 대해 최대 2048GB<br/><br/>**Hyper-V 2세대** VM에 대해 최대 300GB  | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**운영 체제 디스크 수** | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**데이터 디스크 수** | **Azure에 VMware VM**을 복제하는 경우 64개 이하, **Azure에 Hyper-V VM**을 복제하는 경우 16개 이하 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**데이터 디스크 VHD 크기** | 최대 4095GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**네트워크 어댑터** | 여러 어댑터가 지원됩니다. |
**공유 VHD** | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**FC 디스크** | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**하드 디스크 형식** | VHD  <br/><br/> VHDX | 현재 Azure에서는 VHDX가 지원되지 않지만 Azure로 장애 조치하면 Site Recovery가  자동으로 VHDX를 VHD로 변환합니다. 온-프레미스에 장애 복구 시 가상 컴퓨터에서 계속해서 VHDX 형식을 사용합니다.
**Bitlocker** | 지원되지 않음 | 가상 컴퓨터를 보호하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
**VM 이름** | 1 자에서 63자 사이입니다. 문자, 숫자 및 하이픈으로 제한됩니다. VM 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. | Site Recovery에서 가상 컴퓨터 속성의 값을 업데이트합니다.
**VM 유형** | 1세대<br/><br/> 2세대 -- Windows | 기본 OS 디스크 형식이 있는 2세대 VM(VHDX로 포맷된 한 개 또는 두 개의 데이터 볼륨을 포함) 및 300GB 미만의 디스크 공간이 지원됩니다.<br></br>Linux 2세대 VM은 지원되지 않습니다. [자세히 알아보기](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Recovery Services 자격 증명 모음 작업 지원

**작업** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
리소스 그룹 간 자격 증명 모음 이동<br/><br/> 구독 내 및 구독 간 | 아니요 | 아니요 | 아니요
저장소 그룹 간 저장소, 네트워크, Azure VM 이동<br/><br/> 구독 내 및 구독 간 | 아니요 | 아니요 | 아니요


## <a name="support-for-provider-and-agent"></a>공급자 및 에이전트에 대한 지원

**Name** | **설명** | **최신 버전** | **세부 정보**
--- | --- | --- | --- | ---
**Azure Site Recovery 공급자** | 온-프레미스 서버와 Azure 간 통신 조정 <br/><br/> Virtual Machine Manager 서버가 없는 경우 온-프레미스 Virtual Machine Manager 서버 또는 Hyper-V 서버에 설치 | 5.1.19([포털에서 사용 가능](http://aka.ms/downloaddra)) | [최신 기능 및 수정](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery 통합 설치(VMware-Azure)** | 온-프레미스 VMware 서버와 Azure 간 통신 조정  <br/><br/> 온-프레미스 VMware 서버에 설치 | 9.3.4246.1(포털에서 사용 가능) | [최신 기능 및 수정](https://support.microsoft.com/kb/3155002)
**모바일 서비스** | 온-프레미스 VMware 서버/물리적 서버 및 Azure/보조 사이트 간 복제 조정<br/><br/> 복제하려는 VMware VM 또는 물리적 서버에 설치  | 해당 없음(포털에서 사용 가능) | 해당 없음
**MARS(Microsoft Azure Recovery Services) 에이전트** | Hyper-V VM과 Azure 간 복제 조정<br/><br/> 온-프레미스 Hyper-V 서버에 설치(Virtual Machine Manager 서버 존재 여부는 관계 없음) | 최신 에이전트([포털에서 제공](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>다음 단계
[필수 구성 요소 확인](site-recovery-prereq.md)

