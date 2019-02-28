---
title: Azure Backup을 통해 MARS(Microsoft Azure Recovery Services) 에이전트를 실행하는 머신의 백업 매트릭스 지원
description: 이 문서에서는 MARS(Microsoft Azure Recovery Services) 에이전트를 실행하는 머신을 백업할 때의 Azure Backup 지원을 요약하고 있습니다.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430937"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트를 통한 백업 매트릭스 지원

[Azure Backup 서비스](backup-overview.md)를 사용하여 온-프레미스 머신과 애플리케이션 및 Azure VM을 백업할 수 있습니다. 이 문서에서는 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 머신을 백업하기 위한 지원 설정 및 제한 사항을 요약하고 있습니다.

## <a name="about-the-mars-agent"></a>MARS 에이전트 정보

MARS 에이전트는 Azure Backup에서 사용하여 온-프레미스 머신과 Azure VM의 데이터를 Azure의 백업 Recovery Services 자격 증명 모음에 백업합니다. MARS 에이전트는 다음과 같이 사용할 수 있습니다.
- 온-프레미스 Windows 머신에서 에이전트를 실행하여 Azure의 백업 Recovery Services 자격 증명 모음에 직접 백업할 수 있습니다.
- Windows Azure VM에서 에이전트를 실행하여 자격 증명 모음에 직접 백업할 수 있습니다.
- MABS(Microsoft Azure Backup Server) 또는 System Center DPM(Data Protection Manager) 서버에서 에이전트를 실행합니다. 이 시나리오에서는 머신과 워크로드를 MABS/DPM에 백업한 다음, MARS 에이전트를 사용하여 MABS/DPM을 Azure의 자격 증명 모음에 다시 백업합니다. 

에이전트가 설치된 위치에 따라 백업할 수 있는 항목이 달라집니다.

