---
title: MARS 에이전트 정보
description: MARS 에이전트가 백업 시나리오를 지원하는 방법 알아보기
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9e01694aca386482f9ff7ba52593c88326ba3d62
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517745"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트 소개

이 문서에서는 Azure Backup 서비스가 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 온-프레미스 컴퓨터에서 Azure로 파일, 폴더 및 볼륨 또는 시스템 상태를 백업하고 복원하는 방법을 설명합니다.

## <a name="backup-scenarios"></a>백업 시나리오

MARS 에이전트는 다음과 같은 백업 시나리오를 지원합니다.

![MARS 백업 시나리오](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **파일 및 폴더**: Windows 파일 및 폴더를 선택적으로 보호합니다.
- **볼륨 수준**: 컴퓨터의 전체 Windows 볼륨을 보호합니다.
- **시스템 수준**: 전체 Windows 시스템 상태를 보호합니다.

### <a name="additional-scenarios"></a>추가 시나리오

- **Azure 가상 머신 내에서 특정 파일 및 폴더 백업**: Azure 가상 머신(VM)을 백업하는 기본 방법은 VM에서 Azure Backup 확장을 사용하는 것입니다. 이 확장은 전체 VM을 백업합니다. VM 내의 특정 파일 및 폴더를 백업하려면 Azure VM에 MARS 에이전트를 설치하면 됩니다. 자세한 내용은 [아키텍처: 기본 제공 Azure VM 백업](./backup-architecture.md#architecture-built-in-azure-vm-backup)을 참조하세요.

- **오프라인 시딩**: Azure에 대한 데이터의 초기 전체 백업은 일반적으로 많은 양의 데이터를 전송하며 더 많은 네트워크 대역폭이 필요합니다. 후속 백업에서는 델타 또는 증분 분량의 데이터만 전송합니다. Azure Backup은 초기 백업을 압축합니다. 오프라인 시딩의 프로세스를 통해 Azure Backup은 디스크를 사용하여 오프라인으로 압축된 초기 백업 데이터를 Azure에 업로드할 수 있습니다. 자세한 내용은 [Azure Data Box를 사용한 Azure Backup 오프라인 백업](offline-backup-azure-data-box.md)을 참조하세요.

## <a name="restore-scenarios"></a>복원 시나리오

MARS 에이전트는 다음과 같은 복원 시나리오를 지원합니다.

![MARS 복구 시나리오](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **동일한 서버**: 백업이 원래 생성된 서버입니다.
  - **파일 및 폴더**: 복원하려는 개별 파일 및 폴더를 선택합니다.
  - **볼륨 수준**: 복원하려는 볼륨 및 복구 지점을 선택합니다. 그런 다음 동일한 컴퓨터의 동일한 위치 또는 대체 위치에 복원합니다.  기존 파일의 복사본을 만들거나, 기존 파일을 덮어쓰거나, 기존 파일의 복구를 건너뜁니다.
  - **시스템 수준**: 시스템 상태 및 복구 지점을 선택하여 지정된 위치에 있는 동일한 컴퓨터에 복원합니다.

- **대체 서버**: 백업이 수행된 서버 이외의 서버입니다.
  - **파일 및 폴더**: 대상 컴퓨터에 복원하려는 복구 지점이 있는 개별 파일 및 폴더를 선택합니다.
  - **볼륨 수준**: 다른 위치에 복원하려는 볼륨 및 복구 지점을 선택합니다. 기존 파일의 복사본을 만들거나, 기존 파일을 덮어쓰거나, 기존 파일의 복구를 건너뜁니다.
  - **시스템 수준**: 시스템 상태 및 복구 지점을 선택하여 시스템 상태 파일을 대체 컴퓨터에 복원합니다.

## <a name="backup-process"></a>백업 프로세스

1. Azure Portal에서 [Recovery Services 자격 증명 모음](install-mars-agent.md#create-a-recovery-services-vault)을 만들고 **백업 목표** 에서 파일, 폴더 및 시스템 상태를 선택합니다.
2. [Recovery Services 자격 증명 모음 자격 증명 및 에이전트 설치 관리자](./install-mars-agent.md#download-the-mars-agent)를 온-프레미스 컴퓨터에 다운로드합니다.

3. [에이전트를 설치](./install-mars-agent.md#install-and-register-the-agent)하고 다운로드한 자격 증명 모음을 사용하여 Recovery Services 자격 증명 모음에 컴퓨터를 등록합니다.
4. 클라이언트의 에이전트 콘솔에서 [백업을 구성](./backup-windows-with-mars-agent.md#create-a-backup-policy)하여 백업할 항목, 백업 시기(일정), 백업이 Azure에 유지되는 기간(보존 정책)을 지정하고 보호를 시작합니다.

![Azure Backup 에이전트 다이어그램](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>추가 정보

- **초기 백업**(첫 번째 백업)은 백업 설정에 따라 실행됩니다.  MARS 에이전트는 VSS를 사용하여 백업을 위해 선택한 볼륨의 특정 시점 스냅샷을 만듭니다. 에이전트는 Windows 시스템 쓰기 작업만 사용하여 스냅샷을 캡처합니다. 애플리케이션 VSS 기록기를 사용하지 않으며 앱 일관성이 있는 스냅샷은 캡처하지 않습니다. VSS를 사용하여 스냅샷을 만든 후 MARS 에이전트는 백업을 구성할 때 지정한 캐시 폴더에 가상 하드 디스크(VHD)를 만듭니다. 에이전트는 또한 각 데이터 블록에 대한 체크섬을 저장합니다.

- **증분 백업**(후속 백업)은 지정된 일정에 따라 실행됩니다. 증분 백업 중에는 변경된 파일이 식별되고 새 VHD가 만들어집니다. VHD는 압축되고 암호화된 후 자격 증명 모음으로 전송됩니다. 증분 백업이 완료되면 새 VHD가 초기 복제 이후에 만든 VHD와 병합됩니다. 이 병합된 VHD는 지속적인 백업을 위한 비교에 사용할 최신 상태를 제공합니다.

- MARS 에이전트는 USN(업데이트 시퀀스 번호) 변경 저널을 사용하여 **최적화된 모드** 에서 백업 작업을 실행하거나 전체 볼륨 검사를 통해 디렉터리 또는 파일의 변경 내용을 확인하여 **최적화되지 않은 모드** 에서 백업 작업을 실행할 수 있습니다. 최적화되지 않은 모드에서는 에이전트가 볼륨의 각 파일을 검색하고 메타데이터와 비교하여 변경된 파일을 확인해야 하므로 속도가 더 느립니다.  **초기 백업** 은 항상 최적화되지 않은 모드에서 실행됩니다. 이전 백업이 실패하면 예약된 다음 백업 작업이 최적화되지 않은 상태로 실행됩니다. 이러한 모드와 모드를 확인하는 방법에 대한 자세한 내용은 [이 문서](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[MARS 에이전트 지원 매트릭스](./backup-support-matrix-mars-agent.md)

[MARS 에이전트 FAQ](./backup-azure-file-folder-backup-faq.yml)
