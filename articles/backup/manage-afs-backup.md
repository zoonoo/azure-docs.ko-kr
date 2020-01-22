---
title: Azure 파일 공유 백업 관리
description: 이 문서에서는 Azure Backup 서비스에 의해 백업 되는 Azure 파일 공유를 관리 및 모니터링 하는 일반적인 작업에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294553"
---
# <a name="manage-azure-file-share-backups"></a>Azure 파일 공유 백업 관리

이 문서에서는 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) 서비스에 의해 백업 되는 Azure 파일 공유를 관리 및 모니터링 하는 일반적인 작업에 대해 설명 합니다. Recovery Services 자격 증명 모음에서 다음 관리 작업을 실행 하는 방법을 알아봅니다.

* [작업 모니터링](#monitor-jobs)
* [새 정책 만들기](#create-a-new-policy)
* [정책 수정](#modify-policy)
* [파일 공유에 대한 보호 중지](#stop-protection-on-a-file-share)
* [파일 공유에 대한 보호 다시 시작](#resume-protection-on-a-file-share)
* [백업 데이터 삭제](#delete-backup-data)
* [저장소 계정 등록 취소](#unregister-storage-account)

## <a name="monitor-jobs"></a>작업 모니터링

백업 또는 복원 작업을 트리거하는 경우 backup 서비스는 추적을 위한 작업을 만듭니다. **Backup 작업** 페이지에서 모든 작업의 진행률을 모니터링할 수 있습니다.

**Backup 작업** 페이지를 열려면:

1. 파일 공유에 대 한 백업을 구성 하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다. **개요** 블레이드의 **모니터링** 섹션에서 **Backup 작업** 을 클릭 합니다.

   ![모니터링 섹션의 백업 작업](./media/manage-afs-backup/backup-jobs.png)

2. 확인을 클릭 하면 모든 작업의 상태를 나열 하는 **Backup 작업** 블레이드가 표시 됩니다. 모니터링 하려는 파일 공유에 해당 하는 작업 이름을 클릭할 수 있습니다.

   ![작업 이름](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>새 정책 만들기

Recovery Services 자격 증명 모음의 **백업 정책** 섹션에서 Azure 파일 공유를 백업 하는 새 정책을 만들 수 있습니다. 파일 공유에 대 한 백업을 구성할 때 만든 모든 정책은 정책 유형이 Azure 파일 공유로 표시 됩니다.

기존 Backup 정책을 보려면:

1. 파일 공유에 대 한 백업을 구성 하는 데 사용한 Recovery Services 자격 증명 모음을 열고 Recovery Services 자격 증명 모음 메뉴의 관리 섹션에서 **백업 정책** 을 클릭 합니다. 자격 증명 모음에 구성 된 모든 백업 정책이 나열 됩니다.

   ![모든 백업 정책](./media/manage-afs-backup/all-backup-policies.png)

2. Azure 파일 공유와 관련 된 정책을 보려면 오른쪽 위에 있는 드롭다운에서 **Azure 파일 공유** 를 선택 합니다.

   ![Azure 파일 공유 선택](./media/manage-afs-backup/azure-file-share.png)

새 Backup 정책을 만들려면:

1. \+ 추가 백업 정책 블레이드에서 **+ 추가** 를 클릭 합니다.

   ![새 백업 정책](./media/manage-afs-backup/new-backup-policy.png)

2. **추가** 블레이드의 **정책 유형** 으로 **Azure 파일 공유** 를 선택 합니다. Azure 파일 공유에 대 한 Backup 정책 블레이드가 열립니다. 복구 지점의 정책 이름, 백업 빈도 및 보존 범위를 지정 합니다. 정책을 정의한 후 **확인을** 클릭 합니다.

   ![백업 정책 정의](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>정책 수정

백업 정책을 수정 하 여 백업 빈도 또는 보존 범위를 변경할 수 있습니다.

정책을 수정 하려면:

1. 파일 공유에 대 한 백업을 구성 하는 데 사용한 Recovery Services 자격 증명 모음을 열고 Recovery Services 자격 증명 모음 메뉴의 관리 섹션에서 **백업 정책** 을 클릭 합니다. 자격 증명 모음에 구성 된 모든 백업 정책이 나열 됩니다.

   ![자격 증명 모음의 모든 백업 정책](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Azure 파일 공유와 관련 된 정책을 보려면 오른쪽 위에 있는 드롭다운에서 **Azure 파일 공유** 를 선택 합니다. 수정 하려는 백업 정책을 클릭 합니다.

   ![수정할 Azure 파일 공유](./media/manage-afs-backup/azure-file-share-modify.png)

3. **일정** 블레이드가 열립니다. 필요에 따라 백업 일정/보존 범위를 편집 하 고 **저장**을 클릭 합니다. 블레이드에 "업데이트 진행 중" 메시지가 표시 되 고 정책 변경이 성공적으로 업데이트 되 면 "백업 정책을 업데이트 했습니다." 라는 메시지가 표시 됩니다.

   ![수정 된 정책 저장](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>파일 공유에 대한 보호 중지

Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

* 미래의 모든 백업 작업을 중지 하 고 *모든 복구 시점 삭제*
* 이후의 모든 백업 작업을 중지 *하 고 복구 지점은 그대로 두기*

Azure Backup에서 생성 된 기본 스냅숏이 유지 되므로 저장소에 복구 지점의 종료와 관련 된 비용이 있을 수 있습니다. 그러나 복구 지점을 유지할 경우 나중에 원할 때 파일 공유를 복원할 수 있다는 장점이 있습니다. 복구 지점을 유지하는 비용에 대한 자세한 내용은 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/backup/)를 참조하세요. 모든 복구 지점을 삭제하기로 선택하면 파일 공유를 복원할 수 없습니다.

Azure 파일 공유 보호를 중지하려면:

1. 파일 공유 복구 지점이 포함 된 Recovery Services 자격 증명 모음을 열고 보호 된 항목 섹션에서 **백업 항목** 을 클릭 합니다. Backup 항목 형식 목록이 표시됩니다.

   ![Backup 항목](./media/manage-afs-backup/backup-items.png)

2. **Backup 관리 유형** 목록에서 **Azure Storage(Azure 파일)** 를 선택합니다. **(Azure Storage (Azure Files))에 대 한 백업 항목** 의 목록이 표시 됩니다.

   ![Azure Storage (Azure Files) 선택](./media/manage-afs-backup/azure-storage-azure-files.png)

3. **백업 항목 목록 (Azure Storage (Azure Files))** 에서 보호를 중지할 백업 항목을 선택 합니다.

4. **백업 항목** 블레이드의 메뉴에서 **백업 중지** 옵션을 선택 합니다.

   ![백업 중지 선택](./media/manage-afs-backup/stop-backup.png)

5. 백업 **중지** 블레이드에서 **백업 데이터 보존** 또는 **백업 데이터 삭제** 를 선택 하 고 **백업 중지**를 클릭 합니다.

    ![백업 데이터 보존 또는 삭제를 선택 합니다.](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>파일 공유에 대한 보호 다시 시작

파일 공유에 대 한 보호가 중지 되었을 때 **백업 데이터 보존** 옵션을 선택한 경우 보호를 다시 시작할 수 있습니다. **Backup 데이터 삭제** 옵션을 선택한 경우 파일 공유 보호를 다시 시작할 수 없습니다.

Azure 파일 공유에 대 한 보호를 다시 시작 하려면:

1. 파일 공유 복구 지점이 포함 된 Recovery Services 자격 증명 모음을 열고 보호 된 항목 섹션에서 **백업 항목** 을 클릭 합니다. Backup 항목 형식 목록이 표시됩니다.

   ![재개할 백업 항목](./media/manage-afs-backup/backup-items-resume.png)

2. **Backup 관리 유형** 목록에서 **Azure Storage(Azure 파일)** 를 선택합니다. **(Azure Storage (Azure Files))에 대 한 백업 항목** 의 목록이 표시 됩니다.

   ![Azure Storage 목록 (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. **백업 항목 목록 (Azure Storage (Azure Files))** 에서 보호를 다시 시작 하려는 백업 항목을 선택 합니다.

4. **백업 항목** 블레이드의 메뉴에서 **백업 다시 시작** 옵션을 선택 합니다.

   ![백업 다시 시작을 선택 합니다.](./media/manage-afs-backup/resume-backup.png)

5. **Backup 정책** 블레이드가 열리고 선택한 정책을 선택 하 여 백업을 다시 시작할 수 있습니다.

6. 원하는 **백업 정책을**선택한 후 **저장** 을 클릭 합니다. 포털에 "업데이트 진행 중" 메시지가 표시 되 고 백업이 성공적으로 다시 시작 되 면 "보호 된 Azure 파일 공유에 대 한 백업 정책을 업데이트 했습니다." 라는 메시지가 표시 됩니다.

   ![백업 정책을 업데이트 했습니다.](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>백업 데이터 삭제

**백업 중지** 작업 중 또는 보호를 중지 한 이후 언제 든 지 파일 공유의 백업을 삭제할 수 있습니다. 복구 지점을 삭제하기 전에 며칠 또는 몇 주 기다리는 것이 도움이 될 수도 있습니다. 백업 데이터를 삭제할 때 삭제할 특정 복구 지점은 선택할 수 없습니다. 백업 데이터를 삭제 하도록 선택 하는 경우 파일 공유와 연결 된 모든 복구 지점이 삭제 됩니다.

다음 절차에서는 파일 공유에 대 한 보호가 중지 된 것으로 가정 합니다.

Azure 파일 공유에 대 한 백업 데이터를 삭제 하려면:

1. 백업 작업이 중지 되 면 백업 **항목** 대시보드에서 백업 **다시 시작** 및 **백업 데이터 삭제** 옵션을 사용할 수 있습니다. **Backup 항목** 블레이드의 메뉴에서 **백업 데이터 삭제** 옵션을 클릭 합니다.

   ![백업 데이터 삭제](./media/manage-afs-backup/delete-backup-data.png)

2. **백업 데이터 삭제** 블레이드가 열립니다. 삭제를 확인 하려면 파일 공유의 이름을 입력 합니다. 필요에 따라 삭제 하거나 **주석을**추가할 **이유** 를 제공 합니다. 백업 데이터를 삭제 하는 것이 확실 하면 **삭제** 를 클릭 합니다.

   ![데이터 삭제 확인](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>저장소 계정 등록 취소

다른 recovery services 자격 증명 모음을 사용 하 여 특정 저장소 계정에서 파일 공유를 보호 하려면 먼저 해당 저장소 계정의 [모든 파일 공유에 대 한 보호를 중지](#stop-protection-on-a-file-share) 합니다. 그런 다음 보호에 사용 되는 현재 recovery services 자격 증명 모음에서 계정을 등록 취소 합니다.

다음 절차에서는 등록을 취소 하려는 저장소 계정의 모든 파일 공유에 대 한 보호가 중지 된 것으로 가정 합니다.

저장소 계정을 등록 취소 하려면:

1. 저장소 계정이 등록 된 recovery services 자격 증명 모음을 엽니다.
2. **개요** 블레이드의 **관리** 섹션에서 **Backup 인프라** 옵션을 클릭 합니다.

   ![백업 인프라를 클릭 합니다.](./media/manage-afs-backup/backup-infrastructure.png)

3. **Backup 인프라** 블레이드가 열립니다. 이 블레이드의 **Azure Storage 계정** 섹션에서 **저장소 계정** 을 클릭 합니다.

   ![저장소 계정을 클릭 합니다.](./media/manage-afs-backup/storage-accounts.png)

4. **Storage 계정**을 클릭 하면 자격 증명 모음에 등록 된 저장소 계정 목록이 표시 됩니다.
5. 등록을 취소 하려는 storage 계정을 마우스 오른쪽 단추로 클릭 하 고 **등록 취소**를 선택 합니다.

   ![등록 취소 선택](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 파일 공유에 대 한 백업/복원 오류 문제 해결](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files) 을 참조 하세요.
