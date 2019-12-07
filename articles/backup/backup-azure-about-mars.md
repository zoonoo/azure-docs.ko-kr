---
title: MARS 에이전트 정보
description: MARS 에이전트가 백업 시나리오를 지 원하는 방법 알아보기
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897326"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>MARS (Microsoft Azure Recovery Services) 에이전트 정보

이 문서에서는 Azure Backup 서비스가 MARS (Microsoft Azure Recovery Services) 에이전트를 사용 하 여 온-프레미스 컴퓨터에서 Azure로 파일/폴더, 볼륨 또는 시스템 상태를 백업 및 복원 하는 방법을 설명 합니다.

MARS 에이전트는 다음과 같은 백업 시나리오를 지원 합니다.

![복구 서비스 자격 증명 모음 대시보드](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **파일 및 폴더**: Windows 파일 및 폴더를 선택적으로 보호 합니다.
- **볼륨 수준**: 컴퓨터의 전체 Windows 볼륨을 보호 합니다.
- **시스템 수준**: 전체 Windows 시스템 상태를 보호 합니다.

MARS 에이전트는 다음 복원 시나리오를 지원 합니다.

![복구 서비스 자격 증명 모음 대시보드](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **동일한 서버**: 백업이 원래 생성 된 서버와 동일 합니다.
    -    **파일 및 폴더**: 복원 하려는 개별 파일 및 폴더를 찾아 선택할 수 있습니다.
    -    **볼륨 수준**: 복원 하려는 볼륨과 복구 지점을 선택 하 고 동일한 위치 또는 동일한 컴퓨터의 다른 위치에 복원할 수 있습니다.  기존 파일의 복사본을 만들거나 기존 파일을 덮어쓰거나 기존 파일의 복구를 건너뛸 수 있습니다.
    -    **시스템 수준**: 시스템 상태 및 복구 지점을 선택 하 여 지정 된 위치에 있는 동일한 컴퓨터로 복원할 수 있습니다.


-   **대체 서버**: 다른 서버 (즉, 백업을 수행한 서버와 동일한 서버가 아님)
    -    **파일 및 폴더**: 복구 지점을 대상 컴퓨터로 복원 하려는 개별 파일 및 폴더를 찾아 선택할 수 있습니다.
    -    **볼륨 수준**: 기존 파일의 복사본을 만들거나 기존 파일을 덮어쓰거나 기존 파일의 복구를 건너뛰어 대체 위치로 복원 하려는 볼륨 및 복구 지점을 선택할 수 있습니다.
    -    **시스템 수준**: 시스템 상태 및 복구 지점을 선택 하 여 시스템 상태 파일을 대체 컴퓨터로 복원할 수 있습니다.

## <a name="backup-process"></a>백업 프로세스

1.  Azure Portal에서 [복구 서비스 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) 을 만들고 백업 목표에서 파일 및 폴더 및/또는 시스템 상태를 선택 합니다.
2.  온-프레미스 컴퓨터에 복구 서비스 자격 증명 모음 자격 증명 및 에이전트 설치 관리자를 [다운로드](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) 합니다. 백업 옵션을 선택 하 여 온-프레미스 컴퓨터를 보호 하려면 파일 및 폴더 및 시스템 상태를 선택 하 고 MARS 에이전트를 다운로드 합니다.
3.  인프라 준비:

    a.    설치 관리자를 실행 하 여 에이전트를 [설치](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) 합니다.

    b.  다운로드 한 자격 증명 모음 자격 증명을 사용 하 여 Recovery Services 자격 증명 모음에 컴퓨터를 등록 합니다.
4.  클라이언트의 에이전트 콘솔에서 [백업 예약](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) 을 사용 하 여 백업을 구성 합니다. 백업 데이터의 보존 정책을 지정 하 고 보호를 시작 합니다.

![복구 서비스 자격 증명 모음 대시보드](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>추가 시나리오
-   **AZURE vm 내에서 특정 파일 및 폴더 백업** -azure vm (가상 머신)을 백업 하는 기본 방법은 vm에서 Azure Backup 확장을 사용 하는 것입니다. 이 경우 전체 VM을 백업합니다. VM 내의 특정 파일 및 폴더를 백업 하려는 경우 Azure Vm에 MARS 에이전트를 설치할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **오프 라인 시드** -Azure에 대 한 데이터의 초기 전체 백업-일반적으로 많은 양의 데이터를 전송 하 고 델타/증분만 전송 하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요 합니다. Azure Backup은 초기 백업을 압축합니다. 오프라인 시드의 프로세스를 통해 Azure Backup은 디스크를 사용하여 오프라인으로 압축된 초기 백업 데이터를 Azure에 업로드할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>다음 단계
[MARS 에이전트 지원 매트릭스](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[FAQ-MARS 에이전트](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
