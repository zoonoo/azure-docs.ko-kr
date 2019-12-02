---
title: MARS 에이전트 백업 관리 및 모니터링
description: Azure Backup 서비스를 사용 하 여 MARS (Microsoft Azure Recovery Services) 에이전트 백업을 관리 하 고 모니터링 하는 방법에 대해 알아봅니다.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: f299bdeebab4f42721255d462101f0065a640fab
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665596"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure Backup 서비스를 사용 하 여 MARS (Microsoft Azure Recovery Services) 에이전트 백업 관리

이 문서에서는 Microsoft Azure Recovery Services 에이전트를 사용 하 여 백업 되는 파일 및 폴더를 관리 하는 방법을 설명 합니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

백업 정책은 복구 지점의 데이터 스냅숏을 만들고 복구 지점의 보존 기간을 지정 하는 시기를 지정 합니다. MARS 에이전트를 사용 하 여 백업 정책을 구성 합니다.

다음과 같이 정책을 만듭니다.

1. MARS 에이전트를 다운로드 하 고 등록 한 후 에이전트 콘솔을 시작 합니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.  
2. **작업**에서 **백업 예약**을 클릭 합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)
3. 백업 예약 마법사에서 **시작**> **다음**을 클릭 합니다.
4. **백업할 항목 선택**에서 **항목 추가**를 클릭 합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. **항목 선택**에서 백업할 항목을 선택 하 고 **확인**을 클릭 합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. **백업할 항목 선택** 페이지에서 **다음**을 클릭 합니다.
7. **백업 일정 지정** 페이지에서 매일 또는 매주 백업을 수행 하려는 경우를 지정 합니다. 그런 후 **Next** 를 클릭합니다.

    - 복구 지점은 백업을 수행할 때 생성 됩니다.
    - 사용자 환경에서 생성 되는 복구 지점의 수는 백업 일정에 따라 달라 집니다.

8. 하루에 최대 3 번까지 매일 백업을 예약할 수 있습니다. 예를 들어 스크린샷은 매일 자정에 하나씩, 오후 6:00 시에 하나씩 두 개의 일별 백업을 보여 줍니다.

    ![일별 일정](./media/backup-configure-vault/day-schedule.png)

9. 주별 백업만 실행할 수 있습니다. 예를 들어 스크린샷은 9:30 오전 시와 오전 1:00 시에 모든 대체 일요일 & 수요일에 수행 된 백업을 보여 줍니다.

    ![주별 일정](./media/backup-configure-vault/week-schedule.png)

10. **보존 정책 선택** 페이지에서 데이터의 기록 복사본을 저장 하는 방법을 지정 합니다. 그런 후 **Next** 를 클릭합니다.

    - 보존 설정은 저장 해야 하는 복구 지점과 저장 해야 하는 기간을 지정 합니다.
    - 예를 들어 일별 보존 설정을 설정 하는 경우 매일 보존에 지정 된 시간에 최신 복구 지점이 지정 된 일 수 동안 보존 됨을 표시 합니다. 또는 매월 30 일에 생성 된 복구 지점을 12 개월 동안 저장 하도록 표시 하는 월별 보존 정책을 지정할 수 있습니다.
    - 매일 및 매주 복구 지점 보존은 일반적으로 백업 일정과 일치 합니다. 백업이 일정에 따라 트리거될 경우 백업으로 만들어진 복구 지점은 매일 또는 매주 보존 정책에 지정 된 기간 동안 저장 됩니다.
    - 예를 들어 다음 스크린샷에서는 매일 자정 및 오후 6:00 시에 매일 백업을 7 일 동안 보관 합니다.
            -토요일 자정 및 오후 6:00에 수행 된 백업은 4 주 동안 유지 됩니다.
            -월의 마지막 주에 수행 된 토요일 자정 및 오후 6:00에 수행 된 백업은 12 개월 동안 유지 됩니다.
            -3 월의 마지막 주 토요일에 수행 된 백업은 10 년 동안 유지 됩니다.

    ![보존 예](./media/backup-configure-vault/retention-example.png)

