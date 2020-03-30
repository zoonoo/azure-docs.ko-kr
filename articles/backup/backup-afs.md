---
title: Azure Portal의 Azure 파일 공유 백업
description: Azure 포털을 사용하여 복구 서비스 자격 증명 모음에서 Azure 파일 공유를 백업하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938099"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음에서 Azure 파일 공유 백업

이 문서에서는 Azure 포털을 사용하여 [Azure 파일 공유를](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)백업하는 방법을 설명합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* Recovery Services 자격 증명 모음을 만듭니다.
* 파일 공유를 검색하고 백업을 구성합니다.
* 주문형 백업 작업을 실행하여 복원 지점을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* 파일 공유를 호스팅하는 저장소 계정과 동일한 리전에서 [복구 서비스 자격 증명 모음을](#create-a-recovery-services-vault) 식별하거나 만듭니다.
* [지원되는 저장소 계정 유형](#limitations-for-azure-file-share-backup-during-preview)중 하나에 파일 공유가 있는지 확인합니다.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>미리 보는 동안 Azure 파일 공유 백업을 위한 제한 사항

Azure 파일 공유를 위한 백업은 미리 보기에 있습니다. 범용 v1 및 범용 v2 스토리지 계정 둘 다에서 Azure 파일 공유가 지원됩니다. Azure 파일 공유를 백업하는 제한 사항은 다음과 같습니다.

* [ZRS(영역 중복 저장소)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 복제가 있는 저장소 계정의 Azure 파일 공유 백업에 대한 지원은 현재 [이러한 영역으로](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)제한됩니다.
* Azure Backup은 현재 Azure 파일 공유의 예약된 일1일 백업 구성을 지원합니다.
* 일별 최대 예약 백업의 수는 1개입니다.
* 일별 최대 주문형 백업의 수는 4개입니다.
* 저장소 계정의 [리소스 잠금을](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) 사용하여 복구 서비스 자격 증명 모음에서 실수로 백업이 삭제되지 않도록 합니다.
* Azure Backup에서 만든 스냅샷을 삭제하지 마십시오. 스냅숏을 삭제하면 복구 지점이 손실되거나 오류가 복원될 수 있습니다.
* Azure Backup에서 보호하는 파일 공유를 삭제하지 마십시오. 현재 솔루션은 파일 공유가 삭제된 후 Azure Backup에서 수행한 모든 스냅숏을 삭제하므로 모든 복원 지점이 손실됩니다.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>저장소 복제 수정

기본적으로 볼트는 [지리적 중복 저장소(GRS)를](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)사용합니다.

* 볼트가 기본 백업 메커니즘인 경우 GRS를 사용하는 것이 좋습니다.
* [로컬 중복 저장소(LRS)를](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 저렴한 옵션으로 사용할 수 있습니다.

저장소 복제 유형을 수정하려면 다음을 수행하십시오.

1. 새 볼트에서 **설정** 섹션에서 **속성을** 선택합니다.

1. **속성** 페이지에서 백업 **구성에서** **업데이트를**선택합니다.

1. 저장소 복제 유형을 선택하고 **저장을**선택합니다.

    ![백업 구성 업데이트](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 볼트를 설정하고 백업 항목을 포함하는 후에는 저장소 복제 유형을 수정할 수 없습니다. 이렇게 하려면 볼트를 다시 만들어야 합니다.
>

## <a name="discover-file-shares-and-configure-backup"></a>파일 공유 검색 및 백업 구성

1. Azure [포털에서](https://portal.azure.com/)파일 공유를 백업하는 데 사용할 복구 서비스 자격 증명 모음을 엽니다.

1. 복구 **서비스 볼트** 대시보드에서 **+Backup**을 선택합니다.

   ![Recovery Services 자격 증명 모음](./media/backup-afs/recovery-services-vault.png)

    a. **백업 목표에서** **워크로드가 실행 중인 위치를** **Azure로**설정합니다.

    ![백업 목표로 Azure 파일 공유 를 선택합니다.](./media/backup-afs/backup-goal.png)

    b.  **백업할 내용에서**드롭다운 목록에서 **Azure 파일 공유를** 선택합니다.

    다.  **백업을** 선택하여 볼트에 Azure 파일 공유 확장자를 등록합니다.

    ![Azure 파일 공유를 볼트와 연결하려면 백업을 선택합니다.](./media/backup-afs/register-extension.png)

1. **백업을**선택하면 **백업** 창이 열리고 검색된 지원되는 저장소 계정 목록에서 저장소 계정을 선택하라는 메시지가 표시됩니다. 이 자격 증명 모음과 연결되거나 볼트와 동일한 지역에 있지만 아직 복구 서비스 자격 증명 모음에 연결되지 않았습니다.

   ![스토리지 계정 선택](./media/backup-afs/select-storage-account.png)

1. 검색된 저장소 계정 목록에서 계정을 선택하고 **확인을**선택합니다. Azure는 백업할 수 있는 파일 공유에 대한 저장소 계정을 검색합니다. 최근에 파일 공유를 추가한 후 목록에 표시되지 않는 경우 파일 공유가 표시될 때까지 잠시 시간을 내십시오.

    ![파일 공유 검색](./media/backup-afs/discovering-file-shares.png)

1. 파일 **공유** 목록에서 백업할 파일 공유 중 하나 이상을 선택합니다. **확인**을 선택합니다.

1. 파일 공유를 선택하면 **백업** 메뉴가 **백업 정책으로**전환됩니다. 이 메뉴에서 기존 백업 정책을 선택하거나 새 백업 정책을 만듭니다. 그런 다음 **백업 활성화를**선택합니다.

    ![백업 정책 선택](./media/backup-afs/select-backup-policy.png)

백업 정책을 설정한 후 예약된 시간에 파일 공유의 스냅숏이 생성됩니다. 복구 지점도 선택한 기간 동안 유지됩니다.

## <a name="create-an-on-demand-backup"></a>주문형 백업 만들기

경우에 따라 백업 정책에서 예약된 시간 외에 백업 스냅숏 또는 복구 지점을 생성할 수 있습니다. 온디맨드 백업을 생성하는 일반적인 이유는 백업 정책을 구성한 직후입니다. 백업 정책의 일정에 따라 스냅숏이 생성될 때까지 몇 시간 또는 며칠이 소요될 수 있습니다. 백업 정책이 적용될 때까지 데이터를 보호하려면 주문형 백업을 시작합니다. 파일 공유를 계획변경하기 전에 주문형 백업을 만들어야 하는 경우가 많습니다.

### <a name="create-a-backup-job-on-demand"></a>필요에 따라 백업 작업 만들기

1. 파일 공유를 백업하는 데 사용한 복구 서비스 자격 증명 모음을 엽니다. **개요** 창에서 보호된 **항목** 섹션에서 백업 항목을 **선택합니다.**

   ![백업 항목 선택](./media/backup-afs/backup-items.png)

1. **백업 항목을**선택하면 모든 백업 **관리 유형을** 나열하는 새 창이 **개요** 창 옆에 나타납니다.

   ![백업 관리 유형 목록](./media/backup-afs/backup-management-types.png)

1. 백업 **관리 유형** 목록에서 **Azure 저장소(Azure 파일)를**선택합니다. 이 자격 증명 모음을 사용하여 백업된 모든 파일 공유 목록과 해당 저장소 계정 목록이 표시됩니다.

   ![Azure 저장소(Azure 파일) 백업 항목](./media/backup-afs/azure-files-backup-items.png)

1. Azure 파일 공유 목록에서 원하는 파일 공유를 선택합니다. **백업 항목** 세부 정보가 나타납니다. 백업 **항목** 메뉴에서 **지금 백업을 선택합니다.** 이 백업 작업은 요청 시 이므로 복구 지점과 관련 된 보존 정책이 없습니다.

   ![지금 백업 선택](./media/backup-afs/backup-now.png)

1. **지금 백업** 창이 열립니다. 복구 지점을 보존할 마지막 날을 지정합니다. 온디맨드 백업의 경우 최대 10년의 보존 기간을 가질 수 있습니다.

   ![보존 날짜 선택](./media/backup-afs/retention-date.png)

1. **확인을** 선택하여 실행되는 주문형 백업 작업을 확인합니다.

1. 포털 알림을 모니터링하여 백업 작업 실행 완료를 추적합니다. 볼트 대시보드에서 작업 진행 률을 모니터링할 수 있습니다. **진행 중인** **백업 작업을** > 선택합니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:
* [Azure 파일 공유 복원](restore-afs.md)
* [Azure 파일 공유 백업 관리](manage-afs-backup.md)