- MARS 에이전트를 사용하는 백업 아키텍처에 대해 [자세히 알아보세요](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders).
- MABS/DPM [백업 아키텍처](backup-architecture.md#architecture-back-up-to-dpmmabs) 및 [요구 사항](backup-support-matrix-mabs-dpm.md)에 대해 [자세히 알아보세요]().


## <a name="supported-installation"></a>지원되는 설치

**설치** | **세부 정보**
--- | ---
**최신 MARS 에이전트 다운로드** | 자격 증명 모음에서 최신 버전의 에이전트를 다운로드하거나 [직접 다운로드](https://aka.ms/azurebackup_agent)할 수 있습니다.
**머신에 직접 설치** | MARS 에이전트를 온-프레미스 Windows 서버 또는 [지원되는 운영 체제]() 중 하나를 실행하는 Windows Azure VM에 직접 설치할 수 있습니다.
**백업 서버에 설치** | Azure에 백업하도록 DPM 또는 MABS를 설정할 때 MARS 에이전트를 서버에 다운로드하여 설치합니다. 에이전트는 백업 서버 지원 매트릭스의 [지원되는 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)에 따라 설치할 수 있습니다.

> [!NOTE]
> 백업에 사용하도록 설정된 Azure VM에는 기본적으로 Azure Backup 확장 설치가 있습니다. 이 확장은 전체 VM을 백업합니다. 전체 VM이 아닌 특정 폴더와 파일을 백업하려면 MARS 에이전트를 확장과 함께 Azure VM에 설치하고 실행할 수 있습니다.
> Azure VM에서 MARS 에이전트를 실행하면 VM의 임시 스토리지에 있는 파일/폴더가 백업됩니다. 파일/폴더가 임시 스토리지에서 제거되거나 임시 스토리지가 제거되면 백업이 실패합니다.


## <a name="cache-folder-support"></a>캐시 폴더 지원

MARS 에이전트를 사용하여 백업하면 에이전트에서 데이터의 스냅숏을 만들어 로컬 캐시 폴더에 저장한 후에 이를 Azure로 보냅니다. 캐시(스크래치) 폴더에는 여러 가지 요구 사항이 있습니다.

**캐시** | **세부 정보**
--- | ---
**캐시 크기** |  캐시 폴더 크기의 사용 가능한 공간은 전체 백업 데이터 크기의 5~10% 이상이어야 합니다. 
**캐시 위치** | 캐시 폴더는 백업되는 머신에 로컬이어야 하며 온라인 상태여야 합니다.<br/><br/> 캐시 폴더는 네트워크 공유, 이동식 미디어 또는 오프라인 볼륨에 있으면 안 됩니다. 
**캐시 폴더** | 캐시 폴더는 중복 제거된 볼륨 또는 압축/스파스/재분석 지점의 폴더에서 암호화되어야 합니다.
**캐시 위치 수정** | 백업 엔진(net stop bengine)을 중지하고 캐시 폴더를 새 드라이브에 복사하여 캐시 위치를 변경할 수 있습니다(공간이 충분한지 확인). 그런 다음, HKLM\SOFTWARE\Microsoft\Windows Azure Backup 아래의 두 레지스트리 항목(Config/ScratchLocation 및 Config/CloudBackupProvider/ScratchLocation)을 새 위치로 업데이트하고 엔진을 다시 시작합니다.

## <a name="networking-and-access-support"></a>네트워킹 및 액세스 지원

### <a name="url-access"></a>URL 액세스

MARS 에이전트에서 액세스해야 하는 URL은 다음과 같습니다.

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>제한 지원

**기능** | **세부 정보**
--- | ---
대역폭 제어 | 지원됨<br/><br/> MARS 에이전트에서 **속성 변경**을 사용하여 대역폭을 조정합니다.
네트워크 제한 | Windows Server 2008 R2, Windows Server 2008 SP2 또는 Windows 7을 실행하는 백업된 머신에는 사용할 수 없습니다.

## <a name="support-for-direct-backups"></a>직접 백업 지원

다음 표에서는 MARS 에이전트를 사용하여 온-프레미스 머신 및 Azure VM에서 실행되는 운영 체제를 Azure에 직접 백업할 수 있는 기능을 요약하고 있습니다.

- 모든 운영 체제는 64비트입니다.
- 모든 운영 체제에서 최신 서비스 팩 및 업데이트를 실행해야 합니다.
- MARS 에이전트를 사용하여 백업할 수 있는 DPM 및 MABS 서버에 대한 자세한 내용은 [이 표](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)를 검토하세요.

**운영 체제** | **파일/폴더** | **시스템 상태**
--- | --- | ---
Windows 10(Enterprise, Pro, Home) | 예 | 아니요
Windows 8.1(Enterprise, Pro)| 예 |아니요
Windows 8(Enterprise, Pro) | 예 | 아니요
Windows 7(Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | 예 | 아니요
Windows Server 2016(Standard, Datacenter, Essentials) | 예 | 예
Windows Server 2012 R2(Standard, Datacenter, Foundation, Essentials) | 예 | 예
Windows Server 2012(Standard, Datacenter, Foundation) | 예 | 예
Windows Server 2008 R2(Standard, Enterprise, Datacenter, Foundation) | 예 | 예
Windows Server 2008 SP2(Standard, Datacenter, Foundation) | 예 | 아니요
Windows Storage Server 2016/2012 R2/2012(Standard, Workgroup) | 예 | 아니요


## <a name="backup-limits"></a>Backup 제한

Azure Backup은 백업할 수 있는 파일/폴더 데이터 원본의 크기를 제한합니다. 단일 볼륨에서 백업하도록 선택한 항목의 크기는 표에 요약된 크기를 초과할 수 없습니다.

**운영 체제** | **크기 제한**
--- | ---
Windows Server 2012 이상 |  54,400GB
Windows Server 2008 R2 SP1 |    1700GB
Windows Server 2008 SP2 | 1700GB
Windows 8 이상  | 54,400GB
Windows 7   | 1700GB


## <a name="supported-file-types-for-backup"></a>백업에 지원되는 파일 형식

**형식** | **지원됨** 
--- | --- 
암호화   | 예 
압축 | 예
스파스 | 예 
압축 및 스파스 | 예
하드 링크  | 지원되지 않음<br/><br/> 생략
재분석 지점   | 지원되지 않음<br/><br/> 생략
암호화 및 스파스 |  지원되지 않음<br/><br/> 생략
압축된 스트림   | 지원되지 않음<br/><br/> 생략
스파스 스트림   | 지원되지 않음<br/><br/> 생략
OneDrive(동기화된 파일이 스파스 스트림임)    | 지원되지 않음 

## <a name="supported-drivesvolumes-for-backup"></a>백업에 지원되는 드라이브/볼륨

**드라이브/볼륨** | **지원됨** | **세부 정보**
--- | --- | ---
읽기 전용 볼륨   | 지원되지 않음 | VSS가 작동하려면 볼륨이 쓰기 가능해야 합니다.
오프라인 볼륨 | 지원되지 않음 |   VSS가 작동하려면 볼륨이 온라인 상태여야 합니다.
네트워크 공유   | 지원되지 않음 |   볼륨이 백업 서버에서 로컬이어야 합니다.
BitLocker로 보호된 볼륨 | 지원되지 않음 |   백업이 작동하려면 먼저 볼륨의 잠금을 해제해야 합니다.
파일 시스템 식별  | 지원되지 않음 |   NTFS 전용
이동식 미디어 | 지원되지 않음 |   모든 백업 항목 원본의 상태가 '고정'이어야 합니다.
중복 제거된 드라이브 | 지원됩니다.<br/><br/> Azure Backup에서 중복 제거된 데이터를 일반 데이터로 변환합니다. 데이터를 최적화하고, 암호화하고, 저장하고, 자격 증명 모음으로 보냅니다.

## <a name="support-for-initial-offline-backup"></a>오프라인 초기 백업 지원

Azure Backup은 디스크를 사용하여 초기 백업 데이터를 Azure로 보내는 "오프라인 시딩"을 지원합니다. 이 기능은 초기 백업이 TB(테라바이트) 범위 내에 있을 가능성이 높은 경우에 유용합니다. 오프라인 백업이 지원되는 대상은 다음과 같습니다.

- MARS 에이전트를 실행하는 온-프레미스 머신의 파일 및 폴더 직접 백업
- DPM 서버 또는 MABS에서 파일 및 워크로드 백업
- 시스템 상태 파일에는 오프라인 백업을 사용할 수 없습니다.


## <a name="support-for-restore"></a>복원 지원

- Azure Backup의 새로운 [즉시 복원](/backup-instant-restore-capability.md) 릴리스를 사용하면 데이터를 자격 증명 모음에 복사하기 전에 복원할 수 있습니다.<br/><br/> 이 기능을 사용하려면 백업되는 머신에서 .NET Framework 4.5.2 이상을 실행하고 있어야 합니다.
- 이전 버전의 운영 체제를 실행하는 대상 시스템으로 백업을 복원할 수 없습니다. 예를 들어 Windows 7 컴퓨터에서 가져온 백업은 Windows 8 이상에서 복원할 수 있습니다. 그러나 Windows 8 컴퓨터에서 가져온 백업은 Windows 7 컴퓨터에서 복원할 수 없습니다.


## <a name="next-steps"></a>다음 단계
- MARS 에이전트를 사용하는 백업 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders).
- MABS(Microsoft Azure Backup Server) 또는 System Center DPM에서 MARS 에이전트를 실행할 때의 지원 사항에 대해 [알아봅니다](backup-support-matrix-mabs-dpm.md).


