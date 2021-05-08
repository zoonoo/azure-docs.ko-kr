---
title: MARS를 사용하여 볼륨의 모든 파일 복원
description: MARS 에이전트를 사용하여 볼륨에 있는 모든 파일을 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 1d04e9f77b9f92594def9381f973c999e96b2cb2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516504"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>MARS 에이전트를 사용하여 볼륨의 모든 파일 복원

이 문서에서는 MARS(Microsoft Azure Recovery Services) 에이전트에서 데이터 복구 마법사를 사용하여 전체 볼륨의 모든 백업 파일을 복원하는 방법을 설명합니다. 다음과 같습니다.

- 볼륨의 모든 백업 파일을 백업이 수행된 동일한 머신으로 복원합니다.
- 볼륨의 모든 백업 파일을 대체 머신으로 복원합니다.

>[!TIP]
>**볼륨** 옵션은 지정된 볼륨의 모든 백업 데이터를 복구합니다. 이 옵션은 더 빠른 전송 속도(최대 40MBps)를 제공하므로 대용량 데이터 또는 전체 볼륨을 복구하는 데 사용하는 것이 좋습니다.
>
>**개별 파일 및 폴더 옵션** 을 사용하면 복구 지점 데이터에 빠르게 액세스할 수 있습니다. 이 옵션은 개별 파일을 복구하는 데 적합하며 전체 크기 80GB 미만에 사용하는 것이 좋습니다. 복구하는 동안 최대 6MBps의 전송 또는 복사 속도를 제공합니다.

## <a name="volume-level-restore-to-the-same-machine"></a>동일한 머신에 대한 볼륨 수준 복원

다음 단계를 통해 볼륨의 모든 백업 파일을 복구할 수 있습니다.

1. **Microsoft Azure Backup** 스냅인을 엽니다. 스냅인이 설치된 위치를 모르는 경우 컴퓨터 또는 서버에서 **Microsoft Azure Backup** 을 검색합니다. 데스크톱 앱이 검색 결과에 나타나야 합니다.

1. 마법사를 시작하려면 **데이터 복구** 를 선택합니다.

    ![데이터 복구 메뉴](./media/restore-all-files-volume-mars/recover.png)

