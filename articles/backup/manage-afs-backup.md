---
title: Azure 파일 공유 백업 관리
description: 이 문서에서는 Azure Backup에서 백업하는 Azure 파일 공유를 관리하고 모니터링하기 위한 일반적인 작업에 대해 설명합니다.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247658"
---
# <a name="manage-azure-file-share-backups"></a>Azure 파일 공유 백업 관리

이 문서에서는 [Azure Backup에서](https://docs.microsoft.com/azure/backup/backup-overview)백업하는 Azure 파일 공유를 관리하고 모니터링하기 위한 일반적인 작업에 대해 설명합니다. 복구 서비스 자격 증명 모음에서 관리 작업을 수행하는 방법을 배웁니다.

## <a name="monitor-jobs"></a>작업 모니터링

백업 또는 복원 작업을 트리거하면 백업 서비스가 추적을 위한 작업을 만듭니다. **Backup 작업** 페이지에서 모든 작업의 진행률을 모니터링할 수 있습니다.

**Backup 작업** 페이지를 열려면:

1. 파일 공유에 대한 백업을 구성하는 데 사용한 복구 서비스 자격 증명 모음을 엽니다. **개요** 창에서 **모니터링** 섹션에서 **백업 작업을** 선택합니다.

   ![모니터링 섹션의 백업 작업](./media/manage-afs-backup/backup-jobs.png)

1. **확인을**선택하면 **백업 작업** 창에 모든 작업의 상태가 나열됩니다. 모니터링할 파일 공유에 해당하는 워크로드 이름을 선택합니다.

   ![워크로드 이름](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>새 정책 만들기

복구 서비스 자격 증명 모음의 **백업 정책** 섹션에서 Azure 파일 공유를 백업하는 새 정책을 만들 수 있습니다. 파일 공유에 대한 백업을 구성할 때 생성된 모든 정책은 **정책 유형이** **Azure 파일 공유로**표시됩니다.

기존 백업 정책을 보려면 다음을 수행하십시오.

1. 파일 공유에 대한 백업을 구성하는 데 사용한 복구 서비스 자격 증명 모음을 엽니다. 복구 서비스 자격 증명 모음 메뉴에서 **관리** 섹션에서 **백업 정책을** 선택합니다. 볼트에 구성된 모든 백업 정책이 나타납니다.

   ![모든 백업 정책](./media/manage-afs-backup/all-backup-policies.png)

1. **Azure 파일 공유와**관련된 정책을 보려면 오른쪽 상단의 드롭다운 목록에서 **Azure 파일 공유를** 선택합니다.

   ![Azure 파일 공유 선택](./media/manage-afs-backup/azure-file-share.png)

새 백업 정책을 만들려면 다음을 수행합니다.

1. 백업 **정책** 창에서 **+ 추가**를 선택합니다.

   ![새 백업 정책](./media/manage-afs-backup/new-backup-policy.png)

1. **추가** 창에서 Azure **파일 공유를** **정책 유형으로**선택합니다. **Azure 파일 공유에** 대한 **백업 정책** 창이 열립니다. 복구 지점에 대한 정책 이름, 백업 빈도 및 보존 범위를 지정합니다. 정책을 정의한 후 **확인을**선택합니다.

   ![백업 정책 정의](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>정책 수정

백업 정책을 수정하여 백업 빈도 또는 보존 범위를 변경할 수 있습니다.

정책을 수정하려면 다음을 수행하십시오.

1. 파일 공유에 대한 백업을 구성하는 데 사용한 복구 서비스 자격 증명 모음을 엽니다. 복구 서비스 자격 증명 모음 메뉴에서 **관리** 섹션에서 **백업 정책을** 선택합니다. 볼트에 구성된 모든 백업 정책이 나타납니다.

   ![볼트의 모든 백업 정책](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Azure 파일 공유와 관련된 정책을 보려면 오른쪽 상단의 드롭다운 목록에서 **Azure 파일 공유를** 선택합니다. 수정할 백업 정책을 선택합니다.

   ![수정할 Azure 파일 공유](./media/manage-afs-backup/azure-file-share-modify.png)

1. **일정** 창이 열립니다. 필요에 따라 **백업 일정** 및 보존 **범위를** 편집하고 **저장을**선택합니다. 창에 "진행 중 업데이트" 메시지가 표시됩니다. 정책 변경 사항이 성공적으로 업데이트되면 "백업 정책을 성공적으로 업데이트했습니다."

   ![수정된 정책 저장](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>파일 공유에 대한 보호 중지

Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

* 이후의 모든 백업 작업을 중지하고 *모든 복구 지점을 삭제합니다.*
* 모든 향후 백업 작업을 중지하지만 *복구 지점을 둡니다.*

Azure Backup에서 만든 기본 스냅숏이 유지되므로 저장소에 복구 지점을 두는 것과 관련된 비용이 있을 수 있습니다. 복구 지점을 남기면 나중에 파일 공유를 복원할 수 있다는 이점이 있습니다. 복구 지점을 떠나는 비용에 대한 자세한 내용은 [가격 세부 정보를](https://azure.microsoft.com/pricing/details/backup/)참조하십시오. 모든 복구 지점을 삭제하려는 경우 파일 공유를 복원할 수 없습니다.

Azure 파일 공유 보호를 중지하려면:

1. 파일 공유 복구 지점이 포함된 복구 서비스 자격 증명 모음을 엽니다. **보호된** 항목 섹션에서 **백업 항목을** 선택합니다. 백업 항목 유형 목록이 나타납니다.

   ![Backup 항목](./media/manage-afs-backup/backup-items.png)

1. **Backup 관리 유형** 목록에서 **Azure Storage(Azure 파일)** 를 선택합니다. **백업 항목(Azure 저장소(Azure 파일))** 목록이 나타납니다.

   ![Azure 저장소 선택(Azure 파일)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 백업 **항목(Azure 저장소(Azure 파일)** 목록에서 보호를 중지할 백업 항목을 선택합니다.

1. 백업 **중지** 옵션을 선택합니다.

   ![백업 중지 선택](./media/manage-afs-backup/stop-backup.png)

1. 백업 **중지** 창에서 **백업 데이터 유지** 또는 백업 데이터 **삭제를**선택합니다. 그런 다음 **백업 중지를**선택합니다.

    ![백업 데이터 유지 선택 또는 백업 데이터 삭제](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>파일 공유에 대한 보호 다시 시작

파일 공유에 대한 보호가 중지되었을 때 **백업 데이터 유지** 옵션을 선택한 경우 보호를 다시 시작할 수 있습니다. 백업 **데이터 삭제** 옵션을 선택한 경우 파일 공유에 대한 보호를 다시 시작할 수 없습니다.

Azure 파일 공유에 대한 보호를 다시 시작하려면 다음을 수행하십시오.

1. 파일 공유 복구 지점이 포함된 복구 서비스 자격 증명 모음을 엽니다. **보호된** 항목 섹션에서 **백업 항목을** 선택합니다. 백업 항목 유형 목록이 나타납니다.

   ![이력서에 대한 백업 항목](./media/manage-afs-backup/backup-items-resume.png)

1. **Backup 관리 유형** 목록에서 **Azure Storage(Azure 파일)** 를 선택합니다. **백업 항목(Azure 저장소(Azure 파일))** 목록이 나타납니다.

   ![Azure 저장소 목록(Azure 파일)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 백업 **항목(Azure 저장소(Azure 파일)** 목록에서 보호를 다시 시작할 백업 항목을 선택합니다.

1. 백업 **백업** 을 선택합니다.

   ![백업 다시 선택](./media/manage-afs-backup/resume-backup.png)

1. **백업 정책** 창이 열립니다. 선택한 정책을 선택하여 백업을 다시 시작합니다.

1. 백업 정책을 선택한 후 **저장을**선택합니다. 포털에 "진행 중 업데이트" 메시지가 표시됩니다. 백업이 성공적으로 다시 시작되면 "보호된 Azure 파일 공유에 대한 백업 정책이 성공적으로 업데이트되었습니다."

   ![백업 정책이 성공적으로 업데이트되었습니다.](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>백업 데이터 삭제

**백업 중지** 작업 중 또는 보호를 중지한 후 언제든지 파일 공유의 백업을 삭제할 수 있습니다. 복구 지점을 삭제하기 전에 며칠 또는 몇 주를 기다리는 것이 도움이 될 수 있습니다. 백업 데이터를 삭제하면 삭제할 특정 복구 지점을 선택할 수 없습니다. 백업 데이터를 삭제하기로 결정한 경우 파일 공유와 연결된 모든 복구 지점을 삭제합니다.

다음 절차에서 파일 공유에 대한 보호가 중지되었다고 가정합니다.

Azure 파일 공유에 대한 백업 데이터를 삭제하려면 다음을 수행하십시오.

1. 백업 작업이 중지된 후 **백업** **백업** 및 백업 데이터 삭제 옵션을 **백업 항목** 대시보드에서 사용할 수 있습니다. 백업 **데이터 삭제** 옵션을 선택합니다.

   ![백업 데이터 삭제](./media/manage-afs-backup/delete-backup-data.png)

1. **백업 데이터 삭제** 창이 열립니다. 삭제를 확인하려면 파일 공유 이름을 입력합니다. 선택적으로 **이유** 또는 **주석** 상자에 자세한 정보를 제공합니다. 백업 데이터를 삭제하는 것이 확실하면 **삭제를**선택합니다.

   ![데이터 삭제 확인](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>저장소 계정 등록 취소

다른 복구 서비스 자격 증명 모음을 사용하여 특정 저장소 계정의 파일 공유를 보호하려면 먼저 해당 저장소 계정의 [모든 파일 공유에 대한 보호를 중지합니다.](#stop-protection-on-a-file-share) 그런 다음 보호에 사용되는 현재 복구 서비스 자격 증명 모음에서 계정을 등록 취소합니다.

다음 절차에서는 등록취소하려는 저장소 계정의 모든 파일 공유에 대해 보호가 중지되었다고 가정합니다.

저장소 계정을 등록 취소하려면 다음을 수행합니다.

1. 저장소 계정이 등록된 복구 서비스 자격 증명 모음을 엽니다.
1. **개요** 창에서 **관리** 섹션에서 **백업 인프라** 옵션을 선택합니다.

   ![백업 인프라 선택](./media/manage-afs-backup/backup-infrastructure.png)

1. **백업 인프라** 창이 열립니다. **Azure 저장소** 계정 섹션에서 저장소 **계정을** 선택합니다.

   ![저장소 계정 선택](./media/manage-afs-backup/storage-accounts.png)

1. **저장소 계정을**선택하면 자격 증명 모음에 등록된 저장소 계정 목록이 나타납니다.
1. 등록 취소할 저장소 계정을 마우스 오른쪽 단추로 클릭하고 **등록 취소를**선택합니다.

   ![등록 취소 선택](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 파일 공유 백업](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)을 참조하세요.
