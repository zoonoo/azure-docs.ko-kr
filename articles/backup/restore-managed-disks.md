---
title: Azure Managed Disks 복원
description: Azure Portal에서 Azure Managed Disks를 복원 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: b9c9a22f25a8003151217bec15b618e3c380e67e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737379"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Azure Managed Disks 복원 (미리 보기)

>[!IMPORTANT]
>Azure Disk Backup은 서비스 수준 계약 없이 미리 보기 상태 이며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 지역 가용성은 [지원 매트릭스](disk-backup-support-matrix.md)를 참조 하세요.
>
>미리 보기에 등록 하려면 [이 양식을 작성](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 하세요.

이 문서에서는 Azure Backup으로 만든 복원 지점에서 [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) 를 복원 하는 방법을 설명 합니다.

현재는 백업이 수행 된 원본 디스크를 대체 하 여 복원의 OLR (Original-Location 복구) 옵션이 지원 되지 않습니다. 복구 지점에서 복원 하 여 백업이 수행 된 원본 디스크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR (Alternate-Location 복구) 라고 하며,이를 통해 원본 디스크와 복원 된 (새) 디스크를 모두 유지할 수 있습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 디스크를 만들기 위해 복원

- 복원 작업 상태 추적

## <a name="restore-to-create-a-new-disk"></a>새 디스크를 만들기 위해 복원

백업 자격 증명 모음은 관리 Id를 사용 하 여 다른 Azure 리소스에 액세스 합니다. 백업에서 복원 하려면 백업 자격 증명 모음의 관리 되는 id에 디스크를 복원 하는 리소스 그룹에 대 한 사용 권한 집합이 필요 합니다.

백업 자격 증명 모음에는 시스템 할당 관리 id가 사용 됩니다 .이 id는 리소스 당 하나로 제한 되며이 리소스의 수명 주기에 연결 됩니다. Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 관리 되는 id에 대 한 권한을 부여할 수 있습니다. 관리 id는 Azure 리소스에만 사용할 수 있는 특수 형식의 서비스 사용자입니다. [관리 id](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

복원 작업을 수행 하려면 다음 필수 구성 요소가 필요 합니다.

1. 디스크가 Azure Backup 서비스에 의해 복원 되는 리소스 그룹의 백업 자격 증명 모음 관리 id에 **디스크 복원 운영자** 역할을 할당 합니다.

    >[!NOTE]
    > 백업 수행 위치에서 원본 디스크와 동일한 또는 다른 구독 내의 다른 리소스 그룹으로 동일한 리소스 그룹을 선택할 수 있습니다.

    1. 디스크가 복원 되는 리소스 그룹으로 이동 합니다. 예를 들어 리소스 그룹은 *Targetrg* 입니다.

    1. **액세스 제어 (IAM)** 로 이동 하 여 **역할 할당 추가** 를 선택 합니다.

    1. 오른쪽 컨텍스트 창의 **역할** 드롭다운 목록에서 **Disk Restore Operator** 를 선택 합니다. 백업 자격 증명 모음에서 관리 되는 id를 선택 하 고 **저장** 합니다.

        >[!TIP]
        >백업 자격 증명 모음 이름을 입력 하 여 자격 증명 모음의 관리 되는 id를 선택 합니다.

        ![디스크 복원 운영자 역할 선택](./media/restore-managed-disks/disk-restore-operator-role.png)

1. 백업 자격 증명 모음 관리 id에 디스크가 복원 되는 리소스 그룹에 대 한 올바른 역할 할당 집합이 있는지 확인 합니다.

    1. **백업 자격 증명 모음-> id** 로 이동 하 고 **Azure 역할 할당** 을 선택 합니다.

        ![Azure 역할 할당 선택](./media/restore-managed-disks/azure-role-assignments.png)

    1. 역할, 리소스 이름 및 리소스 종류가 올바르게 표시 되는지 확인 합니다.

        ![역할, 리소스 이름 및 리소스 종류를 확인 합니다.](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >포털에서 역할 할당이 올바르게 반영 되지만 백업 자격 증명 모음 관리 id에 사용 권한을 적용 하는 데 약 15 분이 걸릴 수 있습니다.
    >
    >예약 된 백업 또는 요청 시 백업 작업 중 Azure Backup는 디스크의 백업을 구성 하는 동안 제공 된 스냅숏 리소스 그룹에 디스크 증분 스냅숏을 저장 합니다. Azure Backup은 복원 작업 중에 이러한 증분 스냅숏을 사용 합니다. 스냅숏이 스냅숏 리소스 그룹에서 삭제 또는 이동 되거나 스냅숏 리소스 그룹에서 백업 자격 증명 모음 역할 할당이 취소 된 경우에는 복원 작업이 실패 합니다.

1. 복원할 디스크를 [CMK (고객 관리 키)](https://docs.microsoft.com/azure/virtual-machines/disks-enable-customer-managed-keys-portal) 로 암호화 하거나 [플랫폼 관리 키 및 고객 관리 키를 사용 하 여 이중 암호화](https://docs.microsoft.com/azure/virtual-machines/disks-enable-double-encryption-at-rest-portal)를 사용 하는 경우 **디스크 암호화 집합** 리소스에서 백업 자격 증명 모음의 관리 되는 id에 **읽기** 역할 권한을 할당 합니다.

필수 구성 요소가 충족 되 면 다음 단계에 따라 복원 작업을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Backup center** 로 이동 합니다. **관리** 섹션 아래에서 **백업 인스턴스** 를 선택 합니다. 백업 인스턴스 목록에서 복원 작업을 수행 하려는 디스크 백업 인스턴스를 선택 합니다.

    ![백업 인스턴스 목록](./media/restore-managed-disks/backup-instances.png)

    또는 디스크에 대 한 백업을 구성 하는 데 사용한 백업 자격 증명 모음에서이 작업을 수행할 수 있습니다.

1. **백업 인스턴스** 화면에서 복원 작업을 수행 하는 데 사용할 복원 지점을 선택 하 고 **복원** 을 선택 합니다.

    ![복원 지점 선택](./media/restore-managed-disks/select-restore-point.png)

1. **복원** 워크플로에서 **기본 사항을** 검토 하 고 **복구 지점** 탭 정보를 선택한 후 **다음: 매개 변수 복원** 을 선택 합니다.

    ![기본 사항을 검토 하 고 복구 지점 정보를 선택 합니다.](./media/restore-managed-disks/review-information.png)

1. **복원 매개 변수** 탭에서 백업을 복원 하려는 **대상 구독** 및 **대상 리소스 그룹** 을 선택 합니다. 복원할 디스크의 이름을 제공 합니다. **다음: 검토 + 복원** 을 선택 합니다.

    ![복원 매개 변수](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >디스크 백업 솔루션을 사용 하 여 Azure Backup에 의해 백업 되는 디스크는 Recovery Services 자격 증명 모음과 함께 Azure VM 백업 솔루션을 사용 하 여 Azure Backup도 백업할 수 있습니다. 이 디스크가 연결 된 Azure VM에 대 한 보호를 구성한 경우 Azure VM 복원 작업을 사용할 수도 있습니다. 해당 하는 Azure VM 백업 인스턴스의 복구 지점에서 VM, 디스크 및 파일 또는 폴더를 복원 하도록 선택할 수 있습니다. 자세한 내용은 [AZURE VM backup](./about-azure-vm-restore.md)을 참조 하세요.

1. 유효성 검사에 성공 하면 **복원** 을 선택 하 여 복원 작업을 시작 합니다.

    ![복원 작업 시작](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > 복원 작업을 트리거하기 전에 유효성 검사를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 다음의 경우 유효성 검사에 실패할 수 있습니다.
    >
    > - **복원 된 디스크 이름** 에 제공 된 동일한 이름의 디스크가 **대상 리소스 그룹** 에 이미 있습니다.
    > - 백업 자격 증명 모음의 관리 되는 id가 **대상 리소스 그룹** 에 유효한 역할 할당을가지고 있지 않습니다.
    > - 증분 스냅숏이 저장 된 **스냅숏 리소스 그룹** 에서 백업 자격 증명 모음의 관리 되는 id 역할 할당이 취소 되었습니다.
    > - 스냅숏 리소스 그룹에서 증분 스냅숏이 삭제 되거나 이동 되는 경우

복원 작업을 수행 하는 동안 지정 된 대상 리소스 그룹의 선택한 복구 지점에서 새 디스크를 만듭니다. 기존 가상 머신에서 복원 된 디스크를 사용 하려면 추가 단계를 수행 해야 합니다.

- 복원 된 디스크가 데이터 디스크인 경우 기존 디스크를 가상 컴퓨터에 연결할 수 있습니다. 복원 된 디스크가 OS 디스크인 **경우 가상 컴퓨터 창의 Azure Portal** 에서 가상 컴퓨터의 os 디스크를 교환할 수 있습니다 .이 디스크는 **설정** 섹션의 > **디스크** 메뉴에 있습니다.

    ![OS 디스크 교체](./media/restore-managed-disks/swap-os-disks.png)

- Windows 가상 컴퓨터의 경우 복원 된 디스크가 데이터 디스크인 경우 지침에 따라 가상 컴퓨터에서 [원래 데이터 디스크를 분리](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) 합니다. 그런 다음, [복원 된 디스크](../virtual-machines/windows/attach-managed-disk-portal.md) 를 가상 머신에 연결 합니다. 지침에 따라 가상 머신의 [OS 디스크](../virtual-machines/windows/os-disk-swap.md) 를 복원 된 디스크로 바꿉니다.

- Linux 가상 머신의 경우 복원 된 디스크가 데이터 디스크인 경우 지침에 따라 가상 머신에서 [원래 데이터 디스크를 분리](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) 합니다. 그런 다음, [복원 된 디스크](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) 를 가상 머신에 연결 합니다. 지침에 따라 가상 머신의 [OS 디스크](../virtual-machines/linux/os-disk-swap.md) 를 복원 된 디스크로 바꿉니다.

복원 작업을 성공적으로 완료 한 후에 **대상 리소스 그룹** 의 백업 자격 증명 모음 관리 Id에서 **디스크 복원 운영자** 역할 할당을 취소 하는 것이 좋습니다.

## <a name="track-a-restore-operation"></a>복원 작업 추적

복원 작업이 트리거되면 백업 서비스에서 추적 작업을 만듭니다. Azure Backup에서 작업에 대한 알림을 포털에 표시합니다. 복원 작업 진행률을 보려면:

1. **백업 인스턴스** 화면으로 이동 합니다. 지난 7 일간 작업 및 상태를 포함 하는 작업 대시보드가 표시 됩니다.

    ![작업 대시보드](./media/restore-managed-disks/jobs-dashboard.png)

1. 복원 작업의 상태를 보려면 **모두 보기** 를 선택 하 여이 백업 인스턴스의 진행 중인 작업과 이전 작업을 표시 합니다.

    ![모두 보기를 선택 합니다.](./media/restore-managed-disks/view-all.png)

1. 백업 및 복원 작업 목록과 해당 상태를 검토 합니다. 작업 목록에서 작업을 선택 하 여 작업 세부 정보를 볼 수 있습니다.

    ![작업 목록](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>다음 단계

- [Azure Disk Backup FAQ](disk-backup-faq.md)