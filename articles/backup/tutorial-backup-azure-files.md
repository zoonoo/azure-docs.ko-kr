---
title: 자습서 - Azure Files 파일 공유 백업
description: 이 자습서에서는 Azure Portal을 사용하여 Recovery Services 자격 증명 모음을 구성하고 Azure 파일 공유를 백업하는 방법에 대해 알아봅니다.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: f3c1956a0344d300fff9a745a4733b7bef45a367
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171861"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Azure Portal의 Azure 파일 공유 백업

이 자습서에서는 Azure Portal을 사용하여 [Azure 파일 공유](../storage/files/storage-files-introduction.md)를 백업하는 방법을 설명합니다.

이 가이드에서는 다음 작업 방법을 배웁니다.
> [!div class="checklist"]
>
> * Azure 파일을 백업하도록 Recovery Services 자격 증명 모음 구성
> * 주문형 백업 작업을 실행하여 복원 지점 만들기

## <a name="prerequisites"></a>필수 조건

Azure 파일 공유를 백업하려면 파일 공유가 [지원되는 스토리지 계정 유형](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) 중 하나에 있는지 확인합니다. 이를 확인한 후에는 파일 공유를 보호할 수 있습니다.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>미리 보는 동안 Azure 파일 공유 백업을 위한 제한 사항

Azure 파일 공유를 위한 백업은 미리 보기에 있습니다. 범용 v1 및 범용 v2 스토리지 계정 둘 다에서 Azure 파일 공유가 지원됩니다. 다음 백업 시나리오에는 Azure 파일 공유가 지원되지 않습니다.

* Virtual Networks 또는 방화벽을 사용하도록 설정된 스토리지 계정에서 Azure 파일 공유를 보호할 수 없습니다.
* Azure Backup을 사용하여 Azure Files를 보호할 수 있는 CLI가 없습니다.
* 일별 최대 예약 백업의 수는 1개입니다.
* 일별 최대 주문형 백업의 수는 4개입니다.
* 스토리지 계정에서 [리소스 잠금](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)을 사용하면 Recovery Services 자격 증명 모음에서 Backup이 실수로 삭제되는 것을 방지할 수 있습니다.
* Azure Backup으로 생성된 스냅샷은 삭제하지 마십시오. 스냅샷을 삭제하면 복구 지점이 손실되거나 복원이 실패할 수 있습니다.
* Azure Backup으로 보호되는 파일 공유는 삭제하지 마세요. 현재 솔루션은 파일 공유가 삭제되면 Azure Backup에서 만든 모든 스냅샷을 삭제하므로 모든 복원 지점이 손실됩니다.

ZRS([지역 중복 스토리지](../storage/common/storage-redundancy-zrs.md)) 복제 기능을 지원하는 스토리지 계정의 Azure 파일 공유 백업은 현재 CUS(미국 중부), EUS(미국 동부), EUS2(미국 동부2), NE(북유럽), SEA(동남 아시아), WE(서유럽) 및 WUS2(미국 서부 2)에서만 사용할 수 있습니다.

## <a name="configuring-backup-for-an-azure-file-share"></a>Azure 파일 공유를 위한 백업 구성

이 자습서에서는 이미 Azure 파일 공유를 설정한 것으로 가정합니다. Azure 파일 공유를 백업하려면:

1. 파일 공유와 동일한 지역에 Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음이 이미 있는 경우 자격 증명 모음의 개요 페이지를 열고 **Backup**을 클릭합니다.

    ![자격 증명 모음의 개요 페이지에서 백업 클릭](./media/backup-file-shares/overview-backup-page.png)

2. **백업 목표** 메뉴의 **백업할 항목**에서 Azure 파일 공유를 선택합니다.

    ![Backup 목표로 Azure 파일 공유 선택](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. **Backup**을 클릭하여 Recovery Services 자격 증명 모음에 대한 Azure 파일 공유를 구성합니다.

   ![Backup을 클릭하여 Azure 파일 공유를 자격 증명 모음과 연결](./media/backup-file-shares/set-backup-goal.png)

    자격 증명 모음이 Azure 파일 공유와 연결되면 Backup 메뉴가 열리고 스토리지 계정을 선택하라는 메시지가 표시됩니다. 메뉴에는 개발자의 자격 증명 모음이 있는 지역에서 아직 Recovery Services 자격 증명 모음과 연결되지 않은 모든 지원되는 스토리지 계정이 표시됩니다.

   ![사용자의 스토리지 계정을 선택합니다.](./media/backup-file-shares/list-of-storage-accounts.png)

4. 스토리지 계정 목록에서 계정을 선택하고 **확인**을 클릭합니다. Azure는 스토리지 계정에서 백업 가능한 파일 공유를 검색합니다. 최근에 파일 공유를 추가했는데 목록에 보이지 않으면 파일 공유가 표시될 때까지 잠시 기다립니다.

   ![파일 공유 검색 중](./media/backup-file-shares/discover-file-shares.png)

5. **파일 공유** 목록에서 백업하려는 파일 공유를 하나 이상 선택하고 **확인**을 클릭합니다.

6. 파일 공유를 선택하면 Backup 메뉴가 **Backup 정책**으로 전환됩니다. 이 메뉴에서 기존 백업 정책을 선택하거나 새 백업 정책을 만든 후 **Backup 사용**을 클릭합니다.

   ![백업 정책 선택 또는 새 백업 정책 만들기](./media/backup-file-shares/apply-backup-policy.png)

    백업 정책을 설정한 후에는 예정된 시간에 파일 공유의 스냅샷이 생성되고, 선택한 기간 동안 복구 지점이 유지됩니다.

## <a name="create-an-on-demand-backup"></a>주문형 백업 만들기

백업 정책을 구성한 후 다음 예약된 백업까지 데이터를 보호하기 위해 주문형 백업을 만들 수 있습니다.

### <a name="to-create-an-on-demand-backup"></a>주문형 백업을 만들려면

1. 파일 공유 복구 지점이 포함된 Recovery Services 자격 증명 모음을 열고 **Backup 항목**을 클릭합니다. Backup 항목 형식 목록이 표시됩니다.

   ![백업 항목 목록](./media/backup-file-shares/list-of-backup-items.png)

2. 목록에서 **Azure Storage(Azure Files)** 를 선택합니다. Azure 파일 공유 목록이 나타납니다.

   ![Azure 파일 공유 목록](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Azure 파일 공유 목록에서 원하는 파일 공유를 선택합니다. 선택한 파일 공유에 대한 Backup 항목 메뉴가 열립니다.

   ![선택한 파일 공유에 대한 Backup 항목 메뉴](./media/backup-file-shares/backup-item-menu.png)

4. Backup 항목 메뉴에서 **지금 백업**을 클릭합니다. 이것은 주문형 백업 작업이기 때문에 복구 지점과 연결된 보존 정책이 없습니다. **지금 백업** 대화 상자가 열립니다. 복구 지점을 보존할 마지막 날을 지정합니다.

   ![복구 지점을 보존할 날짜 선택](./media/backup-file-shares/backup-now-menu.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal을 사용하여 다음을 수행했습니다.

> [!div class="checklist"]
>
> * Azure 파일을 백업하도록 Recovery Services 자격 증명 모음 구성
> * 주문형 백업 작업을 실행하여 복원 지점 만들기

Azure 파일 공유의 백업에서 복원하려면 다음 문서로 진행합니다.

> [!div class="nextstepaction"]
> [Azure 파일 공유의 백업에서 복원](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
