---
title: Azure Backup을 통해 MARS(Microsoft Azure Recovery Services) 에이전트를 실행하는 머신의 백업 매트릭스 지원
description: 이 문서에서는 Microsoft Azure Recovery Services (MARS) 에이전트를 실행 중인 컴퓨터를 백업할 때 Azure Backup 지원을 요약 합니다.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9799914cdabf1f64fccfd6bfd891f9498b860e39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922996"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트를 통한 백업 매트릭스 지원

사용할 수는 [Azure Backup 서비스](backup-overview.md) 온-프레미스 컴퓨터 및 앱을 백업 하 고 Azure virtual machines (Vm)를 백업 합니다. 이 문서에서는 Microsoft Azure Recovery Services (MARS) 에이전트를 사용 하 여 컴퓨터를 백업할 때 지원 설정 및 제한 사항 요약입니다.

## <a name="the-mars-agent"></a>MARS 에이전트

Azure 백업은 백업 Recovery Services 자격 증명 모음 Azure에서 온-프레미스 컴퓨터 및 Azure Vm에서 데이터를 백업 하려면 MARS 에이전트를 사용 합니다. MARS 에이전트는 다음 작업을 수행할 수 있습니다.
- Azure에서 백업 Recovery Services 자격 증명에 직접 백업할 수 있도록 온-프레미스 Windows 컴퓨터에서 실행 합니다.
- 자격 증명 모음에 직접 백업할 수 있도록 Windows Vm에서 실행 합니다.
- Microsoft Azure Backup Server (MABS) 또는 System Center Data Protection Manager (DPM) 서버에서 실행 합니다. 이 시나리오에서는 컴퓨터 및 워크 로드를 백업 또는 DPM 서버에서 MABS로 합니다. MARS 에이전트는 다음 Azure에서 자격 증명 모음에이 서버를 백업 합니다.

