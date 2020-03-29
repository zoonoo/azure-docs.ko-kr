---
title: 화성 에이전트 소개
description: MARS 에이전트가 백업 시나리오를 지원하는 방법 알아보기
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673285"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>마이크로소프트 Azure 복구 서비스 (MARS) 에이전트 정보

이 문서에서는 Azure Backup 서비스가 Microsoft Azure 복구 서비스(MARS) 에이전트를 사용하여 온-프레미스 컴퓨터에서 Azure로 파일, 폴더 및 볼륨 또는 시스템 상태를 백업하고 복원하는 방법을 설명합니다.

MARS 에이전트는 다음과 같은 백업 시나리오를 지원합니다.

![MARS 백업 시나리오](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **파일 및 폴더**: Windows 파일 및 폴더를 선택적으로 보호합니다.
- **볼륨 수준**: 컴퓨터의 전체 Windows 볼륨을 보호합니다.
- **시스템 수준**: 전체 Windows 시스템 상태를 보호합니다.

MARS 에이전트는 다음과 같은 복원 시나리오를 지원합니다.

![화성 복구 시나리오](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **동일한 서버**: 백업이 원래 생성된 서버입니다.
  - **파일 및 폴더**: 복원할 개별 파일 및 폴더를 선택합니다.
  - **볼륨 수준**: 복원할 볼륨 및 복구 지점을 선택한 다음 동일한 위치 또는 동일한 컴퓨터의 대체 위치로 복원합니다.  기존 파일의 복사본을 만들거나, 기존 파일을 덮어쓰거나, 기존 파일 복구를 건너뜁니다.
  - **시스템 수준**: 지정된 위치에서 동일한 컴퓨터로 복원할 시스템 상태 및 복구 지점을 선택합니다.

- **대체 서버**: 백업이 수행된 서버가 아닌 서버입니다.
  - **파일 및 폴더**: 복구 지점을 대상으로 복원하려는 개별 파일 및 폴더를 선택합니다.
  - **볼륨 수준**: 다른 위치로 복원할 볼륨 및 복구 지점을 선택합니다. 기존 파일의 복사본을 만들거나, 기존 파일을 덮어쓰거나, 기존 파일 복구를 건너뜁니다.
  - **시스템 수준**: 시스템 상태 및 복구 지점을 선택하여 대체 시스템에 시스템 상태 파일로 복원합니다.

## <a name="backup-process"></a>백업 프로세스

1. Azure 포털에서 복구 [서비스 자격 증명 모음을](install-mars-agent.md#create-a-recovery-services-vault)만들고 백업 목표에서 파일, 폴더 및 시스템 상태를 선택합니다.
2. 온-프레미스 컴퓨터에 [복구 서비스 자격 증명 및 에이전트 설치 관리자를 다운로드합니다.](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent)

    백업 옵션을 선택하여 온-프레미스 컴퓨터를 보호하려면 파일, 폴더 및 시스템 상태를 선택한 다음 MARS 에이전트를 다운로드합니다.

3. 인프라 준비:

    a. 설치 관리자를 실행하여 [에이전트를 설치합니다.](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)

    b. 다운로드한 자격 증명을 사용하여 컴퓨터를 복구 서비스 자격 증명에 등록합니다.
4. 클라이언트의 에이전트 콘솔에서 [백업을 구성합니다.](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy) 백업 데이터의 보존 정책을 지정하여 보호를 시작합니다.

![Azure 백업 에이전트 다이어그램](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>추가 시나리오

- **Azure 가상 시스템 내에서 특정 파일 및 폴더 백업:** Azure 가상 시스템(VM)을 백업하는 기본 방법은 VM에서 Azure 백업 확장프로그램을 사용하는 것입니다. 확장은 전체 VM을 백업합니다. VM 내에서 특정 파일 및 폴더를 백업하려는 경우 Azure VM에 MARS 에이전트를 설치할 수 있습니다. 자세한 내용은 [아키텍처: 기본 제공 Azure VM 백업](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)을 참조하십시오.

- **오프라인 시드**: Azure에 대한 데이터의 초기 전체 백업은 일반적으로 많은 양의 데이터를 전송하고 더 많은 네트워크 대역폭이 필요합니다. 후속 백업은 델타 또는 증분 데이터 양만 전송합니다. Azure Backup은 초기 백업을 압축합니다. *오프라인 시드*프로세스를 통해 Azure Backup은 디스크를 사용하여 압축된 초기 백업 데이터를 Azure에 오프라인으로 업로드할 수 있습니다. 자세한 내용은 [Azure 데이터 상자를 사용하여 Azure 백업 오프라인 백업을](offline-backup-azure-data-box.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

[MARS 에이전트 지원 매트릭스](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[화성 에이전트 FAQ](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
