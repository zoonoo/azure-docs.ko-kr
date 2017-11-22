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
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: da120d8e325867eaf9eb8b9be1ae8d9152db54c4
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Azure Site Recovery를 사용하여 보조 사이트에 복제하기 위한 지원 매트릭스

이 문서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 보조 온-프레미스 사이트에 복제하는 경우 지원 되는 사항을 요약하여 설명합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

**배포웹사이트를** | **세부 정보** 
--- | ---
**VMware 간** | 보조 VMware 사이트에 대한 온-프레미스 VMware VM의 재해 복구<br/><br/> [InMage Scout 사용자 가이드](https://aka.ms/asr-scout-user-guide)를 다운로드합니다.
**Hyper-V 간** | 보조 VMM 클라우드에 대한 VMM 클라우드의 온-프레미스 Hyper-V VM 재해 복구<br></br> VMM이 없으면 지원되지 않음



  

## <a name="host-servers"></a>호스트 서버

**배포웹사이트를** | **지원**
--- | ---
**VMware VM/물리적 서버** | vCenter 5.5, 6.0 및 6.5(5.5 기능만 지원)
**Hyper-V(VMM 포함)** | Windows Server 2016 및 최신 업데이트가 포함된 Windows Server 2012 R2<br/><br/> Windows Server 2016 호스트는 VMM 2016에서 관리되어야 합니다.<br/><br/> Windows Server 2016 및 2012 R2 호스트가 혼합된 VMM 2016 클라우드는 현재 지원되지 않습니다.<br/><br/> 기존 VMM 2012 R2를 System Center 2016으로 업그레이드하도록 포함하는 배포는 현재 지원되지 않습니다.


## <a name="support-for-replicated-machine-os-versions"></a>복제된 컴퓨터 운영 체제 버전에 대한 지원

다음 표에서는 Site Recovery를 사용하여 복제된 컴퓨터에 대한 운영 체제 지원을 요약합니다. 워크로드를 지원되는 운영 체제에서 실행할 수 있습니다.

**VMware/물리적 서버** | **Hyper-V(VMM 포함)**
--- | ---
64비트 Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5, 6.8 <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4  | [Hyper-V에서 지원](https://technet.microsoft.com/library/mt126277.aspx)하는 모든 게스트 운영 체제

## <a name="linux-machine-storage"></a>Linux 컴퓨터 저장소

다음 저장소를 포함한 Linux 컴퓨터만을 복제할 수 있습니다.

- 파일 시스템(EXT3, ETX4, ReiserFS, XFS)
- 다중 경로 소프트웨어 장치 매퍼
- 볼륨 관리자(LVM2)
- HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다.
- ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.

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

**Storage(호스트)** | **VMware/물리적 서버** | **Hyper-V(VMM 포함)**
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
UEFI| 예 | 해당 없음
NFS | 아니요 | 아니요
SMB 3.0 | 아니요 | 아니요
RDM | 예 | 해당 없음
디스크 > 1TB | 예 | 예
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM | 예 | 예
저장소 공간 | 아니요 | 예
디스크 핫 추가/제거 | 예 | 아니요
디스크 제외 | 예 | 예
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

- [VMM 클라우드의 Hyper-V VM에서 보조 사이트로 복제](tutorial-vmm-to-vmm.md)
- [VMware VM 및 물리적 서버를 보조 사이트에 복제](tutorial-vmware-to-vmware.md)
