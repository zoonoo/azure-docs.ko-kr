---
title: Azure Files 백업 FAQ
description: 이 문서에서는 Azure Backup 서비스를 사용하여 Azure 파일 공유를 보호하는 방법에 대한 일반적인 질문과 답변을 검색합니다.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: b89e4a55bc8bc4ef2f4cdb50059537fe7708b6a6
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101164"
---
# <a name="questions-about-backing-up-azure-files"></a>Azure Files 백업에 대한 질문

이 문서에서는 Azure Files 백업에 대한 일반적인 질문에 대답합니다. 대답 중 일부에는 포괄적인 정보를 포함하는 문서에 대한 링크가 있습니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure Backup 서비스에 대한 질문도 게시할 수 있습니다.

이 문서의 섹션을 신속하게 검색하려면 **이 문서** 아래의 오른쪽에서 링크를 사용합니다.

## <a name="configuring-the-backup-job-for-azure-files"></a>Azure Files에 대한 백업 작업 구성

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>유효한 Azure 파일 공유를 포함 하 여 보호 하려는 일부 저장소 계정이 표시 되지 않는 이유는 무엇 인가요?

저장소 계정이 지원 되는 저장소 계정 유형 중 하나에 속하도록 하려면 [Azure 파일 공유 백업에 대 한 지원 매트릭스](azure-file-share-support-matrix.md) 를 참조 하세요. 또한 찾고 있는 저장소 계정이 이미 보호 되 고 있거나 다른 자격 증명 모음에 등록 되어 있을 수 있습니다. 보호를 위해 다른 자격 증명 모음에서 저장소 계정을 검색 하려면 자격 증명 모음에서 [저장소 계정 등록을 취소](manage-afs-backup.md#unregister-a-storage-account) 합니다.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>백업을 구성하려고 할 때 스토리지 계정에서 일부 Azure 파일 공유가 표시되지 않는 이유는 무엇인가요?

Azure 파일 공유가 동일한 Recovery Services 자격 증명 모음에서 이미 보호되었거나 최근에 삭제되었는지 확인합니다.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Azure File Sync에서 동기화 그룹에 연결된 파일 공유를 보호할 수 있나요?

예. 동기화 그룹에 연결 된 Azure 파일 공유의 보호를 사용 하도록 설정 합니다.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>파일 공유를 백업하려고 시도할 때 스토리지 계정의 파일 공유를 검색하기 위해 스토리지 계정을 클릭했습니다. 그러나 보호 하지 않았습니다. 다른 자격 증명 모음을 사용 하 여 이러한 파일 공유를 보호 어떻게 할까요??

백업을 시도할 때 저장소 계정을 선택 하 여 내에서 파일 공유를 검색 하면 저장소 계정이이 작업을 수행 하는 자격 증명 모음에 등록 됩니다. 다른 자격 증명 모음을 사용 하 여 파일 공유를 보호 하도록 선택 하는 경우이 자격 증명 모음에서 선택한 저장소 계정을 [등록 취소](manage-afs-backup.md#unregister-a-storage-account) 합니다.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>파일 공유를 백업하는 자격 증명 모음을 변경할 수 있나요?

예. 그러나 연결 된 자격 증명 모음에서 [파일 공유에 대 한 보호를 중지](manage-afs-backup.md#stop-protection-on-a-file-share) 하 고이 저장소 계정의 등록을 [취소](manage-afs-backup.md#unregister-a-storage-account) 한 다음 다른 자격 증명 모음에서 보호 해야 합니다.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>자격 증명 모음에서 보호할 수 있는 Azure 파일 공유 수는 몇 개인가요?

자격 증명 모음 당 최대 50 개의 저장소 계정에서 Azure 파일 공유를 보호할 수 있습니다. 또한 자격 증명 모음당 최대 200개의 Azure 파일 공유를 보호할 수 있습니다.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>동일한 스토리지 계정의 두 파일 공유를 서로 다른 자격 증명 모음에서 보호할 수 있나요?

아니요. 한 스토리지 계정의 모든 파일 공유는 동일한 자격 증명 모음에서만 보호할 수 있습니다.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>최대 제한에 도달 하 여 백업에 실패 하는 경우 어떻게 해야 하나요?

언제든지 파일 공유에 대해 최대 200개의 스냅샷을 가질 수 있습니다. 이 한도에는 정책에 정의된 대로 Azure Backup에서 생성한 스냅샷이 포함됩니다. 제한에 도달한 후에 백업이 실패를 시작 하는 경우 성공적인 향후 백업을 위해 주문형 스냅숏을 삭제 합니다.

## <a name="restore"></a>복원

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>삭제된 Azure 파일 공유에서 복구할 수 있나요?

Azure 파일 공유가 삭제 되 면 삭제 될 백업 목록이 표시 되 고 확인이 요청 됩니다. 현재 삭제 된 Azure 파일 공유는 복원할 수 없습니다.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Azure 파일 공유에 대한 보호를 중지한 경우 백업에서 복원할 수 있나요?

예. 보호를 중지할 때 **백업 데이터 보관**을 선택한 경우 모든 기존 복원 지점에서 복원할 수 있습니다.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>진행 중인 복원 작업을 취소하면 어떻게 되나요?

진행 중인 복원 작업이 취소되면 복원 프로세스가 중지되고 취소 전에 모든 파일이 복원되면 롤백 없이 구성된 대상(원래 또는 대체 위치)에 남아 있습니다.

## <a name="manage-backup"></a>백업 관리

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>PowerShell을 사용하여 Azure 파일 공유에 대한 백업을 구성/관리/복원할 수 있나요?

예. 자세한 내용은 [여기](backup-azure-afs-automation.md)를 참조 하세요.

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Azure 백업에서 만든 스냅숏에 액세스 하 고 탑재할 수 있나요?

Azure Backup에서 수행 하는 모든 스냅숏에는 포털, PowerShell 또는 CLI에서 스냅숏 보기를 통해 액세스할 수 있습니다. Azure 파일 공유 스냅샷에 대한 자세한 내용은 [Azure Files(미리 보기)의 공유 스냅샷 개요](../storage/files/storage-snapshots-files.md)를 참조하세요.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>백업에 대해 구성할 수 있는 최대 보존 기간은 얼마인가요?

최대 보존에 대 한 자세한 내용은 [지원 매트릭스](azure-file-share-support-matrix.md) 를 참조 하세요. Azure Backup는 백업 정책을 구성 하는 동안 보존 값을 입력할 때 스냅숏 수를 실시간으로 계산 합니다. 정의 된 보존 값에 해당 하는 스냅숏 수가 200을 초과 하는 즉시 포털에 보존 값을 조정 하도록 요청 하는 경고가 표시 됩니다. 즉, 언제 든 지 파일 공유에 대 한 Azure Files에서 지 원하는 최대 스냅숏 수 제한을 초과 하지 않습니다.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Azure 파일 공유에 대한 Backup 정책을 변경하면 어떻게 되나요?

파일 공유에 대한 새 정책이 적용되면 새 정책의 일정 및 보존 기간을 따릅니다. 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 유지되도록 표시됩니다. 보존 기간을 줄이면 다음 정리 작업에서 정리(prune) 표시되고 삭제됩니다.

## <a name="next-steps"></a>다음 단계

Azure Backup의 다른 영역에 대한 자세한 내용은 다음 백업 FAQ를 참조하세요.

- [Recovery Services 자격 증명 모음 FAQ](backup-azure-backup-faq.md)
- [Azure VM 백업 FAQ](backup-azure-vm-backup-faq.md)
- [Azure Backup 에이전트 FAQ](backup-azure-file-folder-backup-faq.md)
