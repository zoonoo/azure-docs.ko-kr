---
title: Azure Backup 지원 매트릭스
description: Azure Backup 서비스에 대한 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb3a60995a4edfe5eb00f1a5e88812146816806a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883707"
---
# <a name="azure-backup-support-matrix"></a>Azure Backup 지원 매트릭스

[Azure Backup 서비스](backup-overview.md)를 사용하여 Microsoft Azure 클라우드에 데이터를 백업할 수 있습니다. 이 문서에서는 Azure Backup 시나리오 및 배포에 대한 지원 설정과 제한 사항이 요약되어 있습니다.

## <a name="vault-support"></a>자격 증명 모음 지원

Azure Backup은 Recovery Services 자격 증명 모음을 사용하여 백업을 오케스트레이션 및 관리하고 백업된 데이터를 저장합니다.

**설정** | **세부 정보**
--- | ---
자격 증명 모음의 수 | 단일 구독에서 최대 500개의 Recovery Services 자격 증명 모음입니다.
자격 증명 모음의 머신 수 | 단일 자격 증명 모음에서 최대 1,000개의 Azure VM입니다.<br/><br/> Azure Backup 에이전트(Microsoft Azure Recovery Services 에이전트(MABS))를 실행하는 최대 50개의 온-프레미스 머신을 단일 자격 증명 모음에 등록할 수 있습니다.
자격 증명 모음 스토리지의 데이터 원본 | 최대 54,400GB입니다. Azure VM 백업에는 제한이 없습니다.
자격 증명 모음에 대한 백업 횟수 | Azure VM은 하루에 한 번, DPM/MABS에서 보호하는 머신은 하루에 두 번, MARS 에이전트를 사용하여 직접 백업되는 머신은 하루에 세 번입니다.  
자격 증명 모음 이동 | Recovery Services 자격 증명 모음을 이동하려면 비공개 미리 보기에 등록해야 합니다. 체험해 보려면 AskAzureBackupTeam@microsoft.com에 메일을 보내세요.
자격 증명 모음 간의 데이터 이동 | 자격 증명 모음 간의 백업된 데이터 이동은 지원되지 않습니다.
저장소 복제 유형 | 자격 증명 모음에 대한 스토리지 복제 유형(GRS/LRS)은 백업을 저장하기 전에 수정할 수 있습니다. 자격 증명 모음에서 백업이 시작되면 복제 유형을 수정할 수 없습니다.



## <a name="on-premises-backup-support"></a>온-프레미스 백업 지원

