---
title: Azure 파일 공유 복원
description: Azure Portal를 사용 하 여 Azure Backup 서비스에서 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294397"
---
# <a name="restore-azure-file-shares"></a>Azure 파일 공유 복원

이 문서에서는 Azure Portal를 사용 하 여 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) 서비스에서 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원 하는 방법을 설명 합니다.

이 가이드에서는 다음 방법에 대해 알아봅니다.

* 전체 Azure 파일 공유 복원
* 개별 파일 또는 폴더 복원
* 복원 작업 상태 추적

## <a name="steps-to-perform-restore"></a>복원을 수행 하는 단계

### <a name="select-the-file-share-to-restore"></a>복원할 파일 공유를 선택 하십시오.

1. [Azure Portal](https://portal.azure.com/)에서 파일 공유에 대 한 백업을 구성 하는 데 사용한 Recovery Services 자격 증명 모음을 엽니다.

2. **개요** 블레이드의 **보호 된 항목** 섹션에서 **백업 항목** 을 클릭 합니다.

    ![백업 항목을 클릭 합니다.](./media/restore-afs/backup-items.png)

3. **백업 항목**을 클릭 하면 아래와 같이 모든 백업 관리 유형을 나열 하는 새 블레이드가 **개요** 블레이드 옆에 표시 됩니다.

    ![백업 관리 유형](./media/restore-afs/backup-management.png)

4. **백업 항목**의 **백업 관리 유형**에서 **Azure Storage (Azure Files)** 를 선택 합니다. 이 자격 증명 모음을 사용 하 여 백업 된 모든 파일 공유 및 해당 저장소 계정 목록이 표시 됩니다.

    ![모든 파일 공유 목록](./media/restore-afs/file-shares.png)

5. Azure 파일 공유 목록에서 복원 작업을 수행할 원하는 파일 공유를 선택 합니다.

### <a name="full-share-recovery"></a>전체 공유 복구

이 복원 옵션을 사용 하 여 원래 위치 또는 대체 위치에서 전체 파일 공유를 복원할 수 있습니다.

1. 복원할 [파일 공유 선택](#select-the-file-share-to-restore) 섹션의 5 단계에서 원하는 파일 공유를 선택한 후에 표시 되는 **백업 항목** 블레이드에서 **공유 복원** 옵션을 선택 합니다.

   ![공유 복원 선택](./media/restore-afs/restore-share.png)

2. **공유 복원**을 클릭 **하면 복원** **지점** 메뉴가 열리고 선택한 파일 공유에 사용할 수 있는 복원 지점 목록이 표시 됩니다.

3. 복원 작업을 수행 하는 데 사용할 복원 지점을 선택 하 고 **확인**을 클릭 합니다.

    ![복원 지점 선택](./media/restore-afs/restore-point.png)

4. 확인을 클릭 하면 복원 블레이드 메뉴가 **복원 위치로**전환 됩니다. **복원 위치**에서 데이터를 복원할 위치 (또는 방법)를 지정 합니다. 두 가지 옵션 중에서 선택할 수 있습니다.

    * **원본 위치**: 원래 원본과 동일한 위치에 전체 파일 공유를 복원 합니다.
    * **대체 위치**: 전체 파일 공유를 대체 위치로 복원 하 고 원래 파일 공유를 그대로 유지 **합니다.**

#### <a name="restore-to-original-location"></a>원래 위치로 복원

1. **원본 위치** 를 **복구 대상** 으로 선택 하 고 충돌이 발생 하면 건너뛸지 아니면 덮어쓸지를 선택 합니다. 적절 한 선택을 수행한 후 **확인을** 클릭 합니다.

    ![원본 위치 선택](./media/restore-afs/original-location.png)

2. 복원 **을 클릭 하** 여 복원 작업을 시작 합니다.

    ![시작 하려면 복원을 클릭 합니다.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>대체 위치로 복원

1. 복구 대상으로 **대체 위치** 를 선택 합니다.
2. **저장소 계정** 필드의 드롭다운 메뉴에서 백업 된 콘텐츠를 복원 하려는 대상 저장소 계정을 선택 합니다.
3. 2 단계에서 선택한 저장소 계정에 따라 **파일 공유 선택** 드롭다운 메뉴에 선택한 저장소 계정에 있는 파일 공유의 목록이 표시 됩니다. 백업 된 콘텐츠를 복원 하려는 파일 공유를 선택 합니다.
4. **폴더 이름** 필드에서 복원 된 내용이 포함 된 대상 파일 공유에 만들려는 폴더 이름을 지정 합니다.
5. 충돌이 발생 했을 때 건너뛸지 아니면 덮어쓸지를 선택 합니다.
6. 모든 필드에 적절 한 값을 입력 한 후 **확인을** 클릭 합니다.

    ![대체 위치 선택](./media/restore-afs/alternate-location.png)

7. 복원을 클릭 하 여 복원 작업을 시작 합니다.

    ![시작 하려면 복원을 클릭 합니다.](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>항목 수준 복구

이 복원 옵션을 사용 하 여 원본 또는 대체 위치의 개별 파일 또는 폴더를 복원할 수 있습니다.

1. [복원할 파일 공유 선택](#select-the-file-share-to-restore) 섹션의 5 단계에서 원하는 파일 공유를 선택한 후 표시 되는 **백업 항목** 블레이드에서 **파일 복구** 옵션을 선택 합니다.

    ![파일 복구 선택](./media/restore-afs/file-recovery.png)

2. **파일 복구**를 클릭 **하면 복원** **지점** 메뉴가 열리고 선택한 파일 공유에 사용할 수 있는 복원 지점 목록이 표시 됩니다.

3. 복원 작업을 수행 하는 데 사용할 복원 지점을 선택 하 고 **확인**을 클릭 합니다.

    ![복원 지점 선택](./media/restore-afs/restore-point.png)

4. 확인을 클릭 하면 복원 블레이드 메뉴가 **복원 위치로**전환 됩니다. **복원 위치**에서 데이터를 복원할 위치 (또는 방법)를 지정 합니다. 두 가지 옵션 중에서 선택할 수 있습니다.

    * **원본 위치**: 선택한 파일/폴더를 원본 원본과 동일한 파일 공유로 복원 합니다.
    * **대체 위치**: 선택한 파일/폴더를 대체 위치로 복원 하 고 원래 파일 **공유 내용을 그대로**유지 합니다.

#### <a name="restore-to-original-location"></a>원래 위치로 복원

1. **원본 위치** 를 **복구 대상** 으로 선택 하 고 충돌이 발생 하면 건너뛸지 아니면 덮어쓸지를 선택 합니다.

    ![항목 수준 복구의 원본 위치](./media/restore-afs/original-location-item-level.png)

2. **파일 선택** 을 클릭 하 여 복원 하려는 파일/폴더를 선택 합니다.

    ![파일 선택을 클릭 합니다.](./media/restore-afs/select-file.png)

3. **파일 선택**을 클릭 하면 복원 하도록 선택한 파일 공유 복구 지점의 내용이 표시 되는 파일 공유 블레이드가 표시 됩니다.

4. 복원 하려는 파일/폴더에 해당 하는 상자를 선택 하 고 **선택**을 클릭 합니다.

    ![파일 또는 폴더 선택](./media/restore-afs/select-file-folder.png)

5. 2-4 단계를 반복 하 여 복원에 사용할 여러 파일/폴더를 선택 합니다.
6. 복원 하려는 모든 항목을 선택한 후 **확인**을 클릭 합니다.

    ![복원할 모든 항목을 선택한 후 확인을 클릭 합니다.](./media/restore-afs/after-selecting-items.png)

7. 복원을 클릭 하 여 복원 작업을 시작 합니다.

    ![시작 하려면 복원을 클릭 합니다.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. 복구 대상으로 **대체 위치** 를 선택 합니다.
2. **저장소 계정** 필드의 드롭다운 메뉴에서 백업 된 콘텐츠를 복원 하려는 대상 저장소 계정을 선택 합니다.
3. 2 단계에서 선택한 저장소 계정에 따라 **파일 공유 선택** 드롭다운 메뉴에 선택한 저장소 계정에 있는 파일 공유의 목록이 표시 됩니다. 백업 된 콘텐츠를 복원 하려는 파일 공유를 선택 합니다.
4. **폴더 이름** 필드에서 복원 된 내용이 포함 된 대상 파일 공유에 만들려는 폴더 이름을 지정 합니다.
5. 충돌이 발생 했을 때 건너뛸지 아니면 덮어쓸지를 선택 합니다.
6. **파일 선택** 을 클릭 하 여 복원 하려는 파일/폴더를 선택 합니다.

    ![대체 위치로 복원할 항목 선택](./media/restore-afs/restore-to-alternate-location.png)

7. **파일 선택**을 클릭 하면 복원 하도록 선택한 파일 공유 복구 지점의 내용이 표시 되는 파일 공유 블레이드가 표시 됩니다.
8. 복원 하려는 파일/폴더에 해당 하는 상자를 선택 하 고 **선택**을 클릭 합니다.

    ![복구 대상 선택](./media/restore-afs/recovery-destination.png)

9. 6-8 단계를 반복 하 여 복원에 사용할 여러 파일/폴더를 선택 합니다.
10. 복원 하려는 모든 항목을 선택한 후 **확인**을 클릭 합니다.

    [모든 파일을 선택한 후 확인을 클릭 합니다.](./media/restore-afs/after-selecting-all-items.png)

11. 복원 **을 클릭 하** 여 복원 작업을 시작 합니다.

## <a name="track-restore-operation"></a>복원 작업 추적

복원 작업이 트리거되면 백업 서비스에서 추적 작업을 만듭니다. Azure Backup에서 작업에 대한 알림을 포털에 표시합니다. 작업에 대한 작업을 보려면 알림 하이퍼링크를 클릭합니다.

![알림 하이퍼링크 클릭](./media/restore-afs/notifications-link.png)

Recovery services 자격 증명 모음에서 복원 진행률을 모니터링할 수도 있습니다. 복원 작업 상태를 확인 하는 단계는 다음과 같습니다.

1. 복원 작업을 트리거한 Recovery Services 자격 증명 모음을 엽니다.
2. **개요** 블레이드의 **모니터링 섹션** 에서 **Backup 작업** 을 클릭 하 여 다른 작업에 대해 실행 중인 작업의 상태를 확인 합니다.

    ![백업 작업을 클릭 합니다.](./media/restore-afs/backup-jobs.png)

3. 전송 된 데이터, 복원 된 파일 수 등의 복원 작업에 대 한 자세한 정보를 보려면 파일 공유에 해당 하는 작업 이름을 클릭 합니다.

    ![복원 된 세부 정보 보기](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유 백업을 관리](manage-afs-backup.md) 하는 방법 알아보기
