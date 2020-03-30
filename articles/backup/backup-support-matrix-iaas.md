---
title: Azure VM Backup의 지원 매트릭스
description: Azure Backup 서비스를 사용하여 Azure VM을 백업할 때의 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: c30a1d1b30fcc7a12449b44d35704b3b43daa385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247905"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM Backup의 지원 매트릭스

Azure Backup [서비스를](backup-overview.md) 사용하여 온-프레미스 컴퓨터 및 워크로드 및 Azure 가상 시스템(VM)을 백업할 수 있습니다. 이 문서에서는 Azure 백업을 통해 Azure VM을 백업할 때 지원 설정 및 제한 사항에 대해 요약합니다.

기타 지원 매트릭스:

- Azure 백업에 대한 [일반 지원 매트릭스](backup-support-matrix.md)
- Azure 백업 서버/시스템 센터 데이터 보호 관리자(DPM) 백업에 대한 [지원 매트릭스](backup-support-matrix-mabs-dpm.md)
- Microsoft Azure 복구 서비스(MARS) 에이전트를 통한 백업을 위한 [지원 매트릭스](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>지원되는 시나리오

다음은 Azure Backup 서비스를 사용하여 Azure VM을 백업 및 복원하는 방법입니다.

**시나리오** | **백업** | **에이전트** |**복원**
--- | --- | --- | ---
Azure VM의 직접 백업  | 전체 VM을 백업합니다.  | Azure VM에는 추가 에이전트가 필요하지 않습니다. Azure Backup은 VM에서 실행 중인 [Azure VM 에이전트에](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 대한 확장을 설치하고 사용합니다. | 다음과 같이 복원합니다.<br/><br/> - **기본 VM을 만듭니다.** 이 기능은 VM에 여러 IP 주소와 같은 특별한 구성이 없는 경우에 유용합니다.<br/><br/> - **VM 디스크 복원**. 디스크를 복원합니다. 그런 다음 기존 VM에 연결하거나 PowerShell을 사용하여 디스크에서 새 VM을 만듭니다.<br/><br/> - **VM 디스크 바꾸기**. VM이 있으며 Managed Disks(암호화되지 않음)를 사용하는 경우 디스크를 복원하고 VM에서 기존 디스크 대신 사용할 수 있습니다.<br/><br/> - **특정 파일/폴더 복원**. 전체 VM 대신 VM에서 파일/폴더를 복원할 수 있습니다.
Azure VM의 직접 백업(Windows만 해당)  | 특정 파일/폴더/볼륨을 백업합니다. | Azure [복구 서비스 에이전트를](backup-azure-file-folder-backup-faq.md)설치합니다.<br/><br/> 파일/폴더 수준에서 VM을 백업하려면 Azure VM 에이전트에 대한 백업 확장과 함께 MARS 에이전트를 실행할 수 있습니다. | 특정 폴더/파일을 복원합니다.
백업 서버에 Azure VM 백업  | 파일/폴더/볼륨 백업 시스템 상태/베어 메탈 파일; 시스템 센터 DPM 또는 마이크로 소프트 Azure 백업 서버 (MABS)에 응용 프로그램 데이터.<br/><br/> 그런 다음 DPM/MABS가 백업 자격 증명 모음에 백업합니다. | VM에 DPM/MABS 보호 에이전트를 설치합니다. MARS 에이전트는 DPM/MABS에 설치됩니다.| 파일/폴더/볼륨, 시스템 상태/완전 파일 및 앱 데이터를 복원합니다.

[백업 서버를 사용한](backup-architecture.md#architecture-back-up-to-dpmmabs) 백업 및 [지원 요구 사항에](backup-support-matrix-mabs-dpm.md)대해 자세히 알아봅니다.

>[!NOTE]
> **Azure Backup은 이제 Azure 가상 시스템 백업 솔루션을 사용하여 선택적 디스크 백업 및 복원을 지원합니다.**
>
>현재 Azure Backup은 가상 시스템 백업 솔루션을 사용하여 VM의 모든 디스크(운영 체제 및 데이터)를 함께 백업할 수 있도록 지원합니다. 제외 디스크 기능을 사용하면 VM의 여러 데이터 디스크에서 하나 또는 몇 개를 백업할 수 있는 옵션이 있습니다. 이를 통해 백업 및 복원 요구 사항에 대한 효율적이고 비용 효율적인 솔루션을 제공합니다. 각 복구 지점에는 백업 작업에 포함된 디스크 데이터가 포함되어 있으므로 복원 작업 중에 지정된 복구 지점에서 디스크 하위 집합을 복원할 수 있습니다. 이는 스냅샷과 볼트모두에서 복원하는 데 적용됩니다.
>
>**미리 보기에 등록하려면AskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>지원되는 백업 작업

**작업** | **지원**
--- | ---
종료/오프라인 VM인 VM 백업 | 지원됩니다.<br/><br/> 스냅샷이 충돌 일치 특성만 있고 앱 일치 특성은 없습니다.
관리되는 디스크로 마이그레이션한 후 디스크 백업 | 지원됩니다.<br/><br/> 백업이 계속 작동합니다. 사용자가 조치할 필요는 없습니다.
리소스 그룹 잠금을 사용하도록 설정한 후 Managed Disks를 백업합니다. | 지원되지 않습니다.<br/><br/> Azure Backup은 이전 복원 지점을 삭제할 수 없으며 복원 지점의 최대 제한에 도달하면 백업이 실패하기 시작합니다.
VM의 백업 정책 수정 | 지원됩니다.<br/><br/> VM은 새 정책의 일정 및 보존 설정을 사용하여 백업됩니다. 보존 설정이 연장될 경우 기존 복구 지점이 표시되고 유지됩니다. 감소하면 기존 복구 지점이 다음 정리 작업에서 정리되고 결국 삭제됩니다.
백업 작업 취소| 스냅샷 프로세스 동안 지원됩니다.<br/><br/> 스냅샷이 자격 증명 모음으로 전송될 때는 지원되지 않습니다.
다른 지역 또는 구독에 VM 백업 |지원되지 않습니다.
매일 백업(Azure VM 확장을 통해) | 매일 1회 백업 예약<br/><br/>Azure Backup 서비스는 하루에 최대 9개의 온디맨드 백업을 지원하지만 최상의 성능을 보장하기 위해 매일 4회 이상 온디맨드 백업을 권장합니다.
매일 백업(MARS 에이전트를 통해) | 매일 3회 백업 예약
매일 백업(DPM/MABS를 통해) | 매일 2회 백업 예약
월별/연도별 백업| Azure VM 확장을 사용하여 백업하는 경우에는 지원되지 않습니다. 일별 및 주별만 지원됩니다.<br/><br/> 월별/연도별 보존 기간에 대해 일별/주별 백업을 유지하도록 정책을 설정할 수 있습니다.
자동 클록 조정 | 지원되지 않습니다.<br/><br/> Azure Backup은 VM을 백업할 때 일광 절약 시간제 변경 사항에 맞게 자동으로 조정되지 않습니다.<br/><br/>  필요에 따라 정책을 수동으로 수정합니다.
[하이브리드 백업에 대한 보안 기능](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |보안 기능을 사용하지 않도록 설정은 지원되지 않습니다.
컴퓨터 시간이 변경된 VM 백업 | 지원되지 않습니다.<br/><br/> 해당 VM에 대한 백업을 사용하도록 설정한 후 컴퓨터 시간이 향후 날짜 시간으로 변경된 경우 그러나 시간 변경이 되더라도 성공적인 백업은 보장되지 않습니다.  

## <a name="operating-system-support-windows"></a>운영 체제 지원(Windows)

다음 표는 Windows Azure VM을 백업할 때 지원되는 운영 체제를 요약합니다.

**시나리오** | **OS 지원**
--- | ---
Azure VM 에이전트 확장을 사용하는 백업 | - 윈도우 10 클라이언트 (64 비트 만) <br/><br/>- 윈도우 서버 2019 (데이터 센터 / 데이터 센터 코어 / 표준) <br/><br/> - 윈도우 서버 2016 (데이터 센터 / 데이터 센터 코어 / 표준) <br/><br/> - 윈도우 서버 2012 R2 (데이터 센터 / 표준) <br/><br/> - 윈도우 서버 2008 R2 (RTM 및 SP1 표준)  <br/><br/> - 윈도우 서버 2008 (64 비트 만)
MARS 에이전트를 사용하여 백업 | [지원되는](backup-support-matrix-mars-agent.md#supported-operating-systems) 운영 체제.
DPM/MABS로 백업 | [MABS](backup-mabs-protection-matrix.md) 및 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807)을 사용하는 백업이 지원되는 운영 체제

Azure Backup은 32비트 운영 체제를 지원하지 않습니다.

## <a name="support-for-linux-backup"></a>Linux 백업 지원

Linux 머신을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**작업** | **지원**
--- | ---
Linux Azure VM 에이전트를 사용하여 Linux Azure VM 백업 | 파일 일치 백업입니다.<br/><br/> [사용자 지정 스크립트](backup-azure-linux-app-consistent.md)를 사용하는 앱 일치 백업입니다.<br/><br/> 복원하는 동안 새 VM을 만들고, 디스크를 복원하고, 이를 사용하여 VM을 만들거나, 디스크를 복원하고 이를 사용하여 기존 VM의 디스크를 교체할 수 있습니다. 또한 개별 파일 및 폴더를 복원할 수도 있습니다.
MARS 에이전트를 사용하여 Linux Azure VM 백업 | 지원되지 않습니다.<br/><br/> MARS 에이전트는 Windows 머신에만 설치할 수 있습니다.
DPM/MABS를 사용하여 Linux Azure VM 백업 | 지원되지 않습니다.

## <a name="operating-system-support-linux"></a>운영 체제 지원(Linux)

Azure VM Linux 백업의 경우 Azure Backup은 [Azure 인증 Linux 배포 목록](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 지원합니다. 다음 사항에 유의하세요.

- Azure Backup은 Core OS Linux를 지원하지 않습니다.
- Azure Backup은 32비트 운영 체제를 지원하지 않습니다.
- 다른 리눅스 배포판은 [Linux용 Azure VM 에이전트를](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) VM에서 사용할 수 있는 한, 파이썬이 지원되는 한 작동할 수 있습니다.
- Azure Backup은 파이썬 버전 2.7이 설치되어 있지 않은 경우 프록시로 구성된 Linux VM을 지원하지 않습니다.

## <a name="backup-frequency-and-retention"></a>백업 빈도 및 보존

**설정** | **제한**
--- | ---
보호된 인스턴스당 최대 복구 포인트(기계/워크로드) | 9999.
복구 지점에 대한 최대 만료 시간 | 제한 없음.
자격 증명 모음에 대한 최대 백업 빈도(Azure VM 확장) | 하루에 한 번.
자격 증명 모음에 대한 최대 백업 빈도(MARS 에이전트) | 매일 3회 백업
DPM/MABS에 대한 최대 백업 빈도 | SQL Server의 경우 15분마다<br/><br/> 다른 워크로드의 경우 한 시간에 한 번.
복구 지점 보존 | 매일, 매주, 매월, 매년.
최대 보존 기간 | 백업 빈도에 따라 다름
DPM/MABS 디스크의 복구 지점 수 | 파일 서버의 경우 64개, 앱 서버의 경우 448개입니다.<br/><br/> 온-프레미스 DPM에 대한 테이프 복구 지점 수는 제한되지 않습니다.

## <a name="supported-restore-methods"></a>지원되는 복원 방법

**복원 옵션** | **세부 정보**
--- | ---
**새 VM 만들기** | 복원 지점에서 기본 VM을 빠르게 만들고, 준비하고, 실행할 수 있습니다.<br/><br/> VM의 이름을 지정하고, 배치할 리소스 그룹 및 가상 네트워크(VNet)를 선택하고 복원된 VM에 대한 저장소 계정을 지정할 수 있습니다. 새 VM은 원본 VM과 동일한 영역에서 만들어야 합니다.
**디스크 복원** | VM 디스크를 복원한 다음 새 VM을 만드는 데 사용할 수 있습니다.<br/><br/> Azure Backup은 VM을 사용자 지정하고 만드는 데 도움이 되는 템플릿을 제공합니다. <br/><br> 복원 작업은 다운로드하여 사용자 지정 VM 설정을 지정하고 VM을 만드는 데 사용할 수 있는 템플릿을 생성합니다.<br/><br/> 디스크는 지정한 리소스 그룹에 복사됩니다.<br/><br/> 또는 디스크를 기존 VM에 연결하거나 PowerShell을 사용하여 새 VM을 만들 수 있습니다.<br/><br/> 이 옵션은 VM을 사용자 지정하거나, 백업 시 존재하지 않았던 구성 설정을 추가하거나, 템플릿 또는 PowerShell을 사용하여 구성해야 하는 설정을 추가하려는 경우에 유용합니다.
**기존 항목 바꾸기** | 디스크를 복원하고 이 디스크를 사용하여 기존 VM의 디스크를 바꿀 수 있습니다.<br/><br/> 현재 VM이 있어야 합니다. 삭제된 경우 이 옵션을 사용할 수 없습니다.<br/><br/> Azure Backup은 디스크를 교체하기 전에 기존 VM의 스냅숏을 만들고 지정한 스테이징 위치에 저장합니다. VM에 연결된 기존 디스크가 선택한 복원 지점으로 대체됩니다.<br/><br/> 스냅숏은 볼트에 복사되고 보존 정책에 따라 유지됩니다. <br/><br/> 디스크 교체 작업 후 원래 디스크는 리소스 그룹에 유지됩니다. 필요하지 않은 경우 원래 디스크를 수동으로 삭제하도록 선택할 수 있습니다. <br/><br/>기존 항목 바꾸기는 암호화되지 않은 관리 VM에 대해 지원됩니다. 관리되지 않는 디스크, [일반화된 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 또는 [사용자 지정 이미지를 사용하여 만든](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) VM에 대해서는 지원되지 않습니다.<br/><br/> 현재 VM보다 많거나 적은 디스크가 복원 지점에 있는 경우 복원 지점의 디스크 수에는 VM 구성만 반영됩니다.<br><br> 백업 클라이언트 앱에 복원을 수행하는 동안 이러한 리소스에 대한 사용 권한이 없기 때문에 VM에 대해 기존 대체가 지원되지 않는 연결된 리소스(예: [사용자 할당된 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 또는 Key [Vault)가](https://docs.microsoft.com/azure/key-vault/key-vault-overview)지원되지 않습니다.
**교차 지역(보조 지역)** | 교차 지역 복원을 사용하여 [Azure 쌍을 이루는 영역인](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)보조 리전에서 Azure VM을 복원할 수 있습니다.<br><br> 백업이 보조 지역에서 수행되는 경우 선택한 복구 지점에 대한 모든 Azure VM을 복원할 수 있습니다.<br><br> 이 기능은 아래 옵션에서 사용할 수 있습니다.<br> * [VM 만들기](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [디스크 복원](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> 현재 [기존 디스크 교체](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) 옵션을 지원하지 않습니다.<br><br> 사용 권한<br> 보조 지역의 복원 작업은 백업 관리자 및 앱 관리자가 수행할 수 있습니다.

## <a name="support-for-file-level-restore"></a>파일 수준 복원에 대한 지원

**복원** | **지원됨**
--- | ---
운영 체제에서 파일 복원 | 백업된 VM과 동일한(또는 호환되는) OS가 있는 모든 컴퓨터에서 파일을 복원할 수 있습니다. [호환되는 OS 테이블을](backup-azure-restore-files-from-vm.md#system-requirements)참조하십시오.
클래식 VM에서 파일 복원 | 지원되지 않습니다.
암호화된 VM에서 파일 복원 | 지원되지 않습니다.
네트워크 제한 스토리지 계정에서 파일 복원 | 지원되지 않습니다.
Windows 스토리지 공간을 사용하여 VM에서 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다.<br/><br/> 대신, 호환되는 VM에서 파일을 복원합니다.
LVM/RAID 배열을 사용하여 Linux VM에서 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다.<br/><br/> 호환 가능한 VM에서 복원합니다.
특수한 네트워크 설정을 사용하여 파일 복원 | 동일한 VM에서 복원이 지원되지 않습니다. <br/><br/> 호환 가능한 VM에서 복원합니다.

## <a name="support-for-vm-management"></a>VM 관리 지원

다음 표에는 VM 디스크 추가 또는 교체와 같은 VM 관리 작업 중 백업에 대한 지원이 요약되어 있습니다.

**복원** | **지원됨**
--- | ---
구독/지역/영역에서의 복원 | 지원되지 않습니다.
기존 VM에 복원 | 디스크 바꾸기 옵션을 사용합니다.
Azure SSE(스토리지 서비스 암호화)가 설정된 스토리지 계정을 사용하여 디스크 복원 | 지원되지 않습니다.<br/><br/> SSE를 사용하도록 설정하지 않은 계정으로 복원합니다.
혼합된 스토리지 계정으로 복원 |지원되지 않습니다.<br/><br/> 스토리지 계정 유형에 따라, 복원된 모든 디스크가 프리미엄 또는 표준 중 하나가 되며 혼합되지 않습니다.
가용성 집합에 VM 직접 복원 | 관리 디스크의 경우 디스크를 복원하고 템플릿에서 가용성 집합 옵션을 사용할 수 있습니다.<br/><br/> 비관리형 디스크에서는 지원되지 않습니다. 비관리형 디스크의 경우, 디스크를 복원하고 가용성 집합에 VM을 만듭니다.
관리되는 VM으로 업그레이드한 후 관리되지 않는 VM의 백업 복원| 지원됩니다.<br/><br/> 디스크를 복원하고 관리형 VM을 만들 수 있습니다.
VM이 Managed Disks로 마이그레이션되기 전에 VM을 복원 지점으로 복원 | 지원됩니다.<br/><br/> 비관리형 디스크(기본값)로 복원하고, 복원된 디스크를 Managed Disks로 변환한 후 Managed Disks를 사용하여 VM을 만듭니다.
삭제된 VM을 복원합니다. | 지원됩니다.<br/><br/> 복구 지점에서 VM을 복원할 수 있습니다.
포털을 통해 다중 DC 구성의 일부인 DC(도메인 컨트롤러) VM 복원 | PowerShell을 사용하여 디스크를 복원하고 VM을 만드는 경우 지원됩니다.
다른 가상 네트워크에서 VM 복원 |지원됩니다.<br/><br/> 가상 네트워크는 동일한 구독 및 리전에 있어야 합니다.

## <a name="vm-compute-support"></a>VM 컴퓨팅 지원

**계산** | **지원**
--- | ---
VM 크기 |CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM<br/><br/> [자세한 정보](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
[가용성 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)의 VM 백업 | 지원됩니다.<br/><br/> VM을 빠르게 만드는 옵션을 사용하여 사용 가능한 집합에서 VM을 복원할 수 없습니다. 대신 VM을 복원할 때 디스크를 복원하고 이를 사용하여 VM을 배포하거나 디스크를 복원하고 기존 디스크를 교체하는 데 사용합니다.
[HUB(하이브리드 사용 혜택)와](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 함께 배포된 VM 백업 | 지원됩니다.
[스케일 세트에](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) 배포된 VM 백업 |지원되지 않습니다.
[Azure 마켓플레이스에서](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images) 배포된 VM 백업<br/><br/> (마이크로소프트, 제3자에 의해 게시) |지원됩니다.<br/><br/> VM에서 지원되는 운영 체제도 실행하고 있어야 합니다.<br/><br/> VM에서 파일을 복구할 경우 호환되는 OS(이전 또는 이후 OS 아님)로만 복원할 수 있습니다. 이러한 요구는 구매 정보가 필요하지만 디스크로만 사용하므로 VM으로 백업되는 Azure Marketplace VM은 복원하지 않습니다.
사용자 지정 이미지에서 배포된 VM 백업(타사) |지원됩니다.<br/><br/> VM에서 지원되는 운영 체제도 실행하고 있어야 합니다.<br/><br/> VM에서 파일을 복구할 경우 호환되는 OS(이전 또는 이후 OS 아님)로만 복원할 수 있습니다.
Azure로 마이그레이션된 VM 백업| 지원됩니다.<br/><br/> VM을 백업하려면 VM 에이전트를 마이그레이션된 컴퓨터에 설치해야 합니다.
멀티 VM 일관성 백업 | Azure Backup은 여러 VM에서 데이터 및 응용 프로그램 일관성을 제공하지 않습니다.
진단 [설정을](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) 통한 백업  | 지원 안 됨 <br/><br/> [새 만들기](backup-azure-arm-restore-vms.md#create-a-vm) 옵션을 사용하여 진단 설정을 사용하여 Azure VM의 복원이 트리거되면 복원이 실패합니다.
영역 고정 VM 복원 | 지원됩니다(2019년 1월 이후에 백업되고 [가용성 영역을](https://azure.microsoft.com/global-infrastructure/availability-zones/) 사용할 수 있는 VM에 대해).<br/><br/>현재 VM에 고정된 동일한 영역으로 복원을 지원합니다. 그러나 영역을 사용할 수 없는 경우 복원에 실패합니다.
Gen2 VM | 지원됨 <br> Azure 백업은 [Gen2 VM의](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)백업 및 복원을 지원합니다. 이러한 VM이 복구 지점에서 복원되면 [Gen2 VM으로](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/)복원됩니다.

## <a name="vm-storage-support"></a>VM 스토리지 지원

**구성 요소** | **지원**
--- | ---
Azure VM 데이터 디스크 수 | 16개 이하의 데이터 디스크로 VM을 백업합니다.<BR> 16개 이상의 디스크(최대 32개 디스크)를 사용하여 VM의 프라이빗 미리 보기에 등록하려면 AskAzureBackupTeam@microsoft.com으로 이메일을 보내주세요.
데이터 디스크 크기 | 개별 디스크 크기는 VM의 모든 디스크에 대해 최대 32TB, 최대 256TB까지 결합될 수 있습니다.
스토리지 유형 | 표준 HDD, 표준 SSD, 프리미엄 SSD.
관리 디스크 | 지원됩니다.
암호화된 디스크 | 지원됩니다.<br/><br/> Azure 디스크 암호화로 활성화된 Azure VM은 Azure AD 앱의 유무에 관계없이 백업할 수 있습니다.<br/><br/> 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 전체 VM을 복구해야 합니다.<br/><br/> Azure Backup에서 이미 보호되는 VM에 암호화를 사용하도록 설정할 수 있습니다.
Write Accelerator가 설정된 디스크 | 지원되지 않습니다.<br/><br/> Azure 백업은 백업 중에 쓰기 가속기를 사용하도록 설정한 디스크를 자동으로 제외합니다. 백업되지 않으므로 VM의 복구 지점에서 이러한 디스크를 복원할 수 없습니다.
중복 제거된 VM/디스크 복원& 백업 | Azure 백업은 중복 제거를 지원하지 않습니다. 자세한 내용은 이 [문서를](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) 참조하세요. <br/> <br/>  - Azure 백업복구 서비스 자격 증명 모음의 VM 에서 중복 제거되지 않음 <br/> <br/>  - 복원 중에 중복 제거 상태에 VM이 있는 경우 볼트가 형식을 이해하지 못하므로 파일을 복원할 수 없습니다. 그러나 전체 VM 복원을 성공적으로 수행할 수 있습니다.
보호된 VM에 디스크 추가 | 지원됩니다.
보호된 VM에서 디스크 크기 조정 | 지원됩니다.
공유 스토리지| 클러스터 공유 볼륨(CSV) 또는 확장 파일 서버를 사용하여 VM을 백업하는 것은 지원되지 않습니다. CSV 작성기는 백업 중에 실패할 수 있습니다. 복원 시 CSV 볼륨을 포함하는 디스크가 나오지 않을 수 있습니다.
[공유 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | 지원되지 않습니다.

## <a name="vm-network-support"></a>VM 네트워크 지원

**구성 요소** | **지원**
--- | ---
네트워크 인터페이스 수(NIC) | 특정 Azure VM 크기에서 지원되는 최대 NIC 수입니다.<br/><br/> 복원 프로세스 동안 VM이 만들어지면 NIC가 만들어집니다.<br/><br/> 복원된 VM의 NIC 수는 보호를 사용하도록 설정한 VM의 NIC 수를 미러링합니다. 보호를 사용하도록 설정한 후 NIC를 제거해도 개수에는 영향을 주지 않습니다.
외부/내부 부하 분산 장치 |지원됩니다. <br/><br/> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
여러 개의 예약된 IP 주소 |지원됩니다. <br/><br/> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
여러 네트워크 어댑터를 사용하는 VM| 지원됩니다. <br/><br/> 특수한 네트워크 설정을 사용하여 VM을 복원하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations).
공용 IP 주소가 있는 VM| 지원됩니다.<br/><br/> 기존 공용 IP 주소를 NIC와 연결하거나 주소를 만들고 복원이 완료된 후 NIC와 연결합니다.
NIC/서브넷의 NSG(네트워크 보안 그룹)입니다. |지원됩니다.
고정 IP 주소 | 지원되지 않습니다.<br/><br/> 복원 지점에서 만든 새 VM에는 동적 IP 주소가 할당됩니다.<br/><br/> 클래식 VM의 경우 예약된 IP 주소와 정의된 끝점이 없는 VM을 백업할 수 없습니다.
동적 IP 주소 |지원됩니다.<br/><br/> 소스의 NIC가 동적 IP 주소를 사용하는 경우 기본적으로 복원된 VM의 NIC도 이를 사용합니다.
Azure Traffic Manager| 지원됩니다.<br/><br/>백업된 VM이 트래픽 관리자에 있는 경우 복원된 VM을 동일한 트래픽 관리자 인스턴스에 수동으로 추가합니다.
Azure DNS |지원됩니다.
사용자 지정 DNS |지원됩니다.
HTTP 프록시를 통한 아웃바운드 연결 | 지원됩니다.<br/><br/> 인증된 프록시는 지원되지 않습니다.
가상 네트워크 서비스 엔드포인트| 지원됩니다.<br/><br/> 방화벽 및 가상 네트워크 저장소 계정 설정은 모든 네트워크에서 액세스를 허용해야 합니다.

## <a name="vm-security-and-encryption-support"></a>VM 보안 및 암호화 지원

Azure Backup은 전송 중 및 정지 상태의 데이터에 대한 암호화를 지원합니다.

Azure로의 네트워크 트래픽:

- 서버에서 복구 서비스 자격 증명 모음으로의 백업 트래픽은 고급 암호화 표준 256을 사용하여 암호화됩니다.
- 백업 데이터는 보안 HTTPS 링크를 통해 전송됩니다.
- 백업 데이터는 암호화된 형식으로 Recovery Services 자격 증명 모음에 저장됩니다.
- 사용자만 이 데이터의 잠금을 해제할 수 있는 암호를 보유합니다. Microsoft는 어떠한 경우에도 백업 데이터의 암호를 해독할 수 없습니다.

  > [!WARNING]
  > 볼트를 설정한 후에는 암호화 키에만 액세스할 수 있습니다. Microsoft는 복사본을 유지 관리할 수 없으며, 키에 대한 액세스 권한도 없습니다. 키를 잃어버리면 Microsoft에서 백업 데이터를 복구할 수 없습니다.

데이터 보안:

- Azure VM을 백업할 때 가상 시스템 *내에서* 암호화를 설정해야 합니다.
- Azure Backup은 Windows 가상 컴퓨터에서 BitLocker를 사용하고 Linux 가상 컴퓨터에서 **dm-crypt를** 사용하는 Azure 디스크 암호화를 지원합니다.
- 백 엔드에서 Azure Backup는 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 사용하여 미사용 데이터를 보호합니다.

**컴퓨터** | **전송 중** | **미사용**
--- | --- | ---
온-프레미스 Windows 머신(DPM/MABS 사용 안 함) | ![yes][green] | ![yes][green]
Azure VM | ![yes][green] | ![yes][green]
온-프레미스/Azure VM(DPM 사용) | ![yes][green] | ![yes][green]
온-프레미스/Azure VM(MABS 사용) | ![yes][green] | ![yes][green]

## <a name="vm-compression-support"></a>VM 압축 지원

백업은 다음 표에 요약된 백업 트래픽 압축을 지원합니다. 다음 사항에 유의하세요.

- Azure VM의 경우 VM 확장은 저장소 네트워크를 통해 Azure 저장소 계정에서 직접 데이터를 읽습니다. 이 트래픽을 압축할 필요는 없습니다.
- DPM 또는 MABS를 사용하는 경우 DPM/MABS에 백업하기 전에 데이터를 압축하여 대역폭을 절약할 수 있습니다.

**컴퓨터** | **MABS/DPM에 압축(TCP)** | **볼트로 압축(HTTPS)**
--- | --- | ---
온-프레미스 Windows 머신(DPM/MABS 사용 안 함) | 해당 없음 | ![yes][green]
Azure VM | 해당 없음 | 해당 없음
온-프레미스/Azure VM(DPM 사용) | ![yes][green] | ![yes][green]
온-프레미스/Azure VM(MABS 사용) | ![yes][green] | ![yes][green]

## <a name="next-steps"></a>다음 단계

- [Azure VM을 백업합니다.](backup-azure-arm-vms-prepare.md)
- 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
- Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
- Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
