---
title: 포함 파일
description: 포함 파일
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: c111752dcca03781dfb2a11fb100dafc4bad90f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400991"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간이 지남에 따라 만들어지는 복구 지점을 저장하고 백업 관련 작업을 수행하기 위한 인터페이스를 제공하는 관리 엔터티입니다. 여기에는 주문형 백업 수행, 복원 수행 및 백업 정책 만들기가 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

1. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    ![모든 서비스 선택](./media/backup-create-rs-vault/click-all-services.png)

1. **모든 서비스** 대화 상자에서 *Recovery Services*를 입력합니다. 입력 내용에 따라 리소스 목록이 필터링됩니다. 리소스 목록에서 **Recovery Services 자격 증명 모음**을 선택합니다.

    ![Recovery Services 자격 증명 모음 입력 및 선택](./media/backup-create-rs-vault/all-services.png)

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.

1. **Recovery Services 자격 증명 모음** 대시보드에서 **추가**를 선택합니다.

    ![Recovery Services 자격 증명 모음 추가](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Recovery Services 자격 증명 모음** 대화 상자가 열립니다. **이름**, **구독**, **리소스 그룹** 및 **이름**에 대한 값을 입력합니다.

    ![Recovery Services 자격 증명 모음 구성](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**: 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 고유해야 합니다. 2자 이상 50자 이하의 이름을 지정합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다.
   - **구독**: 사용할 구독을 선택합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택한 다음, 드롭다운 목록에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/management/overview.md)를 참조하세요.
   - **위치**: 자격 증명 모음에 대한 지리적 지역을 선택합니다. 데이터 원본을 보호할 자격 증명 모음을 만들려면 자격 증명 모음이 데이터 원본과 동일한 지역에 *있어야* 합니다.

      > [!IMPORTANT]
      > 데이터 원본의 위치를 잘 모를 경우 대화 상자를 닫습니다. 포털에 있는 리소스 목록으로 이동합니다. 데이터 원본이 여러 지역에 있는 경우 각 지역에 Recovery Services 자격 증명 모음을 만듭니다. 첫 번째 위치에서 자격 증명 모음을 만든 후에 다른 위치에 대한 자격 증명 모음을 만듭니다. 백업 데이터를 저장할 스토리지 계정을 지정하지 않아도 됩니다. Recovery Services 자격 증명 모음 및 Azure Backup은 이를 자동으로 처리합니다.
      >
      >

1. 값이 제공되면 **검토 및 만들기**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기 프로세스에서 검토 + 만들기 단추를 보여주는 스크린샷.](./media/backup-create-rs-vault/review-and-create.png)

1. Recovery Services 자격 증명 모음을 만들 준비가 되면 **만들기**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services 자격 증명 모음을 만드는 데 어느 정도 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 모서리에 있는 **알림** 영역에서 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에서 볼 수 있습니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

     ![백업 자격 증명 모음 목록 새로 고침](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> 자격 증명 모음에서 백업을 구성하기 전에 **스토리지 복제 유형** 및 **보안 설정**에 대한 기본 설정을 검토하는 것이 좋습니다. 자세한 내용은 [스토리지 중복 설정](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) 섹션을 참조하세요.
