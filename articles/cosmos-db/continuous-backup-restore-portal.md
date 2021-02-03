---
title: Azure Cosmos DB에서 Azure Portal를 사용 하 여 연속 백업 및 지정 시간 복원을 구성 합니다.
description: Azure Portal를 사용 하 여 복원 지점을 식별 하 고 연속 백업을 구성 하는 방법에 대해 알아봅니다. 이 예제에서는 live 및 deleted 계정을 복원 하는 방법을 보여 줍니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: a5be4c16b93f22037152147ada8c4ba2b4443eba
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527575"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-portal"></a>연속 백업 및 지정 시간 복원 구성 및 관리-Azure Portal 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB의 지정 시간 복원 기능을 사용 하면 삭제 된 계정, 데이터베이스 또는 컨테이너를 복원 하거나 백업이 있던 모든 지역으로 복원 하기 위해 컨테이너 내에서 실수로 인 한 변경 으로부터 복구 하는 데 도움이 됩니다. 연속 백업 모드를 사용 하면 지난 30 일 이내에 모든 시점으로 복원할 수 있습니다.

이 문서에서는 복원 지점을 식별 하 고 Azure Portal를 사용 하 여 연속 백업을 구성 하는 방법을 설명 합니다.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>연속 백업으로 계정 프로 비전

새 Azure Cosmos DB 계정을 만들 때 **백업 정책** 옵션에 대해 **연속** 모드를 선택 하 여 새 계정에 대 한 특정 시점 복원 기능을 사용 하도록 설정 합니다. 계정에 대해이 기능을 사용 하도록 설정한 후에는 모든 데이터베이스와 컨테이너를 연속 백업에 사용할 수 있습니다. 지정 시간 복원을 사용 하면 데이터가 항상 새 계정으로 복원 되며, 현재는 기존 계정으로 복원할 수 없습니다.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="연속 백업 구성을 사용 하 여 Azure Cosmos DB 계정을 프로 비전 합니다." border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>실수로 수정 하지 않도록 라이브 계정 복원