1. **시작** 페이지에서 동일한 서버 또는 컴퓨터로 데이터를 복원하려면 **이 서버(서버 이름)**  > **다음** 을 선택합니다.

    ![시작 페이지](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. **복구 모드 선택** 페이지에서 **볼륨** > **다음** 을 선택합니다.

    ![복구 모드 선택](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. **볼륨 및 날짜 선택** 페이지에서 복원하려는 볼륨을 선택합니다.

    달력에서 복구 지점을 선택합니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 단일 날짜에서 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다.

     ![볼륨 및 날짜 선택](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. **복구 옵션 지정** 페이지에서 복원 동작을 구성합니다.
    1. 복구 대상을 선택합니다.
        - **원래 위치**: 원래 경로에 데이터를 복원합니다.
        - **다른 위치**: 데이터를 복원할 대체 위치를 지정합니다.
    1. **백업에 포함된 항목이 복구 대상에 이미 있는 경우** 의 동작을 선택합니다.
        - **복사본을 만들어 두 버전 모두 유지**: 이름이 같은 파일이 이미 있으면 복구 지점의 데이터가 복사본으로 복원됩니다. 복사본에는 다음 형식 중 하나로 로컬 복원 작업 시간을 사용하는 지역화된 파일 이름 접두사가 있습니다.
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **복구된 버전으로 기존 버전 덮어쓰기**: 같은 이름의 파일이 이미 있는 경우 콘텐츠를 복구 지점의 데이터로 바꿉니다.
        - **복구 대상에 이미 있는 항목 복구 안 함**: 같은 이름의 파일이 이미 있으면 건너뜁니다.
    1. 복구 지점의 원래 권한으로 파일을 복원해야 하는 경우 **복구되는 파일 또는 폴더에 대한 ACL(액세스 제어 목록) 권한 복원을 사용하도록 설정합니다**.
        ![복구 옵션 지정](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. **확인** 창에서 복구 세부 정보를 확인하고 **복구** 를 선택합니다.

    ![확인 세부 정보](./media/restore-all-files-volume-mars/confirmation-details.png)

1. **복구 진행률** 페이지에서 복구 작업의 진행률을 모니터링합니다. 마법사를 안전하게 닫을 수 있으며 복구 작업은 백그라운드에서 계속됩니다. 대시보드에서 복구 작업을 두 번 클릭하여 진행률을 다시 볼 수 있습니다.

## <a name="volume-level-restore-to-an-alternate-machine"></a>대체 머신에 대한 볼륨 수준 복원

다음 단계를 통해 볼륨의 모든 백업 파일을 대체 머신으로 복구할 수 있습니다. 전체 서버가 손실된 경우 다음 단계를 사용하여 Azure Backup에서 데이터를 복구할 수 있습니다.

이러한 단계에는 다음과 같은 용어가 포함됩니다.

- *원본 머신* – 처음에 백업이 수행되었고 현재는 사용할 수 없는 원래 머신입니다.
- *대상 컴퓨터* – 데이터가 복구되는 컴퓨터입니다.
- *샘플 자격 증명 모음* – 원본 머신 및 대상 머신이 등록된 Recovery Services 자격 증명 모음입니다.

> [!NOTE]
> 이전 버전의 운영 체제를 실행 중인 대상 머신으로 백업을 복원할 수는 없습니다. 예를 들어, Windows 7 컴퓨터에서 가져온 백업은 Windows 7 이상의 컴퓨터에서 복원될 수 있습니다. Windows 10 컴퓨터에서 가져온 백업은 Windows 7 컴퓨터로 복원될 수 없습니다.

1. 대상 머신에서 **Microsoft Azure Backup** 스냅인을 엽니다.

1. 대상 머신 및 원본 머신이 동일한 Recovery Services 자격 증명 모음에 등록되었는지 확인합니다.

1. **데이터 복구** 를 선택하여 **데이터 복구 마법사** 를 엽니다.

    ![데이터 복구를 강조 표시한 Azure Backup의 스크린샷(대체 머신으로 복원)](./media/backup-azure-restore-windows-server/recover.png)

1. **시작** 페이지에서 **다른 서버** 를 선택합니다.

    ![데이터 복구 마법사 시작 페이지의 스크린샷(대체 머신으로 복원)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. 샘플 자격 증명 모음에 해당하는 자격 증명 모음 파일을 제공하고 **다음** 을 선택합니다.

    자격 증명 모음 파일이 잘못되었거나 만료된 경우 Azure Portal의 [샘플 자격 증명 모음에서 새 자격 증명 모음 파일을 다운로드](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-)합니다. 유효한 자격 증명 모음을 입력하면 해당하는 백업 자격 증명 모음의 이름이 나타납니다.

1. **백업 서버 선택** 페이지의 표시된 머신 목록에서 원본 머신을 선택하고 암호를 입력합니다. 그런 후 **다음** 을 선택합니다.

    ![데이터 복구 마법사, 백업 서버 선택 페이지의 스크린샷(대체 머신으로 복원)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. **복구 모드 선택** 페이지에서 **볼륨** > **다음** 을 선택합니다.

    ![복구 모드 선택](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. **볼륨 및 날짜 선택** 페이지에서 복원하려는 볼륨을 선택합니다.

    달력에서 복구 지점을 선택합니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 단일 날짜에서 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다.

     ![볼륨 및 날짜 선택](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. **복구 옵션 지정** 페이지에서 복원 동작을 구성합니다.
    1. 복구 대상을 선택합니다.
        - **원래 위치**: 원래 경로에 데이터를 복원합니다.
        - **다른 위치**: 데이터를 복원할 대체 위치를 지정합니다.
    1. **백업에 포함된 항목이 복구 대상에 이미 있는 경우** 의 동작을 선택합니다.
        - **복사본을 만들어 두 버전 모두 유지**: 이름이 같은 파일이 이미 있으면 복구 지점의 데이터가 복사본으로 복원됩니다. 복사본에는 다음 형식 중 하나로 로컬 복원 작업 시간을 사용하는 지역화된 파일 이름 접두사가 있습니다.
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **복구된 버전으로 기존 버전 덮어쓰기**: 같은 이름의 파일이 이미 있는 경우 콘텐츠를 복구 지점의 데이터로 바꿉니다.
        - **복구 대상에 이미 있는 항목 복구 안 함**: 같은 이름의 파일이 이미 있으면 건너뜁니다.
    1. 복구 지점의 원래 권한으로 파일을 복원해야 하는 경우 **복구되는 파일 또는 폴더에 대한 ACL(액세스 제어 목록) 권한 복원을 사용하도록 설정합니다**.
        ![복구 옵션 지정](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. **확인** 창에서 복구 세부 정보를 확인하고 **복구** 를 선택합니다.

    ![확인 세부 정보](./media/restore-all-files-volume-mars/confirmation-details.png)

1. **복구 진행률** 페이지에서 복구 작업의 진행률을 모니터링합니다. 마법사를 안전하게 닫을 수 있으며 복구 작업은 백그라운드에서 계속됩니다. 대시보드에서 복구 작업을 두 번 클릭하여 진행률을 다시 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 파일과 폴더를 복구했으므로 [백업을 관리](backup-azure-manage-windows-server.md)할 수 있습니다.
- [파일 및 폴더 백업에 관한 일반적인 질문](backup-azure-file-folder-backup-faq.yml)을 찾습니다.
