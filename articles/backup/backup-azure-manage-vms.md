---
title: Azure VM 백업 관리 및 모니터링
description: Azure 백업 서비스를 사용하여 Azure VM 백업을 관리하고 모니터링하는 방법을 알아봅니다.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248152"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Azure 백업 서비스를 통해 Azure VM 백업 관리

이 문서에서는 [Azure Backup 서비스를](backup-overview.md)사용하여 백업되는 Azure 가상 시스템(VM)을 관리하는 방법에 대해 설명합니다. 이 문서에서는 볼트 대시보드에서 찾을 수 있는 백업 정보도 요약합니다.

Azure 포털에서 복구 서비스 자격 증명 모음 대시보드는 다음과 같은 자격 증명 모음 정보에 대한 액세스를 제공합니다.

* 최신 백업(최신 복원 지점이기도 함)
* 백업 정책입니다.
* 모든 백업 스냅숏의 총 크기입니다.
* 백업에 사용할 수 있는 VM 수입니다.

대시보드를 사용하고 개별 VM으로 드릴다운하여 백업을 관리할 수 있습니다. 컴퓨터 백업을 시작하려면 대시보드에서 자격 증명 모음을 엽니다.

![슬라이더가 있는 전체 대시보드 보기](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>대시보드에서 VM 보기

볼트 대시보드에서 VM을 보려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.
2. 허브 메뉴에서 **찾아보기를 선택합니다.** 리소스 목록에서 **Recovery Services**를 입력합니다. 입력할 때 입력에 따라 목록이 필터링됩니다. **Recovery Services 자격 증명 모음**을 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 사용 편의성을 위해 볼트를 마우스 오른쪽 단추로 클릭하고 **대시보드에 Pin을 선택합니다.**
4. 자격 증명 모음 대시보드를 엽니다.

    ![볼트 대시보드 및 설정 창 열기](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. 백업 **항목** 타일에서 **Azure 가상 컴퓨터를**선택합니다.

    ![백업 항목 타일 열기](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. 백업 **항목** 창에서 보호된 VM 목록을 볼 수 있습니다. 이 예제에서 볼트는 하나의 가상 시스템인 데모백업을 보호합니다.  

    ![백업 항목 창 보기](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. 볼트 항목의 대시보드에서 백업 정책을 수정하고, 주문형 백업을 실행하고, VM 보호를 중지 또는 다시 시작하고, 백업 데이터를 삭제하고, 복원 지점을 보고, 복원을 실행합니다.

    ![백업 항목 대시보드 및 설정 창](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>VM에 대한 백업 정책 관리

백업 정책을 관리하려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com/)로그인합니다. 자격 증명 모음 대시보드를 엽니다.
2. 백업 **항목** 타일에서 **Azure 가상 컴퓨터를**선택합니다.

    ![백업 항목 타일 열기](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. 백업 **항목** 창에서 보호된 VM 목록과 최신 복원 지점 시간을 통해 마지막 백업 상태를 볼 수 있습니다.

    ![백업 항목 창 보기](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. 볼트 항목의 대시보드에서 백업 정책을 선택할 수 있습니다.

   * 정책을 전환하려면 다른 정책을 선택한 다음 **저장을**선택합니다. 자격 증명 모음에 새 정책이 즉시 적용됩니다.

     ![백업 정책 선택](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

보호를 설정한 후 VM의 주문형 백업을 실행할 수 있습니다. 다음 세부 정보를 염두에 두십시오.

* 초기 백업이 보류 중인 경우 온디맨드 백업은 복구 서비스 자격 증명 모음에서 VM의 전체 복사본을 만듭니다.
* 초기 백업이 완료되면 온디맨드 백업은 이전 스냅숏의 변경 내용만 복구 서비스 자격 증명 모음으로 보냅니다. 즉, 이후 백업은 항상 증분됩니다.
* 온디맨드 백업의 보존 범위는 백업을 트리거할 때 지정한 보존 값입니다.

요청 시 백업을 트리거하려면 다음을 수행합니다.

1. 볼트 [항목 대시보드에서](#view-vms-on-the-dashboard) **보호된 항목**아래에서 **백업 항목을**선택합니다.

    ![지금 백업 옵션](./media/backup-azure-manage-vms/backup-now-button.png)

2. **백업 관리 유형에서** **Azure 가상 컴퓨터를**선택합니다. **백업 항목(Azure 가상 시스템)** 창이 나타납니다.
3. VM을 선택하고 **지금 백업을** 선택하여 주문형 백업을 만듭니다. **지금 백업** 창이 나타납니다.
4. 백업 **까지 유지** 필드에서 백업유지 날짜를 지정합니다.

    ![지금 백업 캘린더](./media/backup-azure-manage-vms/backup-now-check.png)

5. 백업 작업을 실행하려면 **확인을** 선택합니다.

작업의 진행률을 추적하려면 볼트 대시보드에서 백업 작업 타일을 **선택합니다.**

## <a name="stop-protecting-a-vm"></a>VM 보호 중지

VM 보호를 중지하는 방법에는 두 가지가 있습니다.

* **보호를 중지하고 백업 데이터를 유지합니다.** 이 옵션을 사용하면 이후의 모든 백업 작업이 VM을 보호하지 못하게 됩니다. 그러나 Azure Backup 서비스는 백업된 복구 지점을 유지합니다.  볼트의 복구 지점을 유지하려면 비용을 지불해야 합니다(자세한 내용은 [Azure Backup 가격](https://azure.microsoft.com/pricing/details/backup/) 참조). 필요한 경우 VM을 복원할 수 있습니다. VM 보호를 다시 시작하려는 경우 *백업 다시 시작* 옵션을 사용할 수 있습니다.
* **보호를 중지하고 백업 데이터를 삭제합니다.** 이 옵션을 사용하면 이후의 모든 백업 작업이 VM을 보호하지 못하게 되고 모든 복구 지점이 삭제됩니다. VM을 복원하거나 *백업 다시 시작* 옵션을 사용할 수 없습니다.

>[!NOTE]
>백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다. 이전 복구 지점은 정책에 따라 만료되지만 백업을 중지하고 데이터를 삭제할 때까지 마지막 복구 지점이 항상 유지됩니다.
>

### <a name="stop-protection-and-retain-backup-data"></a>보호 중지 및 백업 데이터 유지

보호를 중지하고 VM의 데이터를 유지하려면 다음을 수행하십시오.

1. 볼트 [항목의 대시보드에서](#view-vms-on-the-dashboard) **백업 중지를**선택합니다.
2. **백업 데이터 유지를**선택하고 필요에 따라 선택 사항을 확인합니다. 원하는 경우 주석을 추가합니다. 항목 이름이 확실하지 않은 경우 느낌표 위로 마우스를 가져가이름을 봅니다.

    ![백업 데이터 유지](./media/backup-azure-manage-vms/retain-backup-data.png)

알림을 통해 백업 작업이 중지되었음을 알 수 있습니다.

### <a name="stop-protection-and-delete-backup-data"></a>보호 중지 및 백업 데이터 삭제

보호를 중지하고 VM의 데이터를 삭제하려면 다음을 수행하십시오.

1. 볼트 [항목의 대시보드에서](#view-vms-on-the-dashboard) **백업 중지를**선택합니다.
2. **백업 데이터 삭제를**선택하고 필요에 따라 선택 사항을 확인합니다. 원하는 경우 백업 항목의 이름을 입력하고 주석을 추가합니다.

    ![백업 데이터 삭제](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>VM 보호 다시 시작

중지 VM 보호 중에 보호 중지 및 백업 데이터 옵션을 [유지하도록](#stop-protection-and-retain-backup-data) 선택한 경우 **백업 백업 백업 을**사용할 수 있습니다. 보호 중지 및 백업 [데이터 삭제](#stop-protection-and-delete-backup-data) 옵션을 선택하거나 [백업 데이터 삭제를](#delete-backup-data)선택하면 이 옵션을 사용할 수 없습니다.

VM에 대한 보호를 다시 시작하려면 다음을 수행하십시오.

1. 볼트 [항목의 대시보드에서](#view-vms-on-the-dashboard) **백업 다시 시작을**선택합니다.

2. 백업 정책 [관리의](#manage-backup-policy-for-a-vm) 단계를 수행하여 VM에 대한 정책을 할당합니다. VM의 초기 보호 정책을 선택할 필요가 없습니다.
3. VM에 백업 정책을 적용하면 다음 메시지가 표시됩니다.

    ![성공적으로 보호된 VM을 나타내는 메시지](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>백업 데이터 삭제

VM의 백업 데이터를 삭제하는 방법에는 두 가지가 있습니다.

* 볼트 항목 대시보드에서 백업 중지를 선택하고 [보호 중지 및 백업 데이터 삭제](#stop-protection-and-delete-backup-data) 옵션에 대한 지침을 따릅니다.

  ![백업 중지 선택](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* 볼트 항목 대시보드에서 백업 데이터 삭제를 선택합니다. 이 옵션은 VM 보호 중지 중에 보호 중지 및 백업 데이터 옵션을 [유지하도록](#stop-protection-and-retain-backup-data) 선택한 경우 사용할 수 있습니다.

  ![백업 삭제 선택](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * 볼트 [항목 대시보드에서](#view-vms-on-the-dashboard) **백업 데이터 삭제를**선택합니다.
  * 복구 지점을 삭제할 지 확인하려면 백업 항목의 이름을 입력합니다.

    ![백업 데이터 삭제](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * 항목의 백업 데이터를 삭제하려면 **을 선택합니다.** 알림 메시지를 통해 백업 데이터가 삭제되었음을 알 수 있습니다.

데이터를 보호하기 위해 Azure Backup에는 소프트 삭제 기능이 포함되어 있습니다. VM의 백업(모든 복구 지점)이 삭제된 후에도 일시 삭제하면 백업 데이터가 14일 동안 유지됩니다. 자세한 내용은 [소프트 삭제 문서를](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud)참조하십시오.

  > [!NOTE]
  > 백업 데이터를 삭제하면 연결된 모든 복구 지점을 삭제합니다. 삭제할 특정 복구 지점을 선택할 수 없습니다.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>기본 데이터 원본이 더 이상 존재하지 않는 백업 항목

* Azure 백업용으로 구성된 Azure VM이 보호 를 중지하지 않고 삭제되거나 이동된 경우 예약된 백업 작업과 주문형(임시) 백업 작업이 모두 UserErrorVmNotFoundV2 오류로 실패합니다. 백업 사전 검사는 실패한 온디맨드 백업 작업에 대해서만 중요한 것으로 나타납니다(실패한 예약된 작업이 표시되지 않음).
* 이러한 백업 항목은 사용자가 설정한 백업 및 보존 정책을 준수하여 시스템에서 활성 상태로 유지됩니다. 이러한 Azure VM에 대한 백업된 데이터는 보존 정책에 따라 유지됩니다. 만료된 복구 지점(마지막 복구 지점 제외)은 백업 정책에 설정된 보존 범위에 따라 정리됩니다.
* 마지막 복구 지점이 영원히 유지되고 사용자에게 적용 가능한 백업 가격 책정에 따라 요금이 부과되므로 삭제 리소스에 대한 백업 항목/데이터가 더 이상 필요하지 않은 경우 기본 데이터 원본이 더 이상 존재하지 않는 백업 항목을 삭제하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [VM의 설정에서 Azure VM을 백업하는](backup-azure-vms-first-look-arm.md)방법에 대해 알아봅니다.
* [VM을 복원하는](backup-azure-arm-restore-vms.md)방법에 대해 알아봅니다.
* [Azure VM 백업을 모니터링하는](backup-azure-monitor-vms.md)방법에 대해 알아봅니다.
