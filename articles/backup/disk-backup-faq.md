---
title: Azure 디스크 백업에 대 한 질문과 대답
description: Azure Disk Backup에 대해 자주 묻는 질문에 대 한 답변 가져오기
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 3ef18a7d178075194e24889477768583f05f0cdd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734565"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>Azure Disk Backup (미리 보기)에 대 한 질문과 대답

>[!IMPORTANT]
>Azure Disk Backup은 서비스 수준 계약 없이 미리 보기 상태 이며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 지역 가용성은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.
>
>미리 보기에 등록 하려면 [이 양식을 작성](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 하세요.

이 문서에서는 Azure Disk Backup에 대해 자주 묻는 질문과 대답을 설명 합니다. [Azure Disk backup](disk-backup-overview.md) 지역 가용성, 지원 되는 시나리오 및 제한 사항에 대 한 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Azure 가상 머신 백업을 사용 하 여 동일한 디스크를 백업 하는 경우 Azure Disk Backup 솔루션을 사용 하 여 디스크를 백업할 수 있나요?

Azure Backup는 디스크 백업 및 [AZURE VM 백업](backup-azure-vms-introduction.md) 솔루션을 사용 하 여 관리 디스크의 백업에 대 한 side-by-side 지원을 제공 합니다. 이는 프로덕션 응용 프로그램 성능에 영향을 주지 않고 일관 되 게 작동 하는 OS 디스크 또는 특정 데이터 디스크의 백업 및 백업에 대 한 한 번의 응용 프로그램 일치 백업을 수행 해야 하는 경우에 유용 합니다.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>디스크에 대 한 백업을 구성 하는 데 사용한 스냅숏 리소스 그룹을 찾을 어떻게 할까요? 있나요?

**Backup 인스턴스** 화면의 **Essentials** 섹션에서 스냅숏 리소스 그룹 필드를 찾을 수 있습니다. 백업 센터 또는 백업 자격 증명 모음에서 해당 디스크의 백업 인스턴스를 검색 하 고 선택할 수 있습니다.

![스냅숏 리소스 그룹 필드](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>스냅숏 리소스 그룹 이란?

Azure Disk Backup은 관리 디스크에 대 한 운영 계층 백업을 제공 합니다. 즉, 예약 된 백업 작업을 수행 하는 동안 생성 된 스냅숏은 구독 내의 리소스 그룹에 저장 됩니다. 증분 스냅숏은 구독 내에 저장 되기 때문에 Azure Backup는 인스턴트 복원을 제공 합니다. 이 리소스 그룹을 스냅숏 리소스 그룹 이라고 합니다. 자세한 내용은 [백업 구성](backup-managed-disks.md#configure-backup)을 참조 하세요.

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>스냅숏 리소스 그룹이 백업 중인 디스크와 동일한 구독에 있어야 하는 이유는 무엇 인가요?

해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅숏을 만들 수 없습니다. 따라서 백업할 디스크와 동일한 구독 내에 있는 리소스 그룹을 선택 합니다. 관리 디스크의 [증분 스냅숏에](../virtual-machines/disks-incremental-snapshots.md#restrictions) 대해 자세히 알아보세요.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>백업을 구성 하 고, 예약 된 요청 및 주문형 백업을 수행 하 고, 작업을 복원 하기 위해 역할 할당을 제공 해야 하는 이유는 무엇 인가요?

Azure 디스크 백업은 최소 권한 방법을 사용 하 여 구독에서 관리 디스크를 검색, 보호 및 복원 합니다. 이를 위해 Azure Backup는 [백업 자격 증명 모음의](backup-vault-overview.md) 관리 id를 사용 하 여 다른 Azure 리소스에 액세스 합니다. 시스템 할당 관리 id는 리소스 당 하나로 제한 되며이 리소스의 수명 주기에 연결 됩니다. Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 관리 되는 id에 대 한 권한을 부여할 수 있습니다. 관리 id는 Azure 리소스에만 사용할 수 있는 특수 형식의 서비스 사용자입니다. [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요. 기본적으로 백업 자격 증명 모음에는 백업 하는 데 사용할 디스크에 액세스할 수 있는 권한이 없습니다. 정기적으로 스냅숏을 만들고 보존 기간 후에 스냅숏을 삭제 하 고 백업에서 디스크를 복원 합니다. 백업 자격 증명 모음 관리 id에 역할 할당을 명시적으로 부여 하 여 구독에 대 한 리소스에 대 한 사용 권한을 관리 하는 것을 제어할 수 있습니다.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>백업 정책이 보존 기간을 제한 하는 이유는 무엇 인가요?

Azure Disk Backup은 디스크 당 200 스냅숏으로 제한 되는 증분 스냅숏을 사용 합니다. 예약 된 백업 외에도 요청 시 백업을 수행할 수 있도록 백업 정책은 전체 백업을 180로 제한 합니다. 관리 디스크의 [증분 스냅숏에](../virtual-machines/disks-incremental-snapshots.md#restrictions) 대해 자세히 알아보세요.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>백업 정책에서 매시간 및 매일 백업 빈도는 어떻게 작동 하나요?

Azure Disk Backup은 하루에 여러 백업을 제공 합니다. 더 자주 백업 해야 하는 경우 **매시간** 백업 빈도를 선택 합니다. 선택한 **시간** 간격에 따라 백업이 예약 됩니다. 예를 들어 **4 시간 마다** 를 선택 하는 경우 백업이 하루에 동일 하 게 분산 되도록 약 4 시간 마다 백업이 수행 됩니다. 하루에 백업 하는 데 충분 한 경우 **매일** 백업 빈도를 선택 합니다. 매일 백업 빈도에서 백업을 수행 하는 시간을 지정할 수 있습니다. 시간은 백업 시작 시간을 나타내고 백업은 백업이 완료 되지 않는다는 점에 유의 해야 합니다. 백업 작업을 완료 하는 데 필요한 시간은 연속 백업 간의 변동 비율을 비롯 한 다양 한 요인에 따라 달라 집니다. 그러나 Azure Disk backup은 프로덕션 응용 프로그램 성능에 영향을 주지 않는 [증분 스냅숏을](../virtual-machines/disks-incremental-snapshots.md) 사용 하는 에이전트 없는 백업입니다.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>백업 자격 증명 모음의 중복성 설정이 운영 계층 (스냅숏 리소스 그룹)에 저장 된 백업에 적용 되지 않는 이유는 무엇 인가요?

Azure Backup는 부모 디스크의 저장소 유형과 관계 없이 표준 HDD 저장소의 마지막 스냅숏 이후 디스크에 대 한 델타 변경 내용만 저장 하는 관리 디스크의 [증분 스냅숏을](../virtual-machines/disks-incremental-snapshots.md#restrictions) 사용 합니다. 안정성을 높이기 위해 증분 스냅숏은 기본적으로 ZRS를 지 원하는 지역에 ZRS (영역 중복 저장소)에 저장 됩니다. 현재 Azure Disk Backup은 백업을 백업 자격 증명 모음 저장소로 복사 하지 않는 관리 디스크의 운영 백업을 지원 합니다. 따라서 백업 자격 증명 모음의 백업 저장소 중복성 설정은 복구 점에 적용 되지 않습니다.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Backup Center를 사용 하 여 백업을 구성 하 고 Azure 디스크에 대 한 백업 인스턴스를 관리할 수 있나요?

예, Azure Disk Backup은 [백업 센터](backup-center-overview.md)에 통합 되어 있으며,이는 azure에서 대규모 백업을 제어, 모니터링, 운영 및 분석 하는 데 사용할 수 있는 **단일 통합 관리 환경을** 제공 합니다. 백업 자격 증명 모음을 사용 하 여 자격 증명 모음 내에서 보호 되는 백업 인스턴스를 백업, 복원 및 관리할 수도 있습니다.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>백업 자격 증명 모음을 만들어야 하 고 Recovery Services 자격 증명 모음을 사용 하지 않는 이유는 무엇 인가요?

백업 자격 증명 모음은 Azure에서 Azure Backup 지원 되는 특정 최신 워크 로드에 대 한 백업 데이터를 보관 하는 저장소 엔터티입니다. 백업 자격 증명 모음을 사용 하 여 Azure Database for PostgreSQL 서버, Azure 디스크 및 Azure Backup에서 지원할 최신 작업 등 다양 한 Azure 서비스에 대 한 백업 데이터를 저장할 수 있습니다. 백업 자격 증명 모음을 사용 하면 관리 오버 헤드를 최소화 하면서 백업 데이터를 쉽게 구성할 수 있습니다. 자세한 내용은 [Backup 자격 증명 모음](./backup-vault-overview.md) 을 참조 하세요.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>백업할 디스크와 백업 자격 증명 모음이 서로 다른 구독에 있을 수 있나요?

예, 백업할 원본 관리 디스크와 백업 자격 증명 모음은 서로 다른 구독에 있을 수 있습니다.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>백업할 디스크와 백업 자격 증명 모음이 서로 다른 지역에 있을 수 있나요?

아니요. 현재 백업할 원본 관리 디스크와 백업 자격 증명 모음은 동일한 지역에 있어야 합니다.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>디스크를 다른 구독으로 복원할 수 있나요?

예, 백업을 수행 하는 원본 관리 디스크와 다른 구독으로 디스크를 복원할 수 있습니다.

### <a name="can-i-back-up-multiple-disks-together"></a>여러 디스크를 함께 백업할 수 있나요?

아니요, 가상 머신에 연결 된 여러 디스크의 지정 시간 스냅숏은 지원 되지 않습니다. 자세한 내용은 [백업 구성](backup-managed-disks.md#configure-backup) 및 제한 사항에 대 한 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>여러 구독에서 디스크를 백업 하는 옵션은 무엇입니까?

현재는 Azure Portal 사용 하 여 디스크 백업을 구성 하는 것은 동일한 구독의 최대 20 개의 디스크로 제한 됩니다.

### <a name="what-is-a-target-resource-group"></a>대상 리소스 그룹 이란?

복원 작업 중에는 디스크를 복원 하려는 구독 및 리소스 그룹을 선택할 수 있습니다. Azure Backup 선택 된 리소스 그룹의 복구 지점에서 새 디스크를 만듭니다. 이를 대상 리소스 그룹 이라고 합니다. 백업 자격 증명 모음의 관리 되는 id는 복원 작업을 성공적으로 수행할 수 있도록 대상 리소스 그룹에 대 한 역할 할당이 필요 합니다. 자세한 내용은 [복원 설명서](restore-managed-disks.md)를 참조 하세요.

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>백업 및 복원 작업 중 Azure Backup에서 사용 하는 권한은 무엇입니까?

백업할 **디스크** 에 할당 된 **디스크 백업 판독기** 역할에 사용 되는 작업은 다음과 같습니다.

"Microsoft. Compute/disks/read"

"BeginGetAccess/disks//action"

**스냅숏 리소스 그룹** 에 할당 된 **디스크 스냅숏 참가자** 역할에 사용 되는 작업은 다음과 같습니다.

"Microsoft. Compute/snapshot/delete"

"Microsoft. Compute/snapshot/write"

"Microsoft. Compute/snapshot/read"

"Microsoft Storage/storageAccounts/write"

"Microsoft Storage/storageAccounts/read"

"Microsoft Storage/storageAccounts/delete"

"Microsoft .Resources/subscription/resourceGroups/read"

"Microsoft Storage/storageAccounts/listkeys/action"

"BeginGetAccess/action"

"Microsoft. Compute/snapshot/endGetAccess/action"

"BeginGetAccess/disks//action"

**대상 리소스 그룹** 에 할당 된 **디스크 복원 운영자** 역할에 사용 되는 작업은 다음과 같습니다.

"Microsoft. Compute/disks/write"

"Microsoft. Compute/disks/read"

"Microsoft .Resources/subscription/resourceGroups/read"

>[!NOTE]
>이러한 역할에 대 한 사용 권한은 나중에 Azure Backup 서비스에서 추가 하는 기능에 따라 변경 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Disk Backup 지원 매트릭스](disk-backup-support-matrix.md)