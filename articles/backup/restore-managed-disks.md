---
title: Azure Managed Disks 복원
description: Azure Portal에서 Azure Managed Disks를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 94adc8512987b50a8df07d295215ffcff873162f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108590"
---
# <a name="restore-azure-managed-disks"></a>Azure Managed Disks 복원

이 문서는 Azure Backup에서 만든 복원 지점에서 [Azure Managed Disks](../virtual-machines/managed-disks-overview.md)를 복원하는 방법을 설명합니다.

현재, 백업을 수행한 기존 소스 디스크를 교체하여 복원하는 OLR(원래 위치 복구) 옵션은 지원되지 않습니다. 복구 지점에서 복원하여 백업을 수행한 원본 디스크와 동일한 리소스 그룹이나 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR(대체 위치 복구)이라고 하며 이는 원본 디스크와 복원된 새 디스크를 모두 유지하는 데 도움이 됩니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 디스크를 만드는 복원

- 복원 작업 상태 추적

## <a name="restore-to-create-a-new-disk"></a>새 디스크를 만드는 복원

백업 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 백업에서 복원하려면 백업 자격 증명 모음의 관리 ID에 디스크를 복원할 리소스 그룹에 대한 권한 집합이 필요합니다.

백업 자격 증명 모음에는 시스템 할당 관리 ID가 사용됩니다. 이 ID는 리소스당 하나로 제한되며 이 리소스의 수명 주기에 연결됩니다. Azure RBAC(역할 기반 액세스 제어)를 사용하여 관리 ID에 대한 권한을 부여할 수 있습니다. 관리 ID는 Azure 리소스에서만 사용할 수 있는 특수 유형의 서비스 주체입니다. [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

복원 작업을 수행하려면 다음 전제 조건이 필요합니다.

1. Azure Backup 서비스에서 디스크를 복원할 리소스 그룹의 백업 자격 증명 모음 관리 ID에 **디스크 복원 운영자** 역할을 할당합니다.

    >[!NOTE]
    > 백업이 수행되는 원본 디스크의 동일한 리소스 그룹을 선택하거나 동일한 구독 또는 다른 구독 내의 다른 리소스 그룹을 선택할 수 있습니다.

    1. 디스크를 복원할 리소스 그룹으로 이동합니다. 예를 들어 리소스 그룹은 *TargetRG* 입니다.

    1. **액세스 제어(IAM)** 로 이동하여 **역할 할당 추가** 를 선택합니다.

    1. 오른쪽 컨텍스트 창의 **역할** 드롭다운 목록에서 **디스크 복원 운영자** 를 선택합니다. 백업 자격 증명 모음의 관리 ID를 선택하고 **저장** 합니다.

        >[!TIP]
        >백업 자격 증명 모음의 이름을 입력하여 자격 증명 모음의 관리 ID를 선택합니다.

        ![디스크 복원 운영자 역할 선택](./media/restore-managed-disks/disk-restore-operator-role.png)

1. 백업 자격 증명 모음의 관리 ID에 디스크가 복원될 리소스 그룹에서 올바른 역할 할당 집합이 있는지 확인합니다.

    1. **백업 자격 증명 모음 -> ID** 로 이동하여 **Azure 역할 할당** 을 선택합니다.

        ![Azure 역할 할당 선택](./media/restore-managed-disks/azure-role-assignments.png)

    1. 역할, 리소스 이름 및 리소스 유형이 올바르게 표시되는지 확인합니다.

        ![역할, 리소스 이름 및 리소스 유형 확인](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >역할 할당이 포털에 올바르게 반영되는 동안 백업 자격 증명 모음의 관리 ID에 권한이 적용되는 데 약 15분이 걸릴 수 있습니다.
    >
    >Azure Backup은 디스크 백업을 구성하는 동안 제공된 스냅샷 리소스 그룹에 디스크 증분 스냅샷을 예약된 백업 또는 주문형 백업 작업 중에 저장합니다. Azure Backup은 복원 작업 중에 이러한 증분 스냅샷을 사용합니다. 스냅샷이 스냅샷 리소스 그룹에서 삭제되거나 이동되거나 스냅샷 리소스 그룹에서 백업 자격 증명 모음 역할 할당이 취소되면 복원 작업이 실패합니다.

1. 복원할 디스크가 [고객 관리형 키(CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) 또는 [플랫폼 관리 키 및 고객 관리 키를 사용한 이중 암호화](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)를 사용하여 암호화된 경우 **디스크 암호화 세트** 리소스에서 백업 자격 증명 모음의 관리 ID에 대한 **판독기** 역할 권한을 할당합니다.

필수 구성 요소가 충족되면 다음 단계에 따라 복원 작업을 수행하세요.

1. [Azure Portal](https://portal.azure.com/)에서 **백업 센터** 로 이동합니다. **관리** 섹션에서 **백업 인스턴스** 를 선택합니다. 백업 인스턴스 목록에서 복원 작업을 수행할 디스크 백업 인스턴스를 선택합니다.

    ![백업 인스턴스 목록](./media/restore-managed-disks/backup-instances.png)

    또는 디스크 백업을 구성하는 데 사용한 백업 자격 증명 모음에서 이 작업을 수행할 수 있습니다.

1. **백업 인스턴스** 화면에서 복원 작업을 수행하는 데 사용할 복원 지점을 선택하고 **복원** 을 선택합니다.

    ![복원 지점 선택](./media/restore-managed-disks/select-restore-point.png)

1. **복원** 워크플로에서 **기본** 및 **복구 지점 선택** 탭 정보를 검토하고 **다음: 매개 변수 복원** 을 선택합니다.

    ![기본 사항 검토 및 복구 지점 정보 선택](./media/restore-managed-disks/review-information.png)

1. **복원 매개 변수** 탭에서 백업을 복원할 **대상 구독** 및 **대상 리소스 그룹** 을 선택합니다. 복원할 디스크의 이름을 제공합니다. **다음: 검토 + 복원** 을 선택합니다.

    ![복원 매개 변수](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Disk Backup 솔루션을 사용하여 Azure Backup에서 백업 중인 디스크는 Recovery Services 자격 증명 모음과 함께 Azure VM 백업 솔루션을 사용하여 Azure Backup으로 백업할 수도 있습니다. 이 디스크가 연결된 Azure VM의 보호를 구성한 경우 Azure VM 복원 작업을 사용할 수도 있습니다. 해당 Azure VM 백업 인스턴스의 복구 지점에서 VM 또는 디스크와 파일 또는 폴더를 복원하도록 선택할 수 있습니다. 자세한 내용은 [Azure VM 백업](./about-azure-vm-restore.md)을 참조하세요.

1. 유효성 검사에 성공하면 **복원** 을 선택하여 복원 작업을 시작합니다.

    ![복원 작업 시작](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > 복원 작업을 트리거하기 전에 유효성 검사를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 다음과 같은 경우 유효성 검사가 실패할 수 있습니다.
    >
    > - **복원된 디스크 이름** 에 제공된 동일한 이름의 디스크가 이미 **대상 리소스 그룹** 에 있습니다.
    > - 백업 자격 증명 모음 관리 ID에 **대상 리소스 그룹** 에 대한 유효한 역할 할당이 없습니다.
    > - 증분 스냅샷이 저장된 **스냅샷 리소스 그룹** 에서 백업 자격 증명 모음의 관리 ID 역할 할당이 취소됩니다.
    > - 증분 스냅샷이 삭제되거나 스냅샷 리소스 그룹에서 이동된 경우

복원은 복원 작업 중에 제공된 대상 리소스 그룹의 선택한 복구 지점에서 새 디스크를 만듭니다. 기존 가상 머신에서 복원된 디스크를 사용하려면 추가 단계를 수행해야 합니다.

- 복원된 디스크가 데이터 디스크인 경우 기존 디스크를 가상 머신에 연결할 수 있습니다. 복원된 디스크가 OS 디스크인 경우 Azure Portal의 **설정** 섹션에 있는 **가상 머신** 창 -> **디스크** 메뉴에서 가상 머신의 OS 디스크를 바꿀 수 있습니다.

    ![OS 디스크 교체](./media/restore-managed-disks/swap-os-disks.png)

- Windows 가상 머신의 경우 복원된 디스크가 데이터 디스크인 경우 지침에 따라 가상 머신에서 [원래 데이터 디스크를 분리](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal)하세요. 그런 다음 가상 머신에 [복원된 디스크를 연결](../virtual-machines/windows/attach-managed-disk-portal.md)합니다. 지침에 따라 가상 머신의 [OS 디스크를 복원된 디스크로 교체](../virtual-machines/windows/os-disk-swap.md)합니다.

- Linux 가상 머신의 경우 복원된 디스크가 데이터 디스크인 경우 지침에 따라 가상 머신에서 [원래 데이터 디스크를 분리](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal)합니다. 그런 다음 가상 머신에 [복원된 디스크를 연결](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk)합니다. 지침에 따라 가상 머신의 [OS 디스크를 복원된 디스크로 교체](../virtual-machines/linux/os-disk-swap.md)합니다.

복원 작업을 성공적으로 완료한 후에는 **대상 리소스 그룹** 에 있는 백업 자격 증명 모음의 관리 ID에서 **디스크 복원 운영자** 역할 할당을 취소하는 것이 좋습니다.

## <a name="track-a-restore-operation"></a>복원 작업 추적

복원 작업이 트리거되면 백업 서비스에서 추적 작업을 만듭니다. Azure Backup에서 작업에 대한 알림을 포털에 표시합니다. 복원 작업 진행률을 보려면:

1. **백업 인스턴스** 화면으로 이동합니다. 지난 7일 동안의 작업 및 상태가 포함된 작업 대시보드를 표시합니다.

    ![작업 대시보드](./media/restore-managed-disks/jobs-dashboard.png)

1. 복원 작업의 상태를 보려면 **모두 보기** 를 선택하여 이 백업 인스턴스의 진행 중인 작업과 과거 작업을 표시합니다.

    ![모두 보기 선택](./media/restore-managed-disks/view-all.png)

1. 백업 및 복원 작업 목록과 해당 상태를 검토합니다. 작업 목록에서 작업을 선택하여 작업 세부 정보를 봅니다.

    ![작업 목록](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>다음 단계

- [Azure Disk Backup FAQ](disk-backup-faq.md)