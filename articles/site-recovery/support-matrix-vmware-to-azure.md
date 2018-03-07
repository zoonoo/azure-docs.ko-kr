---
title: "VMware VM 및 물리적 서버를 Azure로 복제하기 위한 Azure Site Recovery 지원 매트릭스 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 VMware VM을 Azure로 복제하도록 지원되는 운영 체제 및 구성 요소가 요약되어 있습니다."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: raynew
ms.openlocfilehash: 31754cd765c90b9e36d16dc766b0a3546e6fd93e
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Azure에 VMware 및 물리적 서버 복제를 위한 지원 매트릭스


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM을 Azure로 재해 복구하는 데 지원되는 구성 요소와 설정의 요약 정보를 제공합니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

**시나리오** | **세부 정보**
--- | ---
**VMware VM** | Azure에 온-프레미스 VMware VM 재해 복구를 수행할 수 있습니다. Azure Portal에서 또는 PowerShell을 사용하여 이 시나리오를 배포할 수 있습니다.
**물리적 서버** | Azure에 온-프레미스 Windows/Linux 물리적 서버 재해 복구를 수행할 수 있습니다. Azure Portal에서 이 시나리오를 배포할 수 있습니다.

## <a name="on-premises-virtualization-servers"></a>온-프레미스 가상화 서버

**서버** | **요구 사항** | **세부 정보**
--- | --- | ---
**VMware** | vCenter Server 6.5, 6.0 또는 5.5 또는 vSphere 6.5, 6.0 또는 5.5 | vCenter Server를 사용하는 것이 좋습니다.
**물리적 서버** | 해당 없음


## <a name="replicated-machines"></a>복제된 컴퓨터

다음 테이블에는 컴퓨터에 대한 복제 지원이 요약되어 있습니다. Site Recovery는 지원되는 운영 체제가 있는 컴퓨터에서 실행되는 모든 워크로드의 복제를 지원합니다.