Azure Portal를 사용 하 여 라이브 계정이 나 선택한 데이터베이스 및 컨테이너를 복원할 수 있습니다. 다음 단계를 사용 하 여 데이터를 복원 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Cosmos DB 계정으로 이동 하 여 지정 **시간 복원** 창을 엽니다.

   > [!NOTE]
   > Azure Portal의 복원 창은 사용 권한이 있는 경우에만 채워집니다 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` . 이 사용 권한을 설정 하는 방법에 대 한 자세한 내용은 [백업 및 복원 권한](continuous-backup-restore-permissions.md) 문서를 참조 하세요.

1. 복원 하려면 다음 세부 정보를 입력 합니다.

   * **복원 지점 (UTC)** – 지난 30 일 내에 있는 타임 스탬프입니다. 해당 타임 스탬프에 계정이 있어야 합니다. UTC로 복원 지점을 지정할 수 있습니다. 복원 하려는 경우 두 번째에 가까이 있을 수 있습니다. [복원 지점을 식별](#event-feed)하는 방법에 대 한 도움말을 보려면 **여기를 클릭** 하세요. 링크를 선택 합니다.

   * **Location** – 계정이 복원 되는 대상 지역입니다. 이 영역에는 지정 된 타임 스탬프 (예: 미국 서 부 또는 미국 동부). 계정은 원본 계정이 있었던 지역 으로만 복원할 수 있습니다.

   * **리소스 복원** – 복원할 **전체 계정** 또는 **선택한 데이터베이스/컨테이너** 를 선택할 수 있습니다. 데이터베이스와 컨테이너는 지정 된 타임 스탬프에 있어야 합니다. 선택한 복원 지점 및 위치에 따라 복원 리소스가 채워져 사용자가 복원 해야 하는 특정 데이터베이스나 컨테이너를 선택할 수 있습니다.

   * **리소스 그룹** -대상 계정이 만들어지고 복원 되는 리소스 그룹입니다. 리소스 그룹이 이미 존재 해야 합니다.

   * **복원 대상 계정** -대상 계정 이름입니다. 대상 계정 이름은 새 계정을 만들 때와 동일한 지침을 따라야 합니다. 이 계정은 원본 계정이 있는 동일한 지역의 복원 프로세스에 의해 생성 됩니다.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="실수로 수정 Azure Portal에서 라이브 계정을 복원 합니다." border="true":::

1. 위의 매개 변수를 선택한 후 **제출** 단추를 선택 하 여 복원을 시작 합니다. 복원 비용은 데이터의 양과 지정 된 지역의 저장소에 대 한 요금을 기반으로 하는 일회성 요금입니다. 자세히 알아보려면 [가격 책정](continuous-backup-restore-introduction.md#continuous-backup-pricing) 섹션을 참조 하세요.

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>이벤트 피드를 사용 하 여 복원 시간 식별

Azure Portal의 복원 지점 시간을 채울 때 복원 지점을 식별 하는 데 도움이 필요 하면 **여기를 클릭** 하세요. 링크를 선택 하면 이벤트 피드 블레이드로 이동 합니다. 이벤트 피드에는 원본 계정의 데이터베이스 및 컨테이너에 대 한 create, replace, delete 이벤트의 전체 충실도 목록이 제공 됩니다. 

예를 들어 특정 컨테이너를 삭제 하거나 업데이트 하기 전의 시점으로 복원 하려면이 이벤트 피드를 확인 합니다. 이벤트가 발생 한 시간 순서 대로 시간순으로 표시 되 고 최근 이벤트는 위쪽에 표시 됩니다. 결과를 찾아보고 이벤트 전후 시간을 선택 하 여 시간을 더 좁힐 수 있습니다.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="이벤트 피드를 사용 하 여 복원 지점 시간을 식별할 수 있습니다." border="true":::

> [!NOTE]
> 이벤트 피드에 항목 리소스에 대 한 변경 내용이 표시 되지 않습니다. 복원에는 항상 지난 30 일 동안 (계정이 있는 경우) 타임 스탬프를 수동으로 지정할 수 있습니다.

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>삭제 된 계정 복원

삭제 된 후 30 일 이내에 삭제 된 계정을 완전히 복원 하려면 Azure Portal를 사용할 수 있습니다. 다음 단계를 사용 하 여 삭제 된 계정을 복원 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 전역 검색 창에서 "Azure Cosmos DB" 리소스를 검색 합니다. 모든 기존 계정을 나열 합니다.
1. 그런 다음 **복원** 단추를 선택 합니다. 복원 창에는 보존 기간 내에 복원할 수 있는 삭제 된 계정 목록이 표시 됩니다. 삭제 시간은 30 일입니다.
1. 복원 하려는 계정을 선택 합니다.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Azure Portal에서 삭제 된 계정을 복원 합니다." border="true":::

   > [!NOTE]
   > 참고: Azure Portal의 복원 창은 사용 권한이 있는 경우에만 채워집니다 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` . 이 사용 권한을 설정 하는 방법에 대 한 자세한 내용은 [백업 및 복원 권한](continuous-backup-restore-permissions.md) 문서를 참조 하세요.

1. 복원할 계정을 선택 하 고 다음 세부 정보를 입력 하 여 삭제 된 계정을 복원 합니다.

   * **복원 지점 (UTC)** – 지난 30 일 내에 있는 타임 스탬프입니다. 해당 타임 스탬프에 계정이 있어야 합니다. UTC로 복원 지점을 지정 합니다. 복원 하려는 경우 두 번째에 가까이 있을 수 있습니다.

   * **Location** – 계정을 복원 해야 하는 대상 지역입니다. 원본 계정이 지정 된 타임 스탬프에 있는이 영역에 있어야 합니다. 미국 서 부 또는 미국 동부를 예로 들어 보겠습니다.  

   * **리소스 그룹** -대상 계정이 만들어지고 복원 되는 리소스 그룹입니다. 리소스 그룹이 이미 존재 해야 합니다.

   * **대상 계정 복원** – 대상 계정 이름은 새 계정을 만들 때와 동일한 지침을 따라야 합니다. 이 계정은 원본 계정이 있는 동일한 지역의 복원 프로세스에 의해 생성 됩니다.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>복원 작업의 상태를 추적 합니다.

복원 작업을 시작한 후 포털의 오른쪽 위 모퉁이에 있는 **알림** 벨 아이콘을 선택 합니다. 복원 중인 계정의 상태를 표시 하는 링크를 제공 합니다. 복원이 진행 중인 동안에는 계정 상태가 "만들기"가 되 고 복원 작업이 완료 된 후 계정 상태가 "Online"으로 변경 됩니다.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="작업이 완료 되 면 복원 된 계정 상태가 만들기에서 온라인으로 변경 됩니다." border="true":::

## <a name="next-steps"></a>다음 단계

* [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md)또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용 하 여 연속 백업을 구성 하 고 관리 합니다.
* [연속 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
* 연속 백업 모드를 사용 하 여 데이터를 복원 하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md) 합니다.
