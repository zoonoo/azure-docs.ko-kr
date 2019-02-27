---
title: Azure VM Backup의 Azure Backup 지원 매트릭스
description: Azure Backup 서비스를 사용하여 Azure VM을 백업할 때의 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: b99d6285942bafe5467827c30b5ba2e42094fdf3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431057"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM Backup의 지원 매트릭스
[Azure Backup 서비스](backup-overview.md)를 사용하여 온-프레미스 컴퓨터 및 워크로드, Azure VM을 백업할 수 있습니다. 이 문서에서는 Azure Backup을 사용하여 Azure VM(Virtual Machines)을 백업할 때의 지원 설정 및 제한 사항을 요약해서 설명합니다.

기타 지원 매트릭스:

- Azure Backup에 대한 [일반 지원 매트릭스](backup-support-matrix.md)
- Microsoft Azure Backup 서버/DOM 백업에 대한 [지원 매트릭스](backup-support-matrix-mabs-dpm.md)
- MARS 에이전트를 사용한 백업에 대한 [지원 매트릭스](backup-support-matrix-mars-agent.md)



## <a name="supported-scenarios"></a>지원되는 시나리오

다음은 Azure Backup 서비스를 사용하여 Azure VM을 백업 및 복원하는 방법입니다.


**시나리오** | **Backup** | **에이전트** |**Restore** 
--- | --- | --- | ---
**Azure VM의 직접 백업** | 전체 VM 백업  | Azure VM에 에이전트가 필요하지 않습니다. Azure Backup은 VM에서 실행되는 [Azure VM 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)에 대해 확장을 설치하고 사용합니다. | 복원은 다음과 같이 수행됩니다.<br/><br/> - **기본 VM 만들기**. VM에 여러 IP 주소와 같은 특별한 구성이 사용되지 않을 때 유용합니다.<br/><br/> - **VM 디스크 복원**. 디스크를 복원합니다. 그런 다음, 기존 VM에 연결하거나 PowerShell을 사용하여 디스크에서 새 VM을 만듭니다.<br/><br/> - **VM 디스크 바꾸기**. VM이 있으며 Managed Disks(암호화되지 않음)를 사용하는 경우 디스크를 복원하고 VM에서 기존 디스크 대신 사용할 수 있습니다.<br/><br/> - **특정 파일/폴더 복원**. 전체 VM 대신, VM에서 파일/폴더를 복원할 수 있습니다.  
**Azure VM의 직접 백업(Windows만 해당)** | 특정 파일/폴더/볼륨 백업 | [MARS(Microsoft Azure Recovery Services) 에이전트](backup-azure-file-folder-backup-faq.md)를 설치합니다.<br/><br/> 파일/폴더 수준에서 VM을 백업하려면 Azure VM 에이전트에 대한 백업 확장과 함께 MARS 에이전트를 실행할 수 있습니다. | 특정 폴더/파일을 복원합니다.
**백업 서버에 Azure VM 백업** |  파일/폴더/볼륨, 시스템 상태/완전 파일, 앱 데이터를 System Center DPM 또는 MAB Server(Microsoft Azure Backup Server)에 백업합니다.<br/><br/> 그러면 DPM/MABS에서 백업 자격 증명 모음으로 백업합니다. | VM에 MABS/DPM 보호 에이전트를 설치합니다. MARS 에이전트는 DPM/MABS에 설치됩니다.| 파일/폴더/볼륨, 시스템 상태/완전 파일 및 앱 데이터를 복원합니다. 

