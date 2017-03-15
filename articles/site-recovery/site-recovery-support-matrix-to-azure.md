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
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 511e61552ec0a2c862ebd99fd881f87b56b69398
ms.openlocfilehash: 84eabb9c898d32efeb6e1c4b1f17b683940b4c04
ms.lasthandoff: 02/22/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Azure에 복제하기 위한 Azure Site Recovery 지원 매트릭스

> [!div class="op_single_selector"]
> * [Azure에 복제](site-recovery-support-matrix-to-azure.md)
> * [고객 소유의 보조 사이트에 복제](site-recovery-support-matrix-to-sec-site.md)


이 문서에서는 Azure로 복제 및 복구할 때 Azure Site Recovery에 대해 지원되는 구성 및 구성 요소를 요약하여 설명합니다. Azure Site Recovery 요구 사항에 대한 자세한 내용은 [필수 구성 요소](site-recovery-prereq.md)를 참조하세요.


## <a name="support-for-deployment-options"></a>배포 옵션에 대한 지원

**배포웹사이트를** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
**Azure Portal** | Azure Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 온-프레미스 VMware VM을 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다. | Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 Virtual Machine Manager 클라우드 외의 온-프레미스 Hyper-V VM을 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다. | Resource Manager 또는 클래식 저장소 및 네트워크를 사용하여 Azure Storage에 온-프레미스 Hyper-V(Virtual Machine Manager 클라우드의 VM)를 복제합니다.<br/><br/> Resource Manager 기반 또는 클래식 VM에 장애 조치합니다.
**클래식 포털** | 유지 관리 모드에만 해당됩니다. 새 자격 증명 모음은 만들 수 없습니다. | 유지 관리 모드에만 해당됩니다. | 유지 관리 모드에만 해당됩니다.
**PowerShell** | 현재 지원되지 않습니다. | 지원됨 | 지원됨


## <a name="support-for-datacenter-management-servers"></a>데이터 센터 관리 서버에 대한 지원

### <a name="virtualization-management-entities"></a>가상화 관리 엔터티

**배포웹사이트를** | **지원**
--- | ---
**VMware VM/물리적 서버** | 최신 업데이트가 설치된 vSphere 6.0, 5.5 또는 5.1
**Hyper-V(Virtual Machine Manager 있음)** | System Center Virtual Machine Manager 2016 및 System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Windows Server 2016 및 2012 R2 호스트가 혼합된 System Center Virtual Machine Manager 2016 클라우드는 현재 지원되지 않습니다.

### <a name="host-servers"></a>호스트 서버

**배포웹사이트를** | **지원**
--- | ---
**VMware VM/물리적 서버** | vCenter 5.5 또는 6.0(5.5 기능만 지원) 
**Hyper-V(Virtual Machine Manager 없음)** | Windows Server 2016, 최신 업데이트가 포함된 Windows Server 2012 R2
**Hyper-V(Virtual Machine Manager 있음)** | Windows Server 2016, 최신 업데이트가 포함된 Windows Server 2012 R2<br/><br/> Windows Server 2016 호스트는 System Center Virtual Machine Manager 2016에서 관리되어야 합니다.

  >[!Note]
  >Windows Server 2016 및 2012 R2를 실행하는 호스트가 혼합된 Hyper-V 사이트는 현재 지원되지 않습니다.

## <a name="support-for-replicated-machine-os-versions"></a>복제된 컴퓨터 운영 체제 버전에 대한 지원

Azure에 복제하는 경우 보호되는 가상 컴퓨터가 [Azure 요구 사항](#failed-over-azure-vm-requirements)을 충족해야 합니다.
다음은 Azure Site Recovery를 사용하는 경우 다양한 배포 시나리오에서 지원되는 복제된 운영 체제를 요약한 테이블입니다. 이 지원은 언급된 OS에서 실행되는 모든 워크로드에 적용됩니다.

 **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | ---
64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868.aspx) 모든 게스트 OS | [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868.aspx) 모든 게스트 OS


>[!Note]
>Linux 버전 파일 시스템(EXT3, ETX4, ReiserFS, XFS), 다중 경로 소프트웨어 장치 매퍼, 볼륨 관리자(LVM2) 및 HP CCISS 컨트롤러 저장소가 있는 물리적 서버에 대한 저장소는 지원되지 *않습니다*.
>ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.

## <a name="support-for-network-configuration"></a>네트워크 구성 지원
다음은 Azure Site Recovery를 사용하여 Azure에 복제하는 다양한 배포 시나리오에서 지원되는 네트워크 구성을 요약한 테이블입니다.

### <a name="host-network-configuration"></a>호스트 네트워크 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
NIC 팀 | 예<br/><br/>물리적 컴퓨터에서 지원되지 않음| 예 | 예
VLAN | 예 | 예 | 예
IPv4 | 예 | 예 | 예
IPv6 | 아니요 | 아니요 | 아니요

