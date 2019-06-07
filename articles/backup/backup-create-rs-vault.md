---
title: 'Azure Backup: Recovery Services 자격 증명 모음 만들기'
description: 백업 및 복구 지점을 저장 하는 Recovery Services 자격 증명 모음 만들기
services: backup
author: sogup
manager: vijayts
keywords: Recovery Services 자격 증명 모음입니다. Azure VM 백업; Azure VM 복원;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: sogup
ms.openlocfilehash: 9fba7d679b7d0edb3c99207c99b23f9616c6fa0e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66477579"
---
# <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면:

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    ![모든 서비스 선택](./media/backup-create-rs-vault/click-all-services.png)

3. **모든 서비스** 대화 상자에서 **Recovery Services**를 입력합니다. 입력 내용에 따라 리소스 목록이 필터링됩니다. 리소스 목록에서 **Recovery Services 자격 증명 모음**을 선택합니다.

    ![Recovery Services 자격 증명 모음 입력 및 선택](./media/backup-create-rs-vault/all-services.png)

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.

4. **Recovery Services 자격 증명 모음** 대시보드에서 **추가**를 선택합니다.

    ![Recovery Services 자격 증명 모음 추가](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Recovery Services 자격 증명 모음** 대화 상자가 열립니다. **이름**, **구독**, **리소스 그룹** 및 **이름**에 대한 값을 입력합니다.

    ![Recovery Services 자격 증명 모음 구성](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **이름**: 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 고유해야 합니다. 2자 이상 50자 이하의 이름을 지정합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다.
   - **구독**: 사용할 구독을 선택합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택한 다음, 드롭다운 목록 상자에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.
   - **Location**: 자격 증명 모음에 대한 지리적 지역을 선택합니다. 가상 머신을 보호할 자격 증명 모음을 만들려면, 자격 증명 모음이 **반드시** 가상 머신과 동일한 지역에 있어야 합니다.

      > [!IMPORTANT]
      > VM의 위치를 잘 모를 경우 대화 상자를 닫습니다. 포털에서 가상 머신의 목록으로 이동합니다. 가상 머신이 여러 지역에 있는 경우 각 지역에 Recovery Services 자격 증명 모음을 만듭니다. 첫 번째 위치에서 자격 증명 모음을 만든 후에 다른 위치에 대한 자격 증명 모음을 만듭니다. 백업 데이터를 저장할 저장소 계정을 지정하지 않아도 됩니다. Recovery Services 자격 증명 모음 및 Azure Backup 서비스는 이를 자동으로 처리합니다.
      >
      >

5. Recovery Services 자격 증명 모음을 만들 준비가 되면 **만들기**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services 자격 증명 모음을 만드는 데 어느 정도 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 모서리에 있는 **알림** 영역에서 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에서 볼 수 있습니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

     ![백업 자격 증명 모음 목록 새로 고침](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>저장소 중복 설정

Azure Backup 자격 증명 모음에 대 한 저장소를 자동으로 처리합니다. 저장소 복제 되는 방식을 지정 해야 합니다.

1. **Recovery Services 자격 증명 모음** 블레이드에서 새 자격 증명 모음을 클릭합니다. 아래는 **설정을** 섹션에서 클릭 **속성**합니다.
2. **속성**아래에 있는 **Backup 구성**, 클릭 **업데이트**합니다.

3. 저장소 복제 유형을 선택 하 고 클릭 **저장할**합니다.

     ![새 자격 증명 모음의 저장소 구성 설정](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - 기본 백업 저장소 끝점으로 Azure를 사용 하는 경우에 계속 기본값을 사용 하는 것이 좋습니다 **지역 중복** 설정 합니다.
   - Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복**을 선택합니다.
   - 에 대해 자세히 알아보세요 [geo](../storage/common/storage-redundancy-grs.md) 하 고 [로컬](../storage/common/storage-redundancy-lrs.md) 중복성입니다.

> [!NOTE]
> 변경 **저장소 복제 유형** (로컬 중복 / 지리 중복)에 대 한 복구 서비스 자격 증명 모음 자격 증명 모음에서 백업을 구성 하기 전에 실행 해야 합니다. 백업을 구성, 수정 하는 옵션은 사용 하지 않도록 설정 하 고 변경할 수 없습니다는 **저장소 복제 유형**합니다. 

## <a name="next-steps"></a>다음 단계

[에 대 한 자세한](backup-azure-recovery-services-vault-overview.md) Recovery Services 자격 증명 모음입니다.
[에 대 한 자세한](backup-azure-delete-vault.md) 삭제 Recovery Services 자격 증명 모음입니다.