백업 옵션에는 에이전트가 설치 된 위치에 따라 달라 집니다. 자세한 내용은 [MARS 에이전트를 사용 하 여 Azure Backup 아키텍처](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)합니다. DPM 및 MABS 백업 아키텍처에 대 한 정보를 참조 하세요 [DPM 또는 MABS 백업](backup-architecture.md#architecture-back-up-to-dpmmabs)합니다. 도 참조 하세요 [요구 사항](backup-support-matrix-mabs-dpm.md) 백업 아키텍처에 대 한 합니다.

**설치** | **세부 정보**
--- | ---
최신 MARS 에이전트 다운로드 | 자격 증명 모음에서 최신 버전의 에이전트를 다운로드하거나 [직접 다운로드](https://aka.ms/azurebackup_agent)할 수 있습니다.
컴퓨터에 직접 설치 | 온-프레미스 Windows 서버에서 직접 또는 중 하나를 실행 하는 Windows VM에서 MARS 에이전트를 설치할 수 있습니다 합니다 [지원 되는 운영 체제](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)합니다.
백업 서버에 설치 | Azure에 백업하도록 DPM 또는 MABS를 설정할 때 MARS 에이전트를 서버에 다운로드하여 설치합니다. 에이전트에 설치할 수 있습니다 [지원 되는 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) 백업 서버 지원 매트릭스에 있습니다.

> [!NOTE]
> 기본적으로 Azure Vm 백업에 대해 설정 된 경우 Azure Backup 확장 설치 이 확장은 전체 VM을 백업합니다. 전체 VM이 아닌 특정 폴더와 파일을 백업하려면 MARS 에이전트를 확장과 함께 Azure VM에 설치하고 실행할 수 있습니다.
> Azure VM에서 MARS 에이전트를 실행 하면 파일 또는 VM에서 임시 저장소에 있는 폴더를 백업 합니다. 백업 파일 또는 폴더를 임시 저장소에서 제거 되 면 또는 임시 저장소에서 제거 된 경우 실패 합니다.


## <a name="cache-folder-support"></a>캐시 폴더 지원

MARS 에이전트를 사용 하 여 데이터를 백업 하는 경우 에이전트는 데이터의 스냅숏을 Azure에 데이터를 보내기 전에 로컬 캐시 폴더에 저장 합니다. (처음) 캐시 폴더에 몇 가지 요구 사항이 있습니다.

**캐시** | **세부 정보**
--- | ---
크기 |  캐시 폴더의 사용 가능한 공간에는 백업 데이터의 전체 크기의 5 ~ 10% 이상 이어야 합니다.
Location | 캐시 폴더는 백업 되는 컴퓨터에 로컬로 저장 해야 하 고 온라인 상태 여야 합니다. 네트워크 공유, 이동식 미디어 또는 볼륨을 오프 라인 캐시 폴더 아니어야 합니다.
폴더 | 캐시 폴더를 압축 하는 스파스, 재분석 지점이 있는 폴더 또는 중복 제거 된 볼륨에서 암호화 되어야 합니다.
위치 변경 | 백업 엔진을 중지 하 여 캐시 위치를 변경할 수 있습니다 (`net stop bengine`) 및 캐시 폴더를 새 드라이브로 복사 합니다. (새 드라이브에 공간이 충분 한지 확인 합니다.) 그런 다음 아래에 있는 두 개의 레지스트리 항목을 업데이트 **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** 고 **구성/CloudBackupProvider/ScratchLocation**)를 새 위치와 엔진 다시 시작 합니다.

## <a name="networking-and-access-support"></a>네트워킹 및 액세스 지원

### <a name="url-access"></a>URL 액세스

MARS 에이전트에서 액세스해야 하는 URL은 다음과 같습니다.

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>제한 지원

**기능** | **세부 정보**
--- | ---
대역폭 제어 | 지원됩니다. MARS 에이전트를 사용 하 여 **속성 변경** 대역폭을 조정 합니다.
네트워크 제한 | Windows Server 2008 R2, Windows Server 2008 SP2 또는 Windows 7을 실행 하는 백업 된 컴퓨터에 대해 사용할 수 없습니다.

## <a name="support-for-direct-backups"></a>직접 백업 지원

온-프레미스 컴퓨터 및 Azure Vm에서 실행 되는 일부 운영 체제에서 Azure로 바로 백업 하는 MARS 에이전트를 사용할 수 있습니다. 운영 체제는 64 비트 여야 합니다 및 최신 서비스 팩 및 업데이트를 실행 해야 합니다. 다음 표에서 이러한 운영 체제를 보여 줍니다.

**운영 체제** | **파일/폴더** | **시스템 상태**
--- | --- | ---
Windows 10(Enterprise, Pro, Home) | 예 | 아닙니다.
Windows 8.1(Enterprise, Pro)| 예 |아닙니다.
Windows 8(Enterprise, Pro) | 예 | 아닙니다.
Windows 7(Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | 예 | 아닙니다.
Windows Server 2016(Standard, Datacenter, Essentials) | 예 | 예
Windows Server 2012 R2(Standard, Datacenter, Foundation, Essentials) | 예 | 예
Windows Server 2012(Standard, Datacenter, Foundation) | 예 | 예
Windows Server 2008 R2(Standard, Enterprise, Datacenter, Foundation) | 예 | 예
Windows Server 2008 SP2(Standard, Datacenter, Foundation) | 예 | 아닙니다.
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | 예 | 아닙니다.

자세한 내용은 [DPM 및 MABS 지원 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)합니다.

## <a name="backup-limits"></a>Backup 제한

Azure Backup에 백업할 수 있는 파일 또는 폴더 데이터 원본 크기를 제한 합니다. 단일 볼륨에서 백업 하는 항목에는 다음 표에 요약 된 크기를 초과할 수 없습니다.

**운영 체제** | **크기 제한**
--- | ---
Windows Server 2012 이상 |  54,400GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 이상  | 54,400GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>백업에 지원되는 파일 형식

**형식** | **지원**
--- | ---
암호화   | 지원됩니다.
압축 | 지원됩니다.
스파스 | 지원됩니다.
압축 및 스파스 | 지원됩니다.
하드 링크  | 지원되지 않습니다. 건너뛰었습니다.
재분석 지점   | 지원되지 않습니다. 건너뛰었습니다.
암호화 및 스파스 |  지원되지 않습니다. 건너뛰었습니다.
압축된 스트림   | 지원되지 않습니다. 건너뛰었습니다.
스파스 스트림   | 지원되지 않습니다. 건너뛰었습니다.
OneDrive (동기화 된 파일이 스파스 스트림)  | 지원되지 않습니다.

## <a name="supported-drives-or-volumes-for-backup"></a>지원 되는 드라이브 또는 백업 볼륨

**드라이브/볼륨** | **지원** | **세부 정보**
--- | --- | ---
읽기 전용 볼륨   | 지원되지 않음 | 볼륨 섀도 서비스 (VSS 복사본) 볼륨이 쓰기 가능 하는 경우에 작동 합니다.
오프라인 볼륨 | 지원되지 않음 |   VSS는 볼륨은 온라인 하는 경우에 작동 합니다.
네트워크 공유   | 지원되지 않음 |   볼륨은 서버에서 로컬 이어야 합니다.
BitLocker로 보호 된 볼륨 | 지원되지 않음 |   백업을 시작 하기 전에 볼륨의 잠금 해제 해야 합니다.
파일 시스템 식별  | 지원되지 않음 |   NTFS만 지원 됩니다.
이동식 미디어 | 지원되지 않음 |   모든 백업 항목 원본은 있어야를 *고정* 상태입니다.
중복 제거된 드라이브 | 지원됨 | Azure Backup에서 중복 제거된 데이터를 일반 데이터로 변환합니다. 이 최적화, 암호화, 저장 및 자격 증명 모음에 데이터를 보냅니다.

## <a name="support-for-initial-offline-backup"></a>오프라인 초기 백업 지원

Azure Backup 지원 *오프 라인 시드* 디스크를 사용 하 여 Azure에 초기 백업 데이터를 전송 합니다. 이 지원은 초기 백업 크기 Tb (테라바이트)의 범위에 있을 경우에 유용 합니다. 오프라인 백업이 지원되는 대상은 다음과 같습니다.

- MARS 에이전트를 실행 하는 온-프레미스 컴퓨터에서 파일 및 폴더의 직접 백업 합니다.
- DPM 서버 또는 MABS에서 파일 및 워크로드 백업

시스템 상태 파일에 대 한 오프 라인 백업을 사용할 수 없습니다.

## <a name="support-for-data-restoration"></a>데이터 복원에 대 한 지원

사용 하 여 합니다 [즉시 복원을](backup-instant-restore-capability.md) 기능 Azure Backup의 데이터를 복원할 수는 자격 증명 모음에 복사 하기 전에 합니다. 백업 하는 컴퓨터 실행 하 고 있어야.NET Framework 4.5.2 이상.

백업은 이전 버전의 운영 체제를 실행 하는 대상 컴퓨터에 복원할 수 없습니다. 예를 들어, Windows 8 이상에서 Windows 7을 실행 하는 컴퓨터에서 가져온 백업은 복원할 수 있습니다. 하지만 Windows 8을 실행 하는 컴퓨터에서 가져온 백업은 Windows 7을 실행 하는 컴퓨터에 복원할 수 없습니다.

## <a name="next-steps"></a>다음 단계
- 에 대해 자세히 알아보세요 [MARS 에이전트를 사용 하는 아키텍처 백업](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)합니다.
- 무엇에 지원 되는 경우에 대해 알아봅니다 있습니다 [MABS 또는 DPM 서버에서 MARS 에이전트를 실행](backup-support-matrix-mabs-dpm.md)합니다.
