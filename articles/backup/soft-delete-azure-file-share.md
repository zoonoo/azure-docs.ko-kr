---
title: Azure 파일 공유에 대한 실수로 인한 삭제 방지
description: 일시 삭제 방법이 Azure 파일을 실수로 인한 삭제로부터 어떻게 보호하는지 알아봅니다.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89179915"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Azure Backup을 사용하여 Azure 파일 공유의 실수로 인한 삭제 방지

사이버 공격 또는 실수로 인한 삭제를 방지하기 위해 각 스토리지 계정의 모든 파일 공유에 대한 백업을 구성할 때 스토리지 계정의 모든 파일 공유에 대해 [일시 삭제](../storage/files/storage-files-prevent-file-share-deletion.md)를 사용 설정할 수 있습니다. 일시 삭제를 사용하면 악의적인 작업자가 파일 공유를 삭제하는 경우에도 파일 공유의 내용과 복구 지점(스냅샷)이 추가적으로 최소14일 동안 유지되므로 데이터 손실 없이 파일 공유를 복구할 수 있습니다.  일시 삭제는 표준 및 프리미엄 스토리지 계정에 지원되며, Azure Backup에서 스토리지 계정 호스팅으로 백업되는 모든 파일 공유에 대하여 해당 설정을 활성화합니다.

다음 순서도는 스토리지 계정의 파일 공유에 대해 일시 삭제를 사용하도록 설정한 경우 백업 항목의 여러 단계와 상태를 보여 줍니다.

 ![일시 삭제 순서도](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>스토리지 계정에 있는 파일 공유에 대해 언제 일시 삭제가 사용 설정되나요?

스토리지 계정의 모든 파일 공유에 대해 처음으로 백업을 구성할 때 Azure Backup 서비스가 각 스토리지 계정의 모든 파일 공유에 대해 일시 삭제를 사용하도록 설정합니다.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>파일 공유를 삭제한 후 내 스냅샷과 복원 지점이 일시 삭제된 상태로 유지되는 일 수를 구성할 수 있나요?

예, 사용자 요구 사항에 따라 보존 기간을 설정할 수 있습니다. [이 문서](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal)에서는 보존 기간을 구성하는 단계에 대해 설명합니다. 백업된 파일 공유를 포함하는 스토리지 계정의 경우 최소 보존 설정은 14일입니다.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>14일 미만으로 구성한 경우 Azure Backup이 보존 설정을 초기화하나요?

보안적인 관점에서 볼 때 백업된 파일 공유를 포함하는 스토리지 계정에 대해 최소 보존 기간을 14일로 설정하는 것이 좋습니다. 따라서 각 백업 작업이 실행될 때, Azure Backup이 14일 미만으로 된 설정을 식별하면 14일로 초기화합니다.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>보존 기간 중에 어떤 비용이 발생하나요?

일시 삭제된 기간 동안 보호된 인스턴스 비용 및 스냅샷 스토리지 비용은 그대로 유지됩니다.  또한 표준 파일 공유의 경우 일반 요금으로, 프리미엄 파일 공유의 경우 스냅샷 스토리지 요금으로 사용된 용량에 대한 요금이 청구됩니다.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>내 데이터가 일시 삭제 상태일 때 복원 작업을 수행할 수 있나요?

복원 작업을 수행하려면 먼저 일시 삭제된 파일 공유의 삭제를 취소해야 합니다. 삭제 취소 작업은 파일 공유를 특정 시점으로 복원할 수 있는 백업된 상태로 전환합니다. 파일 공유의 삭제를 취소하는 방법을 알아보려면 [이 링크](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)를 방문하거나 [파일 공유 삭제 취소 스크립트](./scripts/backup-powershell-script-undelete-file-share.md)를 참조하세요.

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>보호된 파일 공유가 하나 이상 있는 스토리지 계정에서 파일 공유의 데이터는 어떻게 제거하나요?

스토리지 계정에 보호된 파일 공유가 하나 이상 있다면 해당 계정의 모든 파일 공유에 대해 일시 삭제를 사용하도록 설정이 돼 있고, 삭제 작업 후 14일 동안 데이터가 보존된다는 것을 의미합니다. 그러나 데이터를 즉시 제거하고 싶고 유지를 원하지 않는 경우 다음 단계를 수행합니다.

1. 일시 삭제를 사용하도록 설정한 상태에서 파일 공유를 이미 삭제한 경우에는 먼저 [Files 포털](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)에서 파일 공유의 삭제를 취소하거나 [파일 공유 삭제 취소 스크립트](./scripts/backup-powershell-script-undelete-file-share.md)를 사용하여 파일 공유의 삭제를 취소합니다.
2. [이 문서](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)에 언급된 단계를 수행하여 스토리지 계정의 파일 공유에 대해 일시 삭제를 사용하지 않도록 설정합니다.
3. 이제 콘텐츠를 즉시 제거하고자 하는 파일 공유를 삭제합니다.

>[!NOTE]
>스토리지 계정에 있는 보호된 파일 공유에 대해 다음 예약된 백업 작업이 실행되기 전에 2단계를 수행해야 합니다. 백업 작업이 실행될 때마다 스토리지 계정의 모든 파일 공유에 대해 일시 삭제를 다시 사용하도록 설정되기 때문입니다.

>[!WARNING]
>2단계에서 일시 삭제를 사용하지 않도록 설정한 후 파일 공유에 대해 수행되는 모든 삭제 작업은 영구 삭제 작업입니다. 따라서 일시 삭제를 사용하지 않도록 설정한 후 백업된 파일 공유를 실수로 삭제하면 모든 스냅샷이 손실되며 데이터를 복구할 수 없습니다.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>파일 공유의 일시 삭제 설정이란 맥락에서, 스토리지 계정의 등록을 취소할 때 Azure Backup은 어떤 변경 작업을 하나요?

등록을 취소하는 시점에 Azure Backup이 파일 공유에 대한 보존 기간 설정을 확인해서 14일보다 크거나 14일보다 작은 경우 보존을 그대로 유지합니다. 그러나 보존 기간이 14일인 경우 Azure Backup에서 활성화한 것으로 간주하여 등록 취소 프로세스 중에 일시 삭제를 비활성화합니다. 보존 설정을 그대로 유지하면서 스토리지 계정의 등록을 취소하려면 등록 취소를 완료한 후 스토리지 계정 창에서 다시 사용하도록 설정합니다. 구성 단계에 대해서는 [이 링크](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal에서 Azure File 공유를 백업](backup-afs.md)하는 방법 알아보기