11. **초기 백업 유형 선택** 에서 네트워크를 통해 초기 백업을 수행 하거나 오프 라인 백업을 사용할지 결정 합니다. 오프 라인 백업에 대 한 자세한 내용은이 [문서](backup-azure-backup-import-export.md)를 참조 하세요. 네트워크를 통해 초기 백업을 수행 하려면 **네트워크를 통해 자동으로** 를 선택 하 고 **다음**을 클릭 합니다.

    ![초기 백업 유형](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

12. **확인**에서 정보를 검토 한 다음 **마침**을 클릭 합니다.
    백업 유형을 확인 ![](./media/backup-azure-manage-mars/confirm-backup-type.png)

13. 마법사가 백업 일정 생성을 완료하면 **닫기**를 클릭합니다.
  백업 프로세스 수정 ![확인](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

에이전트가 설치 된 각 컴퓨터에서 정책을 만들어야 합니다.

## <a name="modify-a-backup-policy"></a>백업 정책 수정

백업 정책을 수정할 때 새 항목을 추가 하거나, 백업에서 기존 항목을 제거 하거나, 제외 설정을 사용 하 여 백업에서 파일을 제외할 수 있습니다.

- **항목 추가** 백업할 새 항목을 추가 하는 경우에만이 옵션을 사용 합니다. 기존 항목을 제거 하려면 **항목 제거** 또는 **제외 설정** 옵션을 사용 합니다.  
- **항목 제거** 이 옵션을 사용 하 여 백업에서 항목을 제거 합니다.
  - **항목을 제거**하는 대신 볼륨 내의 모든 항목을 제거 하는 데 **제외 설정을** 사용 합니다.
  - 볼륨의 모든 선택을 취소 하면 항목의 이전 백업이 수정 범위를 제외 하 고 마지막 백업 시 보존 설정에 따라 보존 됩니다.
  - 이러한 항목을 변경 하면 첫 번째 전체 백업이 수행 되 고 새 정책 변경 내용은 이전 백업에 적용 되지 않습니다.
  - 전체 볼륨을 선택을 취소 하면 보존 정책을 수정 하기 위한 범위 없이 이전 백업이 유지 됩니다.
- **제외 설정** 이 옵션을 사용 하 여 특정 항목을 백업에서 제외할 수 있습니다.
  
### <a name="add-new-items-to-existing-policy"></a>기존 정책에 새 항목 추가

1. **작업**에서 **백업 예약**을 클릭 합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)

2. **정책 항목 선택** 탭에서 **파일 및 폴더에 대 한 백업 일정 수정** 을 선택 하 고 **다음**을 클릭 합니다.

    ![정책 항목 선택](./media/backup-azure-manage-mars/select-policy-items.png)

3. **백업 일정 수정 또는 중지** 탭에서 **백업 항목 또는 시간 변경** 을 선택 하 고 **다음**을 클릭 합니다.

    ![백업 수정 또는 예약](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 백업할 **항목 선택** 탭에서 **항목 추가** 를 클릭 하 여 백업 하려는 항목을 추가 합니다.

    ![백업 수정 또는 예약 항목 추가](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **항목 선택** 창에서 추가 하려는 폴더 또는 폴더를 선택 하 고 **확인**을 클릭 합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-item.png)

6. 후속 단계를 완료 하 고 **마침** 을 클릭 하 여 작업을 완료 합니다.

### <a name="add-exclusion-rules-to-existing-policy"></a>기존 정책에 제외 규칙 추가

제외 규칙을 추가 하 여 백업 하지 않을 파일 및 폴더를 건너뛸 수 있습니다. 새 정책을 정의 하거나 기존 정책을 수정 하는 동안이 작업을 수행할 수 있습니다.

1. 작업 창에서 **백업 예약**을 클릭 합니다. **백업할 항목 선택** 으로 이동 하 고 **제외 설정**을 클릭 합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **제외 설정**에서 **제외 추가**를 클릭 합니다.

    ![항목 선택](./media/backup-azure-manage-mars/add-exclusion.png)

3. **제외할 항목 선택**에서 파일 및 폴더를 찾아 제외 하려는 항목을 선택 하 고 **확인**을 클릭 합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 기본적으로 선택 된 폴더 내의 모든 **하위 폴더** 는 제외 됩니다. **예** 또는 **아니요**를 선택 하 여이를 변경할 수 있습니다. 다음과 같이를 편집 하 고 제외할 파일 형식을 지정할 수 있습니다.

    ![항목 선택](./media/backup-azure-manage-mars/subfolders-type.png)

5. 후속 단계를 완료 하 고 **마침** 을 클릭 하 여 작업을 완료 합니다.

### <a name="remove-items-from-existing-policy"></a>기존 정책에서 항목 제거

1. 작업 창에서 **백업 예약**을 클릭 합니다. **백업할 항목 선택**으로 이동 합니다. 목록에서 백업 일정에서 제거 하려는 파일 및 폴더를 선택 하 고 **항목 제거**를 클릭 합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> 정책에서 볼륨을 완전히 제거 하는 경우에는 주의 하십시오.  다시 추가 해야 하는 경우 새 볼륨으로 처리 됩니다. 다음 예약 된 백업에서는 증분 백업 대신 초기 백업 (전체 백업)을 수행 합니다. 나중에 항목을 일시적으로 제거 하 고 추가 해야 하는 경우에는 전체 백업 대신 증분 백업을 위해 **항목을 제거** 하는 대신 **제외 설정을** 사용 하는 것이 좋습니다.

2. 후속 단계를 완료 하 고 **마침** 을 클릭 하 여 작업을 완료 합니다.

## <a name="stop-protecting-files-and-folder-backup"></a>파일 및 폴더 백업 보호 중지

파일 및 폴더 백업 보호를 중지 하는 방법에는 다음 두 가지가 있습니다.

- **보호를 중지 하 고 백업 데이터를 보존**합니다.
  - 이 옵션은 보호에서 이후의 모든 백업 작업을 중지 합니다.
  - Azure Backup 서비스는 보존 정책에 따라 백업 된 복구 지점이 유지 됩니다.
  - 만료 되지 않은 복구 지점의 백업 데이터를 복원할 수 있습니다.
  - 보호를 다시 시작 하기로 결정 한 경우 *백업 일정 다시 사용* 옵션을 사용할 수 있습니다. 그 후에는 데이터가 새 보존 정책에 따라 보존 됩니다.
- **보호를 중지 하 고 백업 데이터를 삭제**합니다.
  - 이 옵션은 미래의 모든 백업 작업에서 데이터를 보호 하 고 모든 복구 지점이 삭제 되지 않도록 합니다.
  - *이 백업 항목에 대 한 데이터를 삭제 했습니다. 메시지가 포함 된 백업 데이터 삭제 경고 전자 메일을 받게 됩니다. 이 데이터는 14 일 동안 일시적으로 사용할 수 있으며, 그 이후에는 영구적으로 삭제* 되 고 권장 조치는 *14 일 이내에 백업 항목을 다시 보호 하 여 데이터를 복구 합니다.*
  - 보호를 다시 시작 하려면 삭제 작업 으로부터 14 일 이내에 다시 보호 하십시오.

### <a name="stop-protection-and-retain-backup-data"></a>보호 중지 및 백업 데이터 보존

1. MARS 관리 콘솔을 열고 **작업 창**으로 이동 하 여 **Backup 예약을 선택**합니다.
    예약 된 백업을 수정 하거나 중지 ![합니다.](./media/backup-azure-manage-mars/mars-actions.png)
1. **정책 항목 선택** 페이지에서 **파일 및 폴더에 대 한 백업 일정 수정** 을 선택 하 고 **다음**을 클릭 합니다.
    예약 된 백업을 수정 하거나 중지 ![합니다.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 예약 된 **백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용 중지를 선택 하지만 일정이 다시 활성화 될 때까지 저장 된 백업 유지**를 선택 합니다. 그다음에 **다음**을 선택합니다.  
    예약 된 백업을 수정 하거나 중지 ![합니다.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **예약 된 백업 일시 중지** 에서 정보를 검토 하 고 **완료** 를 클릭 하 ![예약 된 백업을 수정 하거나 중지 합니다.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **백업 프로세스 수정** 에서 일정 백업 일시 중지가 성공 상태 인지 확인 하 고 **닫기** 를 클릭 하 여 완료 합니다.

### <a name="stop-protection-and-delete-backup-data"></a>보호 중지 및 백업 데이터 삭제

1. MARS 관리 콘솔을 열고 **작업** 창으로 이동 하 여 **Backup 예약**을 선택 합니다.
2. 예약 된 **백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용 중지와 저장 된 백업 모두 삭제**를 선택 합니다. 그다음에 **다음**을 선택합니다.

    ![예약 된 백업 수정 또는 중지](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 예약 된 **백업 중지** 페이지에서 **마침**을 선택 합니다.

    ![예약 된 백업을 중지 합니다.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 수동으로 생성 해야 하는 보안 PIN (개인 식별 번호)을 입력 하 라는 메시지가 표시 됩니다. 이렇게 하려면 먼저 Azure Portal에 로그인 합니다.
5. **Recovery Services 자격 증명 모음** > **설정** > **속성**으로 이동 합니다.
6. **보안 PIN**아래에서 **생성**을 선택 합니다. 이 PIN을 복사 합니다. PIN은 5 분 동안만 유효 합니다.
7. 관리 콘솔에서 PIN을 붙여넣은 다음 **확인**을 선택 합니다.

    ![보안 PIN을 생성 합니다.](./media/backup-azure-delete-vault/security-pin.png)

8. **백업 변경 진행률** 페이지에 다음 메시지가 표시 됩니다. 삭제 된 *백업 데이터는 14 일 동안 보존 됩니다. 이 시간 후에는 백업 데이터가 영구적으로 삭제 됩니다.*  

    ![백업 인프라를 삭제 합니다.](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스 백업 항목을 삭제 한 후 포털에서 다음 단계를 수행 합니다.

## <a name="re-enable-protection"></a>보호 다시 사용

데이터를 유지 하 고 보호를 다시 시작 하기로 결정 한 경우 보호를 중지 한 경우 백업 정책 수정을 사용 하 여 백업 일정을 다시 사용 하도록 설정할 수 있습니다.

1. **작업** 에서 **백업 예약**을 선택 합니다.
1. **백업 일정 다시 사용을 선택 합니다. 백업 항목 또는 시간을 수정** 하 고 **다음**을 클릭할 수도 있습니다.
    ![백업 인프라를 삭제 합니다.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **백업할 항목 선택**에서 **다음**을 클릭 합니다.
    ![백업 인프라를 삭제 합니다.](./media/backup-azure-manage-mars/re-enable-next.png)
1. **백업 일정 지정**에서 백업 일정을 지정 하 고 **다음**을 클릭 합니다.
1. **보존 정책 선택**에서 보존 기간을 지정 하 고 **다음**을 클릭 합니다.
1. 마지막으로 **conformation** 화면에서 정책 세부 정보를 검토 하 고 **마침**을 클릭 합니다.

## <a name="next-steps"></a>다음 단계

- 지원 되는 시나리오 및 제한 사항에 대 한 자세한 내용은 [MARS의 지원 매트릭스](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)를 참조 하세요.
- [주문형 백업 정책 보존 동작](backup-configure-vault.md#on-demand-backup-policy-retention-behavior)에 대해 자세히 알아보세요.