**구성 요소** | **세부 정보**
--- | ---
컴퓨터 구성 | Azure로 복제하는 컴퓨터는 [Azure 요구 사항](#failed-over-azure-vm-requirements)을 충족해야 합니다.
컴퓨터 운영 체제(Windows) | 64비트 Windows Server 2016(Server Core, 데스크톱 환경 포함 서버), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상
컴퓨터 운영 체제(Linux) | Red Hat Enterprise Linux: 5.2 ~ 5.11, 6.1 ~ 6.9, 7.0 ~ 7.4 <br/><br/>CentOS: 5.2 ~ 5.11, 6.1 ~ 6.9, 7.0 ~ 7.4 <br/><br/>Ubuntu 14.04 LTS 서버[(지원되는 커널 버전)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS 서버[(지원되는 커널 버전)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>복제 컴퓨터를 SLES 11 SP3에서 SLES 11 SP4로 업그레이드하는 것은 지원되지 않습니다. 복제된 컴퓨터가 SLES 11SP3에서 SLES 11 SP4로 업그레이드된 경우 복제를 비활성화하고 업그레이드 후 컴퓨터를 다시 보호해야 합니다.

>[!NOTE]
>
> - Linux 배포판에서는 배포판의 부 버전 릴리스/업데이트에 포함된 스톡 커널만 지원됩니다.
>
> - Site Recovery 보호 VMware 가상 머신 또는 물리적 서버에 있는 Linux 배포판의 주 버전 업그레이드는 지원되지 않습니다. 주 버전에서 운영 체제를 업그레이드하는 동안(예: CentOS 6.\*에서 CentOS 7.\*로), 컴퓨터에 복제를 사용하지 않도록 설정하고, 컴퓨터에서 운영 체제를 업그레이드한 다음, 복제를 다시 사용하도록 설정합니다.
>

### <a name="ubuntu-kernel-versions"></a>Ubuntu 커널 버전


**지원되는 릴리스** | **Azure Site Recovery Mobility Service 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic에서 3.13.0-121-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic에서 3.13.0-128-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic에서 3.13.0-132-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic에서 3.13.0-137-generic,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic에서 4.4.0-81-generic<br/>4.8.0-34-generic에서 4.8.0-56-generic<br/>4.10.0-14-generic에서 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic에서 4.4.0-91-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic에서 4.4.0-96-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic에서 4.4.0-104-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Linux 파일 시스템/게스트 저장소 구성

**구성 요소** | **지원됨**
--- | ---
파일 시스템 | ext3, ext4, ReiserFS(Suse Linux Enterprise Server만), XFS
볼륨 관리자 | LVM2
다중 경로 소프트웨어 | 장치 매퍼
반가상화 저장 장치 | 반가상화 드라이버에서 내보낸 장치는 지원되지 않습니다.
다중 큐 블록 IO 장치 | 지원되지 않습니다.
HP CCISS 저장소 컨트롤러가 있는 물리적 서버 | 지원되지 않습니다.
디렉터리 | /(root), /boot, /usr, /usr/local, /var, /etc 디렉터리(별도의 파티션/파일 시스템으로 설정된 경우)는 원본 서버의 동일한 OS 디스크에 모두 있어야 합니다. </br></br> / (루트) 볼륨이 LVM 볼륨인 경우에는 LVM 볼륨이 아니라 동일한 디스크에 있는 별도의 파티션에 /boot가 있어야 합니다.<br/><br/>
XFSv5 | 버전 9.10의 Mobility Service 이상부터 메타데이터 체크섬과 같은 XFS 파일 시스템의 XFSv5 기능이 지원됩니다. xfs_info 유틸리티를 사용하여 파티션에 대한 XFS 수퍼 블록을 확인합니다. ftype이 1로 설정되면 XFSv5 기능이 사용 중입니다.



## <a name="network"></a>네트워크

**구성 요소** | **지원됨**
--- | ---
호스트 네트워크 NIC 팀 | VMware VM에서 지원됩니다. <br/><br/>물리적 컴퓨터 복제에 지원되지 않습니다.
호스트 네트워크 VLAN | 예
호스트 네트워크 IPv4 | 예
호스트 네트워크 IPv6 | 아니요
게스트/서버 네트워크 NIC 팀 | 아니요
게스트/서버 네트워크 IPv4 | 예
게스트/서버 네트워크 IPv6 | 아니오
게스트/서버 네트워크 정적 IP(Windows) | 예
게스트/서버 네트워크 정적 IP(Linux) | 예 <br/><br/>VM이 장애 복구(Failback) 시 DHCP를 사용하도록 구성되어 있습니다.  
게스트/서버 네트워크 다중 NIC | 예


## <a name="azure-vm-network-after-failover"></a>Azure VM 네트워크(장애 조치(failover) 후)

**구성 요소** | **지원됨**
--- | ---
Azure ExpressRoute | 예
ILB | 예
ELB | 예
Azure Traffic Manager | 예
다중 NIC | 예
예약된 IP 주소 | 예
IPv4 | 예
원본 IP 주소 유지 | 예
Azure Virtual Network 서비스 끝점<br/><br/> (Azure Storage 방화벽 및 Virtual Networks) | 아니요

## <a name="storage"></a>Storage
**구성 요소** | **지원됨**
--- | ---
호스트 NFS | VMware의 경우 예<br/><br/> 물리적 서버의 경우 아니요
호스트 SAN(ISCSI) | 예
호스트 다중 경로(MPIO) | 예. 테스트 제품: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
게스트/서버 VMDK | 예
게스트/서버 EFI/UEFI| 부분(Windows Server용 Azure 2012 이상 VMware 가상 머신에만 해당) </br></br> 표 끝에 있는 메모를 참조하세요.
게스트/서버 공유 클러스터 디스크 | 아니요
게스트/서버 암호화된 디스크 | 아니요
게스트/서버 NFS | 아니요
게스트/서버 SMB 3.0 | 아니요
게스트/서버 RDM | 예<br/><br/> 물리적 서버의 경우 해당 없음
게스트/서버 디스크 > 1 TB | 예<br/><br/>최대 4,095GB
4K 논리적 및 4k 물리적 섹터 크기 포함 게스트/서버 디스크 | 예
4K 논리적 및 512바이트 물리적 섹터 크기 포함 게스트/서버 디스크 | 예
게스트/서버 스트라이프 디스크 포함 볼륨 > 4TB <br><br/>LVM 논리 볼륨 관리 | 예
게스트/서버 - 저장소 공간 | 아니요
게스트/서버 디스크 핫 추가/제거 | 아니요
게스트/서버 - 디스크 제외 | 예
게스트/서버 다중 경로(MPIO) | 해당 없음

> [!NOTE]
> Windows Server 2012 이상을 실행하는 UEFI 부팅 VMware 가상 머신을 Azure로 마이그레이션할 수 있습니다. 다음 제한 사항이 적용됩니다.

> - Azure로 마이그레이션만 지원됩니다. 온-프레미스 VMware 사이트에 장애 복구는 지원되지 않습니다.
> - 서버는 OS 디스크에 4개 이상의 파티션을 가질 수 없습니다.
> - Mobility Service 버전 9.13 이상이 필요합니다.
> - 실제 서버에 대해서는 지원되지 않습니다.

## <a name="azure-storage"></a>Azure Storage

**구성 요소** | **지원됨**
--- | ---
LRS | 예
GRS | 예
RA-GRS | 예
쿨 저장소 | 아니요
핫 저장소| 아니오
블록 Blob | 아니요
미사용 암호화(SSE)| 예
Premium Storage | 예
Import/Export 서비스 | 아니요
Virtual Network 서비스 엔드포인트<br/><br/> 저장소 방화벽 및 대상 저장소/캐시 저장소 계정에 구성된 Virtual Network(복제 데이터 저장에 사용) | 아니오
범용 v2 저장소 계정(핫 및 쿨 계층 모두) | 아니요

## <a name="azure-compute"></a>Azure Compute

**기능** | **지원됨**
--- | ---
가용성 집합 | 예
HUB | 예   
관리 디스크 | 예

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure로 복제하는 온-프레미스 VM은 이 표에 요약되어 있는 Azure VM 요구 사항을 충족해야 합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**게스트 운영 체제** | [지원되는 운영 체제](#replicated machines)를 확인하세요. | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**게스트 운영 체제 아키텍처** | 64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**운영 체제 디스크 크기** | 최대 2,048GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**운영 체제 디스크 수** | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**데이터 디스크 수** | *VMware VM을 Azure로* 복제하는 경우 이 개수가 64 이하입니다. *Hyper-V VM을 Azure로* 복제하는 경우 이 개수가 16 이하입니다. | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**데이터 디스크 VHD 크기** | 최대 4,095GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**네트워크 어댑터** | 여러 어댑터가 지원됩니다. |
**공유 VHD** | 지원되지 않습니다. | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**FC 디스크** | 지원되지 않습니다. | 지원되지 않는 경우 필수 구성 요소 확인이 실패함
**하드 디스크 형식** | VHD  <br/><br/> VHDX | 현재 Azure에서는 VHDX가 지원되지 않지만 Azure로 장애 조치하면 Site Recovery가  자동으로 VHDX를 VHD로 변환합니다. 온-프레미스에 장애 복구 시 가상 머신에서 계속해서 VHDX 형식을 사용합니다.
**BitLocker** | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다.
**VM 이름** | 1~63자 사이입니다.<br/><br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. | Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.
**VM 유형** | 1세대<br/><br/> 2세대--Windows | 기본 OS 디스크 형식이 있는 2세대 VM(VHDX로 포맷된 한 개 또는 두 개의 데이터 볼륨을 포함) 및 300GB 미만의 디스크 공간이 지원됩니다.<br></br>Linux 2세대 VM은 지원되지 않습니다. 자세한 내용은 [zure로의 재해 복구 개선](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)을 참조하세요.|

## <a name="vault-tasks"></a>자격 증명 모음 작업

**작업** | **지원됨**
--- | ---
리소스 그룹 간 자격 증명 모음 이동<br/><br/> 구독 내 및 구독 간 | 아니요
저장소 그룹 간 저장소, 네트워크, Azure VM 이동<br/><br/> 구독 내 및 구독 간 | 아니오


## <a name="mobility-service"></a>Mobility Service

**Name** | **설명** | **최신 버전** | **세부 정보**
--- | --- | --- | --- | ---
**Azure Site Recovery 통합 설치** | 온-프레미스 VMware 서버와 Azure 간 통신 조정  <br/><br/> 온-프레미스 VMware 서버에 설치 | 9.12.4653.1(포털에서 사용 가능) | [최신 기능 및 수정](https://aka.ms/latest_asr_updates)
**모바일 서비스** | 온-프레미스 VMware 서버/물리적 서버 및 Azure/보조 사이트 간 복제 조정<br/><br/> 복제하려는 VMware VM 또는 물리적 서버에 설치 | 9.12.4653.1(포털에서 사용 가능) | [최신 기능 및 수정](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>다음 단계
VMware VM의 재해 복구용으로 Azure를 준비하는 방법을 [알아봅니다](tutorial-prepare-azure.md).
