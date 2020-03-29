---
title: MARS 에이전트 백업 관리 및 모니터링
description: Azure 백업 서비스를 사용하여 MICROSOFT Azure 복구 서비스(MARS) 에이전트 백업을 관리하고 모니터링하는 방법을 알아봅니다.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c11d73edd32c197aac2cec58eeb1cc20e5c6a339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673262"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure 백업 서비스를 사용하여 Microsoft Azure 복구 서비스(MARS) 에이전트 백업 관리

이 문서에서는 Microsoft Azure 복구 서비스 에이전트에 백업된 파일 및 폴더를 관리하는 방법에 대해 설명합니다.

## <a name="modify-a-backup-policy"></a>백업 정책 수정

백업 정책을 수정할 때 새 항목을 추가하거나, 백업에서 기존 항목을 제거하거나, 제외 설정을 사용하여 백업되지 않도록 파일을 제외할 수 있습니다.

- **항목을 추가하려면** 백업할 새 항목을 추가하는 경우에만 이 옵션을 사용합니다. 기존 항목을 제거하려면 **항목 제거** 또는 제외 **설정** 옵션을 사용합니다.  
- **항목 제거이** 옵션을 사용하여 백업되는 항목을 제거합니다.
  - **제외 설정을** 사용하여 항목 제거 대신 볼륨 내의 모든 항목을 **제거합니다.**
  - 볼륨의 모든 선택 항목을 선택하면 항목의 이전 백업이 수정 범위 없이 마지막 백업 시 보존 설정에 따라 유지됩니다.
  - 이러한 항목을 다시 선택하면 첫 번째 전체 백업이 발생하며 새 정책 변경 사항은 이전 백업에 적용되지 않습니다.
  - 전체 볼륨을 선택 취소하는 것은 보존 정책을 수정할 수 있는 범위 없이 이전 백업을 유지합니다.
- **제외 설정은** 이 옵션을 사용하여 특정 항목이 백업되지 않도록 제외합니다.

### <a name="add-new-items-to-existing-policy"></a>기존 정책에 새 항목 추가

1. **작업에서** **백업 일정을**클릭합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)

2. **정책 항목** 선택 탭에서 파일 **및 폴더에 대한 백업 일정 수정을** 선택하고 **다음**을 클릭합니다.

    ![정책 항목 선택](./media/backup-azure-manage-mars/select-policy-items.png)