### <a name="guest-vm-network-configuration"></a>게스트 VM 네트워크 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
NIC 팀 | 아니요 | 아니요 | 아니요
IPv4 | 예 | 예 | 예
IPv6 | 아니요 | 아니요 | 아니요
고정 IP(Windows) | 예 | 예 | 예
고정 IP(Linux) | 아니요 | 아니요 | 아니요
다중 NIC | 예 | 예 | 예

### <a name="failed-over-azure-vm-network-configuration"></a>장애 조치(Failover)된 Azure VM 네트워크 구성

**Azure 네트워킹** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
Express 경로 | 예 | 예 | 예
ILB | 예 | 예 | 예
ELB | 예 | 예 | 예
트래픽 관리자 | 예 | 예 | 예
다중 NIC | 예 | 예 | 예
예약된 IP | 예 | 예 | 예
IPv4 | 예 | 예 | 예
원본 IP 유지 | 예 | 예 | 예


## <a name="support-for-storage"></a>저장소에 대한 지원
다음은 Azure Site Recovery를 사용하여 Azure에 복제하는 다양한 배포 시나리오에서 지원되는 저장소 구성을 요약한 테이블입니다.

### <a name="host-storage-configuration"></a>호스트 저장소 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
NFS | VMware의 경우 예<br/><br/> 물리적 서버의 경우 아니요 | 해당 없음 | 해당 없음
SMB 3.0 | 해당 없음 | 예 | 예
SAN(ISCSI) | 예 | 예 | 예
다중 경로(MPIO)<br></br>테스트 제품: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON | 예 | 예 | 예

### <a name="guest-or-physical-server-storage-configuration"></a>게스트 또는 물리적 서버 저장소 구성

**구성** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
VMDK | 예 | 해당 없음 | 해당 없음
VHD/VHDX | 해당 없음 | 예 | 예
2세대 VM | 해당 없음 | 예 | 예
EFI/UEFI| 아니요 | 예 | 예
공유 클러스터 디스크 | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음 | 아니요 | 아니요
암호화된 디스크 | 아니요 | 아니요 | 아니요
NFS | 아니요 | 해당 없음 | 해당 없음
SMB 3.0 | 아니요 | 아니요 | 아니요
RDM | 예<br/><br/> 물리적 서버의 경우 해당 없음 | 해당 없음 | 해당 없음
디스크 > 1TB | 아니요 | 아니요 | 아니요
스트라이프 디스크 포함 볼륨 > 1TB<br/><br/> LVM 논리 볼륨 관리 | 예 | 예 | 예
저장소 공간 | 아니요 | 예 | 예
디스크 핫 추가/제거 | 아니요 | 아니요 | 아니요
디스크 제외 | 예 | 예 | 예
다중 경로(MPIO) | 해당 없음 | 예 | 예

**Azure 저장소** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
LRS | 예 | 예 | 예
GRS | 예 | 예 | 예
쿨 저장소 | 아니요 | 아니요 | 아니요
핫 저장소| 아니요 | 아니요 | 아니요
휴지 상태의 암호화(SSE)| 예 | 예 | 예
Premium Storage | 예 | 아니요 | 아니요
Import/Export 서비스 | 아니요 | 아니요 | 아니요


## <a name="support-for-azure-compute-configuration"></a>Azure 계산 구성에 대한 지원

**Compute 기능** | **VMware/물리적 서버** | **Hyper-V(Virtual Machine Manager 없음)** | **Hyper-V(Virtual Machine Manager 있음)**
--- | --- | --- | ---
가용성 집합 | 아니요 | 아니요 | 아니요
HUB | 예 | 예 | 예

## <a name="failed-over-azure-vm-requirements"></a>장애 조치(Failover)된 Azure VM 요구 사항

Azure에서 지원하는 운영 체제를 실행하는 가상 컴퓨터와 물리적 서버를 복제하기 위해 Site Recovery를 배포할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다. 복제하려는 온-프레미스 VM이 Azure에 복제하는 동안 아래의 Azure 요구 사항을 준수해야 합니다.

**엔터티** | **요구 사항** | **세부 정보**
--- | --- | ---
**게스트 운영 체제** | Hyper-V에서 Azure로 복제: Site Recovery에서는 [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)모든 운영 체제를 지원합니다. <br/><br/> VMware 및 물리적 서버 복제의 경우: Windows 및 Linux [필수 구성 요소](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**게스트 운영 체제 아키텍처** | 64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**운영 체제 디스크 크기** | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**운영 체제 디스크 수** | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**데이터 디스크 수** | **Azure에 VMware VM**을 복제하는 경우 64개 이하, **Azure에 Hyper-V VM**을 복제하는 경우 16개 이하 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
**데이터 디스크 VHD 크기** | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
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