온-프레미스 머신을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**머신** | **위치**: | **백업** | **기능**
--- | --- | --- | ---
**Windows 실제/가상(백업 서버 없음)** | 파일, 폴더, 시스템 상태 | Recovery Services 자격 증명 모음에 백업합니다. | 하루에 세 번 백업합니다.<br/><br/> 앱 인식 백업이 없습니다.<br/><br/> 파일, 폴더, 볼륨을 복원합니다.
**Linux 실제/가상(백업 서버 없음)** | 백업이 지원되지 않습니다.
**실제/가상(DPM 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | DPM(DPM 서버 또는 테이프에 로컬로 연결된 디스크)에 백업합니다.<br/><br/> 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/>. Oracle은 지원되지 않음
**실제/가상(MABS 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS(MABS 서버에 로컬로 연결된 디스크)에 백업합니다. 테이프는 지원되지 않습니다.<br/><br/> 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/>. Oracle은 지원되지 않음


## <a name="azure-vms"></a>Azure VM

### <a name="azure-vm-limits"></a>Azure VM 제한

**제한** | **세부 정보**
--- | ---
Azure VM 데이터 디스크 수 | 16개로 제한됩니다.
Azure VM 데이터 디스크 크기 | 개별 디스크의 크기는 최대 4,095GB입니다.


### <a name="azure-vm-backup-options"></a>Azure VM 백업 옵션

Azure VM을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**머신** | **위치**: | **백업** | **기능**
--- | --- | --- | ---
**Azure VM(백업 서버 없음)** | 파일, 폴더, 시스템 상태 | 자격 증명 모음에 백업합니다. | 하루에 한 번 백업합니다.<br/><br/> Windows VM의 경우 앱 인식 백업이고, Linux VM의 경우 파일 일치 백업입니다. 사용자 지정 스크립트를 사용하여 Linux 머신에 대한 앱 일관성을 구성할 수 있습니다.<br/><br/> VM 디스크를 복원합니다.<br/><br/> Azure VM에서 온-프레미스 위치에 백업할 수 없습니다.
**Azure VM(DPM 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | Azure에서 실행되는 DPM(DPM 서버에 로컬로 연결된 디스크)에 백업합니다. 테이프는 지원되지 않습니다.<br/><br/> 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/>. Oracle은 지원되지 않음
**Azure VM(MABS 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | Azure에서 실행되는 MABS(MABS 서버에 로컬로 연결된 디스크)에 백업합니다. 테이프는 지원되지 않습니다.<br/><br/> 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/>. Oracle은 지원되지 않음





## <a name="linux-backup-support"></a>Linux 백업 지원

Linux 머신을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**Backup** | **Linux(Azure 인증)**
--- | ---
**온-프레미스 Linux 머신(DPM 또는 MABS 사용 안 함)** |  아니요. MARS 에이전트는 Windows 머신에만 설치할 수 있습니다.
**Azure VM(DPM 또는 MABS 사용 안 함)** | [사용자 지정 스크립트](backup-azure-linux-app-consistent.md)를 사용하는 앱 일치 백업입니다.<br/><br/> 파일 수준 복구입니다.<br/><br/> 복구 지점 또는 디스크에서 VM을 만들어 복원합니다.
**온-프레미스 머신/Azure VM(DPM 사용)** | Hyper-V 및 VMWare에서 일관성 있는 Linux 게스트 VM 파일 백업<br/><br/> Hyper-V 및 VMWare Linux 게스트 VM의 VM 복원</br></br> Azure VM에서 파일 일치 백업을 사용할 수 없음
**온-프레미스 머신/Azure VM(MABS 사용)** | Hyper-V 및 VMWare에서 일관성 있는 Linux 게스트 VM 파일 백업<br/><br/> Hyper-V 및 VMWare Linux 게스트 VM의 VM 복원</br></br> Azure VM에서 파일 일치 백업을 사용할 수 없습니다.

## <a name="disk-support"></a>디스크 지원

디스크 중복 제거 지원은 다음과 같습니다.
- DPM 또는 MABS를 사용하여 Windows를 실행하는 Hyper-V VM을 백업하는 경우 온-프레미스에서 디스크 중복이 지원됩니다. Windows Server는 백업 저장소로 가상 머신에 연결된 VHD(가상 하드 디스크)에서 호스트 수준 중복 제거를 수행합니다.
- Azure에서는 백업 구성 요소에 대한 중복 제거가 지원되지 않습니다. System Center DPM 및 Backup 서버가 Azure에 배포되는 경우 VM에 연결된 스토리지 디스크의 중복은 제거할 수 없습니다.


## <a name="securityencryption-support"></a>보안/암호화 지원

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



## <a name="compression-support"></a>압축 지원

백업은 아래 표에 요약된 대로 백업 트래픽의 압축을 지원합니다. 다음 사항에 유의하세요.

- Azure VM의 경우 VM 확장은 Azure 스토리지 계정에서 스토리지 네트워크를 통해 직접 데이터를 읽으므로 이 트래픽을 압축할 필요가 없습니다.
- DPM 또는 MABS를 사용하는 경우 대역폭을 절약하기 위해 DPM/MABS에 백업하기 전에 데이터를 압축할 수 있습니다.

**머신** | **MABS/DPM에 압축(TCP)** | **자격 증명 모음에 압축(HTTPS)**
--- | --- | ---
온-프레미스 Windows 머신(DPM/MABS 사용 안 함) | 해당 없음 | 예
Azure VM | 해당 없음 | 해당 없음
온-프레미스/Azure VM(DPM 사용) | ![예][green] | ![예][green]
온-프레미스/Azure VM(MABS 사용) | ![예][green] | ![예][green]



## <a name="retention-limits"></a>보존 제한

**설정** | **제한**
--- | ---
보호된 인스턴스(머신/워크로드)당 최대 복구 지점 수 | 9999
복구 지점에 대한 최대 만료 시간 | 제한 없음
DPM/MABS에 대한 최대 백업 빈도 | SQL Server에 대해 15분마다<br/><br/> 다른 워크로드의 경우 한 시간에 한 번
자격 증명 모음에 대한 최대 백업 빈도 | MARS를 실행하는 온-프레미스 Windows 머신/Azure VM: 하루에 세 번<br/><br/> DPM/MABS: 하루에 두 번<br/><br/> Azure VM 백업: 하루 한 번
복구 지점 보존 | 매일, 매주, 매월, 매년
최대 보존 기간 | 백업 빈도에 따라 다름
DPM/MABS 디스크의 복구 지점 수 | 파일 서버의 경우 64개, 앱 서버의 경우 448개<br/><br/> 온-프레미스 DPM에 대한 테이프 복구 지점 수는 제한되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure VM 백업](backup-azure-arm-vms-prepare.md)
- 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
- Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
- Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
