---
title: Azure Managed Disks 백업
description: Azure Portal에서 Azure Managed Disks를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: ca86550c4dec4b51c60d9ecdef124e38783a3764
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738155"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>Azure Managed Disks 백업(미리 보기 상태)

>[!IMPORTANT]
>Azure Disk Backup은 서비스 수준 계약 없이 미리 보기 상태 이며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 지역 가용성은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.
>
>미리 보기에 등록 하려면 [이 양식을 작성](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 하세요.

이 문서에서는 Azure Portal에서 [Azure 관리 디스크](../virtual-machines/managed-disks-overview.md) 를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 백업 자격 증명 모음 만들기

- 백업 정책 만들기

- Azure 디스크의 백업 구성

- 주문형 백업 작업 실행

Azure 디스크 백업 지역 가용성, 지원 되는 시나리오 및 제한 사항에 대 한 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

## <a name="create-a-backup-vault"></a>백업 자격 증명 모음 만들기

백업 자격 증명 모음은 Azure Database for PostgreSQL 서버 및 Azure 디스크와 같이 Azure Backup에서 지 원하는 다양 한 최신 작업에 대 한 백업 데이터를 저장 하는 Azure의 저장소 엔터티입니다. 백업 자격 증명 모음을 사용 하면 관리 오버 헤드를 최소화 하면서 백업 데이터를 쉽게 구성할 수 있습니다. 백업 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며, 백업 데이터를 보호 하는 데 도움이 되는 향상 된 기능을 제공 합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.
1. 검색 상자에 **Backup center** 를 입력 합니다.
1. **서비스** 에서 **Backup center** 를 선택 합니다.
1. **백업 센터** 페이지에서 **자격 증명 모음** 을 선택 합니다.

   ![백업 센터에서 자격 증명 모음 선택](./media/backup-managed-disks/backup-center.png)

1. **시작: 자격 증명 모음 만들기** 화면에서 **백업 자격 증명 모음** 을 선택 하 고 **계속** 합니다.

   ![시작: 자격 증명 모음 만들기](./media/backup-managed-disks/initiate-create-vault.png)

1. **기본 사항** 탭에서 구독, 리소스 그룹, 백업 자격 증명 모음 이름, 지역 및 백업 저장소 중복성을 제공 합니다. **검토 + 만들기** 를 선택 하 여 계속 합니다. [백업 자격 증명 모음을 만드는](./backup-vault-overview.md#create-a-backup-vault)방법에 대해 자세히 알아보세요.

   ![자격 증명 모음 검토 및 만들기](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>백업 정책 만들기

1. 이전 단계에서 만든 *DemoVault* **backup 자격 증명 모음** 에서 **백업 정책** 으로 이동 하 여 **추가** 를 선택 합니다.

   ![백업 정책 추가](./media/backup-managed-disks/backup-policies.png)

1. **기본 사항** 탭에서 정책 이름을 제공 하 고 **데이터 원본 유형** 을 **Azure Disk** 로 선택 합니다. 자격 증명 모음이 이미 미리 채워져 있고 선택한 자격 증명 모음 속성이 표시 됩니다.

   >[!NOTE]
   > 선택한 자격 증명 모음에 전역 중복 설정이 있을 수 있지만 현재 Azure Disk Backup은 스냅숏 데이터 저장소만 지원 합니다. 모든 백업은 구독의 리소스 그룹에 저장 되며 백업 자격 증명 모음 저장소로 복사 되지 않습니다.

   ![Datasource 유형 선택](./media/backup-managed-disks/datasource-type.png)

1. **백업 정책** 탭에서 백업 일정 빈도를 선택 합니다.

   ![백업 일정 빈도 선택](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure Disk Backup은 하루에 여러 백업을 제공 합니다. 더 자주 백업 해야 하는 경우 4, 6, 8 또는 12 시간 간격으로 백업을 수행할 수 있는 기능을 사용 하 여 **매시간** 백업 빈도를 선택 합니다. 선택한 **시간** 간격에 따라 백업이 예약 됩니다. 예를 들어 **4 시간 마다** 를 선택 하는 경우 백업이 하루에 동일 하 게 분산 되도록 4 시간 마다 매일 백업이 수행 됩니다. 하루에 한 번 백업이 충분 한 경우 **매일** 백업 빈도를 선택 합니다. 매일 백업 빈도에서 백업을 수행할 시간을 지정할 수 있습니다. 시간은 백업 시작 시간을 나타내고 백업은 백업이 완료 되지 않는다는 점에 유의 해야 합니다. 백업 작업을 완료 하는 데 필요한 시간은 디스크 크기 및 연속 백업 간의 변동 비율을 비롯 한 다양 한 요인에 따라 달라 집니다. 그러나 Azure Disk backup은 프로덕션 응용 프로그램 성능에 영향을 주지 않는 [증분 스냅숏을](../virtual-machines/disks-incremental-snapshots.md)사용 하는 에이전트 없는 백업입니다.

1. **백업 정책** 탭에서 RPO (복구 지점 목표) 요구 사항을 충족 하는 보존 설정을 선택 합니다.

   다른 보존 규칙이 지정 되지 않은 경우 기본 보존 규칙이 적용 됩니다. 보존 기간을 변경 하도록 기본 보존 규칙을 수정할 수 있지만 삭제할 수는 없습니다. **보존 규칙 추가** 를 선택 하 여 새 보존 규칙을 추가할 수 있습니다.

   ![보존 규칙 추가](./media/backup-managed-disks/add-retention-rule.png)

   매일 또는 매주 성공적으로 수행 된 **첫 번째 백업을** 선택 하 고 특정 백업이 삭제 되기 전에 보존 될 보존 기간을 지정할 수 있습니다. 이 옵션은 일 또는 주에 대 한 특정 백업을 오랜 시간 동안 유지 하는 데 유용 합니다. 다른 모든 잦은 백업은 짧은 기간 동안 보존 될 수 있습니다.

   ![보존 설정](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Managed Disks에 대 한 Azure Backup는 디스크당 200 스냅숏으로 제한 되는 증분 스냅숏을 사용 합니다. 예약 된 백업 외에도 요청 시 백업을 수행할 수 있도록 백업 정책은 전체 백업을 180로 제한 합니다. 관리 디스크의 [증분 스냅숏에](../virtual-machines/disks-incremental-snapshots.md#restrictions) 대해 자세히 알아보세요.

1. **검토 + 만들기** 를 선택 하 여 백업 정책 만들기를 완료 합니다.

## <a name="configure-backup"></a>백업 구성

백업 자격 증명 모음은 관리 Id를 사용 하 여 다른 Azure 리소스에 액세스 합니다. 관리 디스크의 백업을 구성 하려면 백업 자격 증명 모음의 관리 되는 id에 스냅숏이 만들어지고 관리 되는 원본 디스크 및 리소스 그룹에 대 한 권한 집합이 필요 합니다.

시스템 할당 관리 id는 리소스 당 하나로 제한 되며이 리소스의 수명 주기에 연결 됩니다. Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 관리 되는 id에 대 한 권한을 부여할 수 있습니다. 관리 id는 Azure 리소스에만 사용할 수 있는 특수 형식의 서비스 사용자입니다. [관리 id](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

관리 디스크의 백업을 구성 하려면 다음 필수 구성 요소가 필요 합니다.

1. 백업할 원본 디스크에서 백업 자격 증명 모음 관리 id에 **디스크 백업 읽기 권한자** 역할을 할당 합니다.

   1. 백업 해야 하는 디스크로 이동 합니다.

   1. **액세스 제어 (IAM)** 로 이동 하 여 **역할 할당 추가** 를 선택 합니다.

   1. 오른쪽 컨텍스트 창의 **역할** 드롭다운 목록에서 **디스크 백업 판독기** 를 선택 합니다. 백업 자격 증명 모음에서 관리 되는 id를 선택 하 고 **저장** 합니다.

   >[!TIP]
   >백업 자격 증명 모음 이름을 입력 하 여 자격 증명 모음의 관리 되는 id를 선택 합니다.

   ![디스크 백업 판독기 역할 추가](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Azure Backup 서비스에서 백업을 만들고 관리 하는 리소스 그룹의 백업 자격 증명 모음 관리 id에 **디스크 스냅숏 참가자** 역할을 할당 합니다. 디스크 스냅숏은 구독 내의 리소스 그룹에 저장 됩니다. Azure Backup 서비스에서 스냅숏을 만들고 저장 하 고 관리할 수 있도록 하려면 백업 자격 증명 모음에 대 한 권한을 제공 해야 합니다.

   **스냅숏을 저장 하 고 관리 하기 위한 리소스 그룹 선택:**

   - 원본 디스크와 동일한 리소스 그룹을 선택 하지 마세요.

   - 지침으로, Azure Backup 서비스에서 사용할 스냅숏 데이터 저장소로 전용 리소스 그룹을 만드는 것이 좋습니다. 전용 리소스 그룹을 사용 하면 리소스 그룹에 대 한 액세스 권한을 제한할 수 있으므로 안전 하 고 쉽게 백업 데이터를 관리할 수 있습니다.

   - 이 리소스 그룹을 사용 하 여 백업 되거나 백업 될 예정인 여러 디스크에 스냅숏을 저장할 수 있습니다.  

   - 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅숏을 만들 수 없습니다. 따라서 백업할 디스크와 동일한 구독 내에 있는 리소스 그룹을 선택 합니다. 관리 디스크의 [증분 스냅숏에](../virtual-machines/disks-incremental-snapshots.md#restrictions) 대해 자세히 알아보세요.

   역할을 할당 하려면 다음 단계를 수행 합니다.

   1. 리소스 그룹으로 이동 합니다. 예를 들어 리소스 그룹은 백업할 디스크와 동일한 구독에 있는 *SnapshotRG* 입니다.

   1. **액세스 제어 (IAM)** 로 이동 하 여 **역할 할당 추가** 를 선택 합니다.

   1. 오른쪽 컨텍스트 창의 **역할** 드롭다운 목록에서 **디스크 스냅숏 참가자** 를 선택 합니다. 백업 자격 증명 모음에서 관리 되는 id를 선택 하 고 **저장** 합니다.

   >[!TIP]
   >백업 자격 증명 모음 이름을 입력 하 여 자격 증명 모음의 관리 되는 id를 선택 합니다.

   ![디스크 스냅숏 참가자 역할 추가](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. 백업 자격 증명 모음에서 관리 되는 id에 스냅숏 데이터 저장소 역할을 하는 원본 디스크 및 리소스 그룹에 대 한 올바른 역할 할당 집합이 있는지 확인 합니다.

   1. **백업 자격 증명 모음-> id** 로 이동 하 고 **Azure 역할 할당** 을 선택 합니다.

      ![Azure 역할 할당 선택](./media/backup-managed-disks/azure-role-assignments.png)

   1. 역할, 리소스 이름 및 리소스 종류가 올바르게 반영 되었는지 확인 합니다.

      ![역할, 리소스 이름 및 리소스 종류를 확인 합니다.](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >포털에서 역할 할당이 올바르게 반영 되지만 백업 자격 증명 모음 관리 id에 사용 권한을 적용 하는 데 약 15 분이 걸릴 수 있습니다.

1. 필수 구성 요소가 충족 되 면 **백업 자격 증명 모음-> 개요** 로 이동 하 고 **백업** 을 선택 하 여 디스크의 백업 구성을 시작 합니다.

   ![백업 선택](./media/backup-managed-disks/select-backup.png)

1. **기본 사항** 탭에서 **Azure Disk** 를 데이터 원본 유형으로 선택 합니다.

   ![Azure 디스크 선택](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure Backup는 관리 디스크의 [증분 스냅숏을](../virtual-machines/disks-incremental-snapshots.md#restrictions) 사용 합니다 .이 스냅숏은 부모 디스크의 저장소 유형에 관계 없이 표준 HDD 저장소의 마지막 스냅숏 이후 디스크에 대 한 델타 변경 내용만 저장 합니다. 추가 안정성을 위해 증분 스냅숏은 기본적으로 ZRS를 지 원하는 지역에 ZRS (영역 중복 저장소)에 저장 됩니다. 현재 Azure Disk Backup은 백업 자격 증명 모음 저장소에 백업을 복사 하지 않는 관리 디스크의 운영 백업을 지원 합니다. 따라서 백업 자격 증명 모음의 백업 저장소 중복성 설정은 복구 시점에 적용 되지 않습니다.

1. **백업 정책** 탭에서 백업 정책을 선택 합니다.

   ![백업 정책 선택](./media/backup-managed-disks/choose-backup-policy.png)

1. **리소스** 탭에서 **select Azure Disk** 를 선택 합니다. 오른쪽 컨텍스트 창에서 백업할 디스크를 선택 합니다.

   ![백업할 디스크 선택](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >포털에서는 여러 디스크를 선택 하 고 백업을 구성할 수 있지만 각 디스크는 개별 백업 인스턴스입니다. 현재 Azure Disk Backup은 개별 디스크의 백업만 지원 합니다. 가상 디스크에 연결 된 여러 디스크의 지정 시간 백업은 지원 되지 않습니다.
   >
   >포털을 사용 하는 경우 동일한 구독 내에서 디스크를 선택 하는 것으로 제한 됩니다. 백업할 디스크가 여러 개 있거나 디스크가 여러 구독에 걸쳐 있는 경우 스크립트를 사용 하 여 자동화할 수 있습니다.
   >
   >Azure Disk backup 지역 가용성, 지원 되는 시나리오 및 제한 사항에 대 한 자세한 내용은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.

1. **스냅숏 리소스 그룹** 을 선택 하 고 **유효성 검사** 를 선택 합니다. 이는 Azure Backup 서비스에서 백업 자격 증명 모음에 대 한 증분 스냅숏을 만들고 관리 하는 리소스 그룹입니다. 관리 id는 필수 구성 요소의 일부로 필수 역할 권한으로 할당 됩니다.

   ![스냅숏 리소스 그룹 선택](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >백업 워크플로 구성을 완료 하기 전에 유효성 검사를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 다음의 경우 유효성 검사에 실패할 수 있습니다.
   >
   > - 디스크가 지원 되지 않습니다. 지원 되지 않는 시나리오는 [지원 매트릭스](disk-backup-support-matrix.md) 를 참조 하세요.
   > - 백업 자격 증명 모음 관리 id에 백업할 **디스크** 또는 증분 스냅숏이 저장 된 **스냅숏 리소스 그룹** 에 대 한 올바른 역할 할당이 없습니다.

1. 유효성 검사가 완료 되 면 **검토 및 구성** 을 선택 하 여 선택한 디스크의 백업을 구성 합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

1. 이전 단계에서 만든 *DemoVault* **backup 자격 증명 모음** 에서 **백업** 인스턴스로 이동 하 고 백업 인스턴스를 선택 합니다.

   ![백업 인스턴스 선택](./media/backup-managed-disks/select-backup-instance.png)

1. **백업 인스턴스** 화면에서 다음을 찾을 수 있습니다.

   - 원본 디스크 이름, 증분 스냅숏이 저장 되는 스냅숏 리소스 그룹, 백업 자격 증명 모음 및 백업 정책을 포함 하는 **필수** 정보입니다.
   - 지난 7 일간 백업 및 복원 작업의 요약 및 해당 상태를 보여 주는 **작업 상태** 입니다.
   - 선택한 기간에 대 한 **복원 지점의** 목록입니다.

1. **백업** 을 선택 하 여 요청 시 백업을 시작 합니다.

   ![지금 백업을 선택 합니다.](./media/backup-managed-disks/backup-now.png)

1. 백업 정책과 연결 된 보존 규칙 중 하나를 선택 합니다. 이 보존 규칙은이 주문형 백업의 보존 기간을 결정 합니다. **지금 백업** 을 선택 하 여 백업을 시작 합니다.

   ![백업 시작](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>백업 작업 추적

Azure Backup 서비스는 예약 된 백업에 대 한 작업을 만들거나 추적을 위해 주문형 백업 작업을 트리거하는 경우입니다. 백업 작업 상태를 보려면 다음을 수행 합니다.

1. **백업 인스턴스** 화면으로 이동 합니다. 지난 7 일간 작업 및 상태를 포함 하는 작업 대시보드가 표시 됩니다.

   ![작업 대시보드](./media/backup-managed-disks/jobs-dashboard.png)

1. 백업 작업의 상태를 보려면 **모두 보기** 를 선택 하 여이 백업 인스턴스의 진행 중인 작업과 이전 작업을 표시 합니다.

   ![모두 보기를 선택 합니다.](./media/backup-managed-disks/view-all.png)

1. 백업 및 복원 작업 목록과 해당 상태를 검토 합니다. 작업 목록에서 작업을 선택 하 여 작업 세부 정보를 볼 수 있습니다.

   ![세부 정보를 보려면 작업을 선택 합니다.](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>다음 단계

- [Azure Managed Disks 복원](restore-managed-disks.md)