3. 백업 백업 탭 **수정 또는 중지에서** **백업 항목 또는 시간을 변경하고** **다음**을 클릭합니다.

    ![백업 수정 또는 예약](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. **백업 할 항목 선택** 탭에서 항목 **추가를** 클릭하여 백업할 항목을 추가합니다.

    ![백업 추가 항목 수정 또는 예약](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **항목 선택** 창에서 추가하려는 flies 또는 폴더를 선택하고 **확인을**클릭합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-item.png)

6. 이후 단계를 완료하고 **완료를** 클릭하여 작업을 완료합니다.

### <a name="add-exclusion-rules-to-existing-policy"></a>기존 정책에 제외 규칙 추가

제외 규칙을 추가하여 백업하지 않으려는 파일 및 폴더를 건너뛸 수 있습니다. 새 정책을 정의하거나 기존 정책을 수정하는 동안 이 작업을 수행할 수 있습니다.

1. 작업 창에서 백업 **예약**을 클릭합니다. 항목 **선택으로 이동하여 백업으로** 이동하여 **제외 설정을**클릭합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **제외 설정에서** **제외 추가를**클릭합니다.

    ![항목 선택](./media/backup-azure-manage-mars/add-exclusion.png)

3. **항목 선택에서 제외할 것,** 파일 및 폴더 를 찾아보고 제외할 항목을 선택하고 **확인을**클릭합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 기본적으로 선택한 폴더 내의 모든 **하위 폴더는** 제외됩니다. **예** 또는 **아니요를**선택하여 변경할 수 있습니다. 아래 와 같이 제외할 파일 형식을 편집하고 지정할 수 있습니다.

    ![항목 선택](./media/backup-azure-manage-mars/subfolders-type.png)

5. 이후 단계를 완료하고 **완료를** 클릭하여 작업을 완료합니다.

### <a name="remove-items-from-existing-policy"></a>기존 정책에서 항목 제거

1. 작업 창에서 백업 **예약**을 클릭합니다. 백업 항목 **선택으로 이동합니다.** 목록에서 백업 일정에서 제거할 파일 및 폴더를 선택하고 항목 **제거를**클릭합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> 정책에서 볼륨을 완전히 제거할 때는 주의해서 진행하십시오.  다시 추가해야 하는 경우 새 볼륨으로 처리됩니다. 다음 예약된 백업은 증분 백업 대신 초기 백업(전체 백업)을 수행합니다. 나중에 항목을 일시적으로 제거하고 추가해야 하는 경우 **항목 제거** 대신 **제외 설정을** 사용하여 전체 백업 대신 증분 백업을 보장하는 것이 좋습니다.

2. 이후 단계를 완료하고 **완료를** 클릭하여 작업을 완료합니다.

## <a name="stop-protecting-files-and-folder-backup"></a>파일 및 폴더 백업 보호 중지

파일 및 폴더 백업 보호를 중지하는 방법에는 두 가지가 있습니다.

- **보호를 중지하고 백업 데이터를 유지합니다.**
  - 이 옵션을 사용하면 이후의 모든 백업 작업이 보호되지 않습니다.
  - Azure 백업 서비스는 보존 정책에 따라 백업된 복구 지점을 유지합니다.
  - 만료되지 않은 복구 지점에 대해 백업된 데이터를 복원할 수 있습니다.
  - 보호를 다시 시작하려는 경우 *백업 일정 다시 활성화* 옵션을 사용할 수 있습니다. 그 후 새 보존 정책에 따라 데이터가 유지됩니다.
- **보호를 중지하고 백업 데이터를 삭제합니다.**
  - 이 옵션을 사용하면 이후의 모든 백업 작업이 데이터를 보호하지 못하게 되고 모든 복구 지점이 삭제됩니다.
  - 이 백업 항목에 대한 데이터가 삭제되었다는 메시지와 함께 백업 데이터 경고 이메일 삭제를 받게 *됩니다. 이 데이터는 14일 동안 일시적으로 사용할 수 있으며, 그 후에는 영구적으로 삭제되고* *14일 이내에 백업 항목을 다시 보호하는* 것이 좋습니다.
  - 보호를 다시 시작하려면 삭제 작업으로부터 14일 이내에 다시 보호하십시오.

### <a name="stop-protection-and-retain-backup-data"></a>보호 중지 및 백업 데이터 유지

1. MARS 관리 콘솔을 열고 **작업 창으로**이동하여 **백업 예약을 선택합니다.**

    ![예약된 백업을 수정하거나 중지합니다.](./media/backup-azure-manage-mars/mars-actions.png)
1. **정책 항목 선택** 페이지에서 파일 및 폴더의 백업 일정 수정을 선택하고 **다음**을 **클릭합니다.**

    ![예약된 백업을 수정하거나 중지합니다.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. **예약된 백업 수정 또는 중지** 페이지에서 이 백업 일정 사용 **중지를 선택하지만 일정이 다시 활성화될 때까지 저장된 백업을 유지합니다.** 그런 다음 **을 선택합니다.**

    ![예약된 백업을 수정하거나 중지합니다.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **일시 중지 예약된 백업에서** 정보를 검토하고 **완료를**클릭합니다.

    ![예약된 백업을 수정하거나 중지합니다.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **수정 백업 프로세스에서** 일정 백업 일시 중지가 성공 상태임을 확인하고 **닫기를** 클릭하여 완료합니다.

### <a name="stop-protection-and-delete-backup-data"></a>보호 중지 및 백업 데이터 삭제

1. MARS 관리 콘솔을 열고 **작업** 창으로 이동한 다음 **백업 예약을**선택합니다.
2. **예약된 백업 수정 또는 중지** 페이지에서 이 백업 일정 사용 중지를 선택하고 저장된 모든 **백업을 삭제합니다.** 그런 다음 **을 선택합니다.**

    ![예약된 백업을 수정하거나 중지합니다.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 예약된 백업 중지 페이지에서 **완료를** **선택합니다.**

    ![예약된 백업을 중지합니다.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 수동으로 생성해야 하는 보안 PIN(개인 식별 번호)을 입력하라는 메시지가 표시됩니다. 이렇게 하려면 먼저 Azure 포털에 로그인합니다.
5. 복구 **서비스 볼트** > **설정** > **속성으로**이동합니다.
6. **보안 PIN에서** **생성을**선택합니다. 이 PIN을 복사합니다. PIN은 5분 동안만 유효합니다.
7. 관리 콘솔에서 PIN을 붙여넣은 다음 **확인을**선택합니다.

    ![보안 PIN을 생성합니다.](./media/backup-azure-delete-vault/security-pin.png)

8. 백업 **진행률 수정** 페이지에는 *삭제된 백업 데이터가 14일 동안 보존된다는 메시지가 나타납니다. 이 시간이 지나면 백업 데이터가 영구적으로 삭제됩니다.*  

    ![백업 인프라를 삭제합니다.](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스 백업 항목을 삭제한 후 포털의 다음 단계를 따릅니다.

## <a name="re-enable-protection"></a>보호 재사용

데이터를 유지하면서 보호를 중지하고 보호를 다시 시작하기로 결정한 경우 백업 정책 수정을 사용하여 백업 일정을 다시 활성화할 수 있습니다.

1. **작업에서** **백업 예약을**선택합니다.
1. **백업 일정 다시 활성화를 선택합니다. 백업 항목 또는 시간을 수정하고** **다음**을 클릭할 수도 있습니다.<br>

    ![백업 인프라를 삭제합니다.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **백업할 항목 선택에서** **다음을 클릭합니다.**

    ![백업 인프라를 삭제합니다.](./media/backup-azure-manage-mars/re-enable-next.png)
1. **백업 일정 지정에서**백업 일정을 지정하고 **다음**을 클릭합니다.
1. **보존 정책 선택에서**보존 기간을 지정하고 다음 을 **클릭합니다.**
1. 마지막으로 **확인** 화면에서 정책 세부 정보를 검토하고 **완료를**클릭합니다.

## <a name="re-generate-passphrase"></a>암호 다시 생성

암호는 Azure에서 MARS 에이전트를 사용하여 온-프레미스 또는 로컬 컴퓨터를 백업하거나 복원하는 동안 데이터를 암호화하고 해독하는 데 사용됩니다. 암호를 분실했거나 잊어버린 경우 다음 단계에 따라 암호를 다시 생성할 수 있습니다(컴퓨터가 여전히 복구 서비스 자격 증명 모음에 등록되어 있고 백업이 구성된 경우).

- MARS 에이전트 콘솔에서 **> 작업 창** > **변경 속성으로** 이동합니다. 그런 다음 **암호화 탭으로**이동합니다.<br>
- **암호 변경 확인란을 선택합니다.**<br>
- 새 암호를 입력하거나 **암호 생성을**클릭합니다.
- **찾아보기를** 클릭하여 새 암호를 저장합니다.

    ![암호를 생성합니다.](./media/backup-azure-manage-mars/passphrase.png)
- **확인을** 클릭하여 변경 내용을 적용합니다.  복구 서비스 자격 증명 모음에 대한 Azure 포털에서 [보안 기능을](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) 사용하도록 설정하면 보안 PIN을 입력하라는 메시지가 표시됩니다. PIN을 받으려면 이 [문서에](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)나열된 단계를 따르십시오.<br>
- 포털에서 보안 PIN을 붙여넣은 다음 **확인을** 클릭하여 변경 내용을 적용합니다.<br>

    ![암호를 생성합니다.](./media/backup-azure-manage-mars/passphrase2.png)
- 암호가 원본 컴퓨터 이외의 대체 위치에 안전하게 저장되어 있는지 확인합니다. MARS 에이전트에 백업되는 컴퓨터가 여러 개인 경우 모든 암호를 추적합니다.


## <a name="next-steps"></a>다음 단계

- 지원되는 시나리오 및 제한 사항에 대한 자세한 내용은 [MARS 에이전트에 대한 지원 매트릭스를](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)참조하십시오.
- [온디맨드 백업 정책 보존 동작에](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)대해 자세히 알아봅니다.
