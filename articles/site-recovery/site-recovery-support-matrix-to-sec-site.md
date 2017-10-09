
---
title: "Azure Site Recovery를 사용하여 보조 사이트에 복제하기 위한 지원 매트릭스 | Microsoft Docs"
description: "Azure 사이트 복구에 대한 지원되는 운영 체제 및 구성 요소를 요약해서 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/24/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 69c5d09b6608484210870e1a69c51b112b497810
ms.contentlocale: ko-kr
ms.lasthandoff: 09/26/2017

---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 보조 사이트에 복제하기 위한 지원 매트릭스

이 문서는 Azure Site Recovery를 사용하여 보조 온-프레미스 사이트에 복제하는 경우 지원 되는 사항을 요약하여 설명합니다.

## <a name="deployment-options"></a>배포 옵션

**배포웹사이트를** | **VMware/물리적 서버** | **Hyper-V(SCVMM 포함/제외)**
--- | --- | --- | ---
**Azure 포털** | 보조 VMware 사이트에 온-프레미스 VMware VM을 복제합니다.<br/><br/> [InMage Scout 사용자 가이드](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)를 다운로드합니다(Azure Portal에서 사용할 수 없음). | VMM 클라우드의 온-프레미스 Hyper-V VM을 보조 VMM 클라우드에 복제<br></br> VMM이 없으면 지원되지 않음  <br/><br/> 표준 Hyper-V 복제만 해당 SAN은 지원되지 않음
**클래식 포털** | 유지 관리 모드에만 해당됩니다. 새 자격 증명 모음은 만들 수 없습니다. | 유지 관리 모드에만 해당됩니다.<br></br> SCVMM이 없으면 지원되지 않음
**PowerShell** | 지원되지 않음 | 지원됨<br></br> SCVMM이 없으면 지원되지 않음

## <a name="on-premises-servers"></a>온-프레미스 서버

### <a name="virtualization-servers"></a>가상화 서버

**배포웹사이트를** | **지원**
--- | ---
**VMware VM/물리적 서버** | 최신 업데이트가 설치된 vSphere 6.0, 5.5 또는 5.1
**Hyper-V(VMM 포함)** | VMM 2016 및 VMM 2012 R2

  >[!Note]
  > Windows Server 2016 및 2012 R2 호스트가 혼합된 VMM 2016 클라우드는 현재 지원되지 않습니다.
  > 기존 SCVMM 2012 R2를 2016으로 업그레이드하는 구성은 현재 지원되지 않습니다.
### <a name="host-servers"></a>호스트 서버

**배포웹사이트를** | **지원**
--- | ---
**VMware VM/물리적 서버** | vCenter 5.5 또는 6.0(5.5 기능만 지원) 
**Hyper-V(VMM 없음)** | 보조 사이트로 복제하는 데 지원되는 구성이 아님
**Hyper-V(VMM 포함)** | Windows Server 2016 및 최신 업데이트가 포함된 Windows Server 2012 R2<br/><br/> Windows Server 2016 호스트는 VMM 2016에서 관리되어야 합니다.

## <a name="support-for-replicated-machine-os-versions"></a>복제된 컴퓨터 운영 체제 버전에 대한 지원
다음은 Azure Site Recovery를 사용하는 경우 다양한 배포 시나리오에서 지원되는 운영 체제를 요약한 테이블입니다. 이 지원은 언급된 OS에서 실행되는 모든 워크로드에 적용됩니다.

**VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4 또는 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Hyper-V에서 지원](https://technet.microsoft.com/library/mt126277.aspx)하는 모든 게스트 운영 체제

>[!Note]
>파일 시스템(EXT3, ETX4, ReiserFS, XFS), Multipath 소프트웨어 장치 매퍼, 볼륨 관리자(LVM2)등의 저장소가 포함된 Linux 컴퓨터만 복제할 수 있습니다.
>HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다.
>ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.

## <a name="network-configuration"></a>네트워크 구성

### <a name="hosts"></a>호스트

**구성** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
NIC 팀 | 예 | 예
VLAN | 예 | 예
IPv4 | 예 | 예
IPv6 | 아니요 | 아니요

### <a name="guest-vms"></a>게스트 VM

**구성** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
NIC 팀 | 아니요 | 아니요
IPv4 | 예 | 예
IPv6 | 아니요 | 아니요
고정 IP(Windows) | 예 | 예
고정 IP(Linux) | 예 | 예
다중 NIC | 예 | 예


## <a name="storage"></a>저장소

### <a name="host-storage"></a>호스트 저장소

**저장소(호스트)** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
NFS | 예 | 해당 없음
SMB 3.0 | 해당 없음 | 예
SAN(ISCSI) | 예 | 예
다중 경로(MPIO) | 예 | 예

### <a name="guest-or-physical-server-storage"></a>게스트 또는 물리적 서버 저장소

**구성** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
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
디스크 제외 | 아니요 | 예
다중 경로(MPIO) | 해당 없음 | 예

## <a name="vaults"></a>자격 증명 모음

**작업** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | --- | ---
리소스 그룹 간에 자격 증명 모음 이동(동일 구독 내 또는 구독 간에) | 아니요 | 아니요
리소스 그룹 간에 저장소, 네트워크, Azure VM 이동(동일 구독 내 또는 구독 간에) | 아니요 | 아니요

## <a name="provider-and-agent"></a>공급자 및 에이전트

**Name** | **설명** | **최신 버전** | **세부 정보**
--- | --- | --- | --- | ---
**Azure Site Recovery 공급자** | 온-프레미스 서버와 Azure 간 통신 조정 <br/><br/> 온-프레미스 VMM 서버에 설치, VMM 서버가 없는 경우 Hyper-V 서버에 설치 | 5.1.19([포털에서 사용 가능](http://aka.ms/downloaddra)) | [최신 기능 및 수정](https://support.microsoft.com/kb/3155002)
**모바일 서비스** | 온-프레미스 VMware 서버 또는 물리적 서버 및 보조 사이트 간 복제 조정<br/><br/> 복제하려는 VMware VM 또는 물리적 서버에 설치  | 해당 없음(포털에서 사용 가능) | 해당 없음


## <a name="next-steps"></a>다음 단계

- [VMM 클라우드의 Hyper-V VM에서 보조 사이트로 복제](site-recovery-vmm-to-vmm.md)
- [VMware VM 및 물리적 서버를 보조 사이트에 복제](site-recovery-vmware-to-vmware.md)