백업 서버(backup-architecture.md#architecture-back-up-to-dpmmabs)를 사용하는 백업 및 [지원 요구 사항](backup-support-matrix-mabs-dpm.md)에 대해 자세히 알아봅니다.


## <a name="supported-backup-actions"></a>지원되는 백업 작업

**작업** | **지원**
--- | ---
Microsoft Azure VM을 만들 때 백업 사용 | 지원 대상:  Windows Server 2016(Datacenter/Datacenter Core), Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1
Linux VM을 만들 때 백업 사용 | 지원 대상:<br/><br/> - Ubuntu Server: 1710, 1704, 1604(LTS), 1404(LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
종료/오프라인/검색 VM에 해당하는 VM 백업 | 지원됩니다.<br/><br/> 스냅숏이 충돌 일치 특성만 있고 앱 일치 특성은 없습니다.
Managed Disks로 마이그레이션한 후 디스크를 백업합니다. | 지원됩니다.<br/><br/> 백업이 계속 작동합니다. 추가적인 조치가 필요하지 않습니다.
리소스 그룹 잠금을 사용하도록 설정한 후 Managed Disks를 백업합니다. | 지원되지 않습니다.<br/><br/> 백업을 수행하여 이전 리소스 지점을 삭제할 수 없으며, 리소스 지점의 최대 제한에 도달하면 백업이 시작되지 못합니다.
VM의 백업 정책 수정 | 지원됩니다.<br/><br/> VM은 새 정책의 일정 및 보존 설정을 사용하여 백업됩니다. 보존 설정이 연장될 경우 기존 복구 지점이 표시되고 유지됩니다. 보존 설정이 축소될 경우 다음 정리 작업에서 기존 복구 지점이 정리되고, 결과적으로 삭제됩니다.
백업 작업 취소 | 스냅숏 프로세스 동안 지원됩니다.<br/><br/> 스냅숏이 자격 증명 모음으로 전송될 때는 지원되지 않습니다. 
다른 지역 또는 구독에 VM 백업 |  지원되지 않습니다.
매일 백업(Azure VM 확장을 통해) | 매일 1회 백업 예약<br/><br/> 매일 최대 4개의 주문형 백업을 수행할 수 있습니다.
매일 백업(MARS 에이전트를 통해) | 매일 3회 백업 예약 
매일 백업(DPM/MABS를 통해) | 매일 2회 백업 예약
월별/연도별 백업   | Azure VM 확장을 사용하여 백업하는 경우에는 지원되지 않습니다. 일별 및 주별만 지원됩니다.<br/><br/> 월별/연도별 보존 기간에 대해 일별/주별 백업을 유지하도록 정책을 설정할 수 있습니다.
자동 클록 조정 | 지원되지 않습니다.<br/><br/> Azure Backup은 VM을 백업할 때 일광 절약 시간 변경에 대해 시계 자동 조정을 고려하지 않습니다.<br/><br/>  필요에 따라 정책을 수동으로 수정합니다.
[하이브리드 백업에 대한 보안 기능](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  이러한 기능을 사용하지 않도록 설정할 수 없습니다.



## <a name="operating-system-support-windows"></a>운영 체제 지원(Windows)

이 표에서는 Microsoft Azure VM을 백업하는 경우 지원되는 운영 체제를 요약해서 보여 줍니다.

**시나리오** | **OS 지원** 
--- | ---
Azure VM 에이전트 확장을 사용하는 백업 | Windows 클라이언트: 지원되지 않음<br/><br/> Windows Server: Windows Server 2008 R2 이상 
MARS 에이전트를 사용하여 백업 | [지원되는](backup-support-matrix-mars-agent.md#support-for-direct-backups) 운영 체제
DPM/MABS를 통한 백업 | [MABS](backup-mabs-protection-matrix.md) 및 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807)을 사용하는 백업이 지원되는 운영 체제

## <a name="support-for-linux-backup"></a>Linux 백업 지원

Linux 머신을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**작업** | **지원**
--- | --- 
Linux Azure VM 에이전트를 사용하여 Linux Azure VM 백업 | 파일 일치 백업입니다.<br/><br/> [사용자 지정 스크립트](backup-azure-linux-app-consistent.md)를 사용하는 앱 일치 백업입니다.<br/><br/> 복원하는 동안 새 VM을 만들거나, 디스크를 복원한 후 VM을 만드는 데 사용하거나, 디스크를 복원한 후 기존 VM의 디스크 대신 사용할 수 있습니다. 또한 개별 파일 및 폴더를 복원할 수도 있습니다.
MARS 에이전트를 사용하여 Linux Azure VM 백업 | 지원되지 않습니다.<br/><br/> MARS 에이전트는 Windows 머신에만 설치할 수 있습니다.
DPM/MABS를 사용하여 Linux Azure VM 백업 | 지원되지 않습니다.

## <a name="operating-system-support-linux"></a>운영 체제 지원(Linux)

Azure VM Linux 백업의 경우 Azure Backup은 [Azure 인증 Linux 배포 목록](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 지원합니다. 다음 사항에 유의하세요.

- Azure Backup은 Core OS Linux를 지원하지 않습니다.
- Azure Backup은 32비트 운영 체제를 지원하지 않습니다.
- VM에서 [Linux용 Azure VM 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)를 사용할 수 있고 Python이 지원되는 한, 다른 Bring-Your-Own Linux 배포가 작동할 수 있습니다.



## <a name="backup-frequency-and-retention"></a>백업 빈도 및 보존

**설정** | **제한** 
--- | --- 
보호된 인스턴스(머신/워크로드)당 최대 복구 지점 수 | 9999
복구 지점에 대한 최대 만료 시간 | 제한 없음
자격 증명 모음에 대한 최대 백업 빈도(Azure VM 확장) | 하루 한 번
자격 증명 모음에 대한 최대 백업 빈도(MARS 에이전트) | 매일 3회 백업
DPM/MABS에 대한 최대 백업 빈도 | SQL Server에 대해 15분마다<br/><br/> 다른 워크로드의 경우 한 시간에 한 번
복구 지점 보존 | 매일, 매주, 매월, 매년
최대 보존 기간 | 백업 빈도에 따라 다름
DPM/MABS 디스크의 복구 지점 수 | 파일 서버의 경우 64개, 앱 서버의 경우 448개<br/><br/> 온-프레미스 DPM에 대한 테이프 복구 지점 수는 제한되지 않습니다.


## <a name="supported-restore-methods"></a>지원되는 복원 방법

**복원 방법** | **세부 정보**
--- | ---
**새 VM 만들기** | 복원 프로세스 동안 VM을 만들 수 있습니다. <br/><br/> 이 옵션은 기본 VM을 작동 및 실행합니다. VM 이름, 리소스 그룹, 가상 네트워크, 서브넷 및 스토리지를 지정할 수 있습니다.  
**디스크 복원** | 디스크를 복원하고 VM을 만드는 데 사용할 수 있습니다.<br/><br/> 이 옵션을 선택하면 Azure Backup은 자격 증명 모음의 데이터를 선택한 스토리지 계정으로 복사합니다. 복원 작업은 템플릿을 생성합니다. 이 템플릿을 다운로드한 후 사용하여 사용자 지정 VM 설정을 지정하고 VM을 만들 수 있습니다.<br/><br/> 이 옵션을 사용하면 이전 옵션보다 더 많은 설정을 지정하여 VM을 만들 수 있습니다.<br/><br/>
**기존 디스크 바꾸기** | 디스크를 복원하고 복원된 디스크를 사용하여 현재 VM에 있는 디스크를 바꿀 수 있습니다.
**파일 복원** | 선택한 복구 지점에서 파일을 복구할 수 있습니다. 복구 지점에서 VM 디스크를 탑재하는 스크립트를 다운로드합니다. 그런 다음, 디스크 볼륨을 검색하여 복구하려는 파일/폴더를 찾고, 완료되면 디스크를 분리합니다.

## <a name="support-for-file-level-restore"></a>파일 수준 복원에 대한 지원

**Restore** | **지원됨**
--- | --- 
운영 체제에서 파일 복원 | 백업된 VM과 동일한(또는 호환되는) OS가 있는 모든 컴퓨터에서 파일을 복원할 수 있습니다. [호환되는 OS 표](backup-azure-restore-files-from-vm.md#system-requirements)를 참조하세요.
클래식 VM에서 파일 복원 | 지원되지 않습니다. 
암호화된 VM에서 파일 복원 | 지원되지 않습니다.
네트워크 제한 스토리지 계정에서 파일 복원 | 지원되지 않습니다.
Windows 스토리지 공간을 사용하여 VM에서 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다.<br/><br/> 대신, 호환되는 VM에서 파일을 복원합니다.
LVM/RAID 배열을 사용하여 Linux VM에서 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다.<br/><br/> 호환 가능한 VM에서 복원합니다.
특수한 네트워크 설정을 사용하여 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다. <br/><br/> 호환 가능한 VM에서 복원합니다.

## <a name="support-for-vm-management"></a>VM 관리 지원

이 표에서는 VM 관리 작업(예: VM 디스크 추가 또는 바꾸기) 동안의 백업 지원을 요약해서 설명합니다.

**Restore** | **지원됨** 
--- | --- 
구독/지역/영역에서의 복원 | 지원되지 않음 
기존 VM에 복원 | 디스크 바꾸기 옵션을 사용합니다.
Azure SSE(스토리지 서비스 암호화)가 설정된 스토리지 계정을 사용하여 디스크 복원 | 지원되지 않습니다.<br/><br/> SSE를 사용하도록 설정하지 않은 계정으로 복원합니다.
혼합된 스토리지 계정으로 복원 | 지원되지 않습니다.<br/><br/> 스토리지 계정 유형에 따라, 복원된 모든 디스크가 프리미엄 또는 표준 중 하나가 되며 혼합되지 않습니다. 
ZRS를 사용하여 스토리지 계정으로 복원 | 지원되지 않음
가용성 집합에 VM 직접 복원 | Managed Disks의 경우, 디스크를 복원하고, 템플릿에서 가용성 집합 옵션을 사용할 수 있습니다.<br/><br/> 비관리형 디스크에서는 지원되지 않습니다. 비관리형 디스크의 경우, 디스크를 복원하고 가용성 집합에 VM을 만듭니다.
관리형 VM으로 업그레이드한 후에 비관리형 VM 백업 복원 | 지원됩니다.<br/><br/> 디스크를 복원하고 관리형 VM을 만들 수 있습니다.
VM이 Managed Disks로 마이그레이션되기 전에 VM을 복원 지점으로 복원 | 지원됩니다.<br/><br/> 비관리형 디스크(기본값)로 복원하고, 복원된 디스크를 Managed Disks로 변환한 후 Managed Disks를 사용하여 VM을 만듭니다.
삭제된 VM을 복원합니다. | 지원됩니다.<br/><br/> 복구 지점에서 VM을 복원할 수 있습니다.
포털을 통해 다중 DC 구성의 일부인 DC(도메인 컨트롤러) VM 복원 | PowerShell을 사용하여 디스크를 복원하고 VM을 만드는 경우 지원됩니다.
다른 VNet에서 VM 복원 |  지원됩니다.<br/><br/> VNet은 동일한 구독 및 지역에 있어야 합니다.




## <a name="vm-compute-support"></a>VM 컴퓨팅 지원

**Compute** | **지원** 
--- | --- 
VM 크기 |   CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM<br/><br/> [자세히 알아보기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
[가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets)의 VM 백업 | 지원됩니다.<br/><br/> VM을 신속하게 만드는 옵션으로는 가용성 집합에서 VM을 복원할 수 없습니다. 대신, VM을 복원할 때는 디스크를 복원한 후 이 디스크를 사용하여 VM을 배포하거나 디스크를 복원한 후 이 디스크를 기존 디스크 대신 사용해야 합니다. 
[가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)의 VM 백업 |  지원되지 않습니다.  
[HUB(하이브리드 사용 혜택)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)로 배포한 VM 백업 | 지원됩니다.
[확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)에 배포한 VM 백업 |   지원되지 않음   
[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)에서 배포한 VM 백업<br/><br/> (Microsoft, 타사에서 게시) |   지원됩니다.<br/><br/> VM에서 지원되는 운영 체제도 실행하고 있어야 합니다.<br/><br/> VM에서 파일을 복구할 경우 호환되는 OS(이전 또는 이후 OS 아님)로만 복원할 수 있습니다.
사용자 지정 이미지(타사)에서 배포한 VM 백업 |    지원됩니다.<br/><br/> VM에서 지원되는 운영 체제도 실행하고 있어야 합니다.<br/><br/> VM에서 파일을 복구할 경우 호환되는 OS(이전 또는 이후 OS 아님)로만 복원할 수 있습니다.
Azure로 마이그레이션된 VM 백업   | 지원됩니다.<br/><br/> VM을 백업하려면 VM 에이전트를 마이그레이션된 컴퓨터에 설치해야 합니다. 



## <a name="vm-storage-support"></a>VM 스토리지 지원

**구성 요소** | **지원**
--- | ---
Azure VM 데이터 디스크 수 | 16개 이하의 데이터 디스크가 있는 VM을 백업합니다.
데이터 디스크 크기 | 개별 디스크의 크기는 최대 4,095GB입니다.<br/><br/> 최신 버전의 Azure VM 백업(인스턴스 복원이라고도 함)을 실행하는 경우 최대 4TB의 디스크 크기가 지원됩니다. [자세히 알아보기](backup-instant-restore-capability.md).
저장소 유형 | 표준 HDD, 표준 SSD 및 프리미엄 SSD <br/><br/> 최신 버전의 Azure VM 백업(인스턴스 복원이라고도 함)을 실행하는 경우 표주 SSD가 지원됩니다. [자세히 알아보기](backup-instant-restore-capability.md).
관리 디스크 | 지원됨
암호화된 디스크 | 지원됩니다.<br/><br/> ADE(Azure Disk Encryption)를 사용하도록 설정된 Azure AM을 백업할 수 있습니다(Azure AD 앱 사용 또는 사용 안 함).<br/><br/> 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 전체 VM을 복구해야 합니다.<br/><br/> Azure Backup에서 이미 보호되는 VM에 암호화를 사용하도록 설정할 수 있습니다.
Write Accelerator가 설정된 디스크 | 지원되지 않습니다.<br/><br/> 최신 버전의 Azure VM 백업([즉시 복원](backup-instant-restore-capability.md)이라고도 함)을 실행하는 경우 백업에서 Write Accelerator가 설정된 디스크를 제외할 수 있습니다.
중복 제거된 디스크 백업 | 지원되지 않습니다.
보호된 VM에 디스크 추가 | 지원됩니다.
보호된 VM에서 디스크 크기 조정 | 지원됩니다.

## <a name="vm-network-support"></a>VM 네트워크 지원


**구성 요소** | **지원**
--- | ---
NIC 수 | 특정 Azure VM 크기에서 지원되는 최대 NIC 수입니다.<br/><br/> 복원 프로세스 동안 VM이 만들어지면 NIC가 만들어집니다.<br/><br/> 복원된 VM의 NIC 수는 보호를 사용하도록 설정한 VM의 NIC 수를 미러링합니다. 설정 후에는 NIC를 제거해도 이 수에 영향을 미치지 않습니다.
외부/내부 부하 분산 장치 |   지원됩니다. <br/></br> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
예약된 여러 IP 주소 |  지원됩니다. <br/></br> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
여러 네트워크 어댑터를 사용하는 VM  | 지원됩니다. <br/></br> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
공용 IP 주소가 있는 VM    | 지원됩니다.<br/><br/> 기존 공용 IP 주소를 NIC에 연결하거나 주소를 만든 후 복원이 완료된 후에 NIC에 연결해야 합니다.
NIC/서브넷의 NSG(네트워크 보안 그룹)입니다. |   지원됩니다.
예약된 IP 주소(고정) | 지원되지 않습니다.<br/><br/> 예약된 IP 주소가 있고 정의된 끝점이 없는 VM은 백업할 수 없습니다.
동적 IP 주소 |    지원됩니다.<br/><br/> 원본 VM의 NIC가 동적 IP 주소 지정을 사용하는 경우 기본적으로 복원된 VM의 NIC도 해당 주소 지정 방식을 사용합니다.
Traffic Manager | 지원됨<br/><br/>. 백업한 VM이 Traffic Manager에 있는 경우 복원된 VM을 동일한 Traffic Manager에 수동으로 추가해야 합니다. 
Azure DNS | 지원됩니다.
사용자 지정 DNS |    지원됩니다.
HTTP 프록시를 통한 아웃바운드 연결 | 지원됩니다.<br/><br/> 인증된 프록시는 지원되지 않습니다. 
VNet 서비스 엔드포인트  | 지원됩니다.<br/><br/> 방화벽 및 VNet 스토리지 계정 설정은 모든 네트워크의 액세스를 허용해야 합니다. 



## <a name="vm-securityencryption-support"></a>VM 보안/암호화 지원

Azure Backup은 전송 중 및 정지 상태의 데이터에 대한 암호화를 지원합니다.

Azure로의 네트워크 트래픽:
- 서버에서 Recovery Services 자격 증명 모음으로의 백업 트래픽은 Advanced Encryption Standard 256을 사용하여 암호화됩니다.
- 백업 데이터는 보안 HTTPS 링크를 통해 전송됩니다.
- 백업 데이터는 암호화된 형식으로 Recovery Services 자격 증명 모음에 저장됩니다.
- 사용자만 이 데이터의 잠금을 해제할 수 있는 암호를 보유합니다. Microsoft는 어떠한 경우에도 백업 데이터의 암호를 해독할 수 없습니다.
    > [!WARNING]
    > 자격 증명 모음이 설정되면 암호화 키에 대한 액세스 권한만 부여됩니다. Microsoft는 복사본을 유지 관리할 수 없으며, 키에 대한 액세스 권한도 없습니다. 키를 잃어버리면 Microsoft에서 백업 데이터를 복구할 수 없습니다.
데이터 보안:
- Azure VM이 백업되면 가상 머신 *내에서* 암호화를 설정해야 합니다.
- Azure Backup은 Azure Disk Encryption을 지원하며, Windows 가상 머신의 BitLocker와 Linux 가상 머신의 **dm-crypt**를 사용합니다.
- 백 엔드에서 Azure Backup는 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 사용하여 미사용 데이터를 보호합니다.


**머신** | **전송 중** | **저장**
--- | --- | ---
온-프레미스 Windows 머신(DPM/MABS 사용 안 함) | ![예][green] | ![예][green]
Azure VM | ![예][green] | ![예][green] 
온-프레미스/Azure VM(DPM 사용) | ![예][green] | ![예][green]
온-프레미스/Azure VM(MABS 사용) | ![예][green] | ![예][green]



## <a name="vm-compression-support"></a>VM 압축 지원

백업은 아래 표에 요약된 대로 백업 트래픽의 압축을 지원합니다. 다음 사항에 유의하세요.

- Azure VM의 경우 VM 확장은 Azure 스토리지 계정에서 스토리지 네트워크를 통해 직접 데이터를 읽으므로 이 트래픽을 압축할 필요가 없습니다.
- DPM 또는 MABS를 사용하는 경우 대역폭을 절약하기 위해 DPM/MABS에 백업하기 전에 데이터를 압축할 수 있습니다. 

**머신** | **MABS/DPM에 압축(TCP)** | **자격 증명 모음에 압축(HTTPS)**
--- | --- | ---
온-프레미스 Windows 머신(DPM/MABS 사용 안 함) | 해당 없음 | 예
Azure VM | 해당 없음 | 해당 없음
온-프레미스/Azure VM(DPM 사용) | ![예][green] | ![예][green]
온-프레미스/Azure VM(MABS 사용) | ![예][green] | ![예][green]




## <a name="next-steps"></a>다음 단계

- [Azure VM 백업](backup-azure-arm-vms-prepare.md)
- 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
- Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
- Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

