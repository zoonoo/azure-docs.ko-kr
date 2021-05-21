---
title: Azure Cosmos DB에서 Azure Portal을 사용하여 지속적인 백업 및 특정 시점 복원을 구성합니다.
description: Azure Portal을 사용하여 복원 지점을 식별하고 지속적인 백업을 구성하는 방법을 알아봅니다. 여기서는 라이브 및 삭제된 계정을 복원하는 방법을 보여 줍니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 707ef9f60891c1da7c13638e233ee74e78fc20dd
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283940"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>지속적인 백업 및 특정 시점 복원(미리 보기) 구성 및 관리 - Azure Portal 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB의 특정 시점 복원 기능(지속적인 백업 모드)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB의 특정 시점 복원 기능(미리 보기)을 사용하면 컨테이너 내에서 실수로 인한 변경을 복구하거나, 삭제된 계정, 데이터베이스 또는 컨테이너를 복원하거나, 백업이 있던 모든 지역으로 복원할 수 있습니다. 지속적인 백업 모드를 사용하면 지난 30일 이내의 모든 시점으로 복원할 수 있습니다.

이 문서에서는 Azure Portal을 사용하여 복원 지점을 식별하고 지속적인 백업을 구성하는 방법을 설명합니다.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>지속적인 백업으로 계정 프로비전

새 Azure Cosmos DB 계정을 만들 때 **백업 정책** 옵션에 **연속** 모드를 선택하여 새 계정에 특정 시점 복원 기능을 사용하도록 설정합니다. 계정에 이 기능을 사용하도록 설정하면 모든 데이터베이스와 컨테이너를 지속적으로 백업할 수 있습니다. 특정 시점 복원을 사용하면 데이터가 항상 새 계정으로 복원되며, 현재는 기존 계정으로 복원할 수 없습니다.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="지속적인 백업 구성을 사용하여 Azure Cosmos DB 계정 프로비전" border="true":::

## <a name="backup-storage-redundancy"></a>백업 스토리지 중복성

기본적으로 Azure Cosmos DB는 연속 모드 백업 데이터를 로컬 중복 스토리지 Blob에 저장합니다. 영역 중복이 구성된 지역의 경우 백업이 영역 중복 스토리지 Blob에 저장됩니다. 이 모드에서는 백업 스토리지 중복도를 업데이트할 수 없습니다.

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>실수로 수정한 라이브 계정 복원

Azure Portal을 사용하여 라이브 계정이나 라이브 계정으로 선택한 데이터베이스 및 컨테이너를 복원할 수 있습니다. 데이터를 복원하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Cosmos DB 계정으로 이동하여 **특정 시점 복원** 창을 엽니다.

   > [!NOTE]
   > Azure Portal의 복원 창은 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 권한이 있는 경우에만 채워집니다. 이 권한을 설정하는 방법에 대한 자세한 내용은 [백업 및 복원 권한](continuous-backup-restore-permissions.md) 문서를 참조하세요.

1. 복원하려면 다음 세부 정보를 입력합니다.

   * **복원 지점(UTC)** – 지난 30일 이내의 타임스탬프입니다. 해당 타임스탬프에 계정이 있어야 합니다. UTC로 복원 지점을 지정할 수 있습니다. 복원 지점은 초 단위까지 지정할 수 있습니다. [복원 지점을 식별](#event-feed)하는 방법에 대한 도움말을 보려면 **여기를 클릭** 링크를 선택합니다.

   * **위치** – 계정이 복원되는 대상 지역입니다. 계정이 지정된 타임스탬프에 이 지역(예: 미국 서부 또는 미국 동부)에 있어야 합니다. 계정은 원본 계정이 있었던 지역으로만 복원할 수 있습니다.

   * **리소스 복원** – **전체 계정** 또는 **선택한 데이터베이스/컨테이너** 를 복원하도록 선택할 수 있습니다. 데이터베이스와 컨테이너는 지정된 타임스탬프에 있어야 합니다. 선택한 복원 지점 및 위치에 따라 복원 리소스가 채워지므로 사용자가 복원해야 하는 특정 데이터베이스나 컨테이너를 선택할 수 있습니다.

   * **리소스 그룹** - 대상 계정이 만들어지고 복원되는 리소스 그룹입니다. 이 리소스 그룹은 이미 있어야 합니다.

   * **복원 대상 계정** - 대상 계정 이름입니다. 대상 계정 이름은 새 계정을 만들 때와 동일한 지침을 따라야 합니다. 이 계정은 원본 계정이 있는 곳과 동일한 지역에 복원 프로세스를 통해 생성됩니다.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Azure Portal에서 실수로 수정한 라이브 계정 복원" border="true":::

1. 위의 매개 변수를 선택한 후 **제출** 단추를 선택하여 복원을 시작합니다. 복원 비용은 데이터의 양과 지정된 지역의 스토리지에 대한 요금에 따라 책정되는 일회성 요금입니다. 자세한 내용은 [가격 책정](continuous-backup-restore-introduction.md#continuous-backup-pricing) 섹션을 참조하세요.

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>이벤트 피드를 사용하여 복원 시간 파악

Azure Portal의 복원 지점 시간을 입력할 때 복원 지점을 파악하는 데 도움이 필요하면 **여기를 클릭** 링크를 선택하세요. 그러면 이벤트 피드 블레이드로 이동합니다. 이벤트 피드에는 원본 계정의 데이터베이스 및 컨테이너에 대한 완전히 신뢰할 수 있는 create, replace, delete 이벤트 목록이 제공됩니다. 

예를 들어 특정 컨테이너를 삭제하거나 업데이트하기 이전의 지점으로 복원하려면 이 이벤트 피드를 확인합니다. 이벤트가 발생한 시간의 내림차순으로 표시되고 최근 이벤트가 위쪽에 표시됩니다. 결과를 찾아보고 이벤트 전후 시간을 선택하여 시간을 더 좁힐 수 있습니다.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="이벤트 피드를 사용하여 복원 지점 시간 파악" border="true":::

> [!NOTE]
> 항목 리소스에 대한 변경 내용은 이벤트 피드에 표시되지 않습니다. 복원에는 항상 지난 30일 이내의 타임스탬프를 수동으로 지정할 수 있습니다(해당 시간에 계정이 있는 경우에 한함).

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>삭제된 계정 복원

삭제된 계정을 삭제일로부터 30일 이내에 Azure Portal을 사용하여 완전히 복원할 수 있습니다. 삭제된 계정을 복원하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 글로벌 검색 창에서 *Azure Cosmos DB* 리소스를 검색합니다. 그러면 기존 계정이 모두 나열됩니다.
1. 그런 다음, **복원** 단추를 선택합니다. 복원 창에는 보존 기간(삭제 시간으로부터 30일) 내에 복원할 수 있는 삭제된 계정의 목록이 표시됩니다.
1. 복원하려는 계정을 선택합니다.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Azure Portal에서 삭제된 계정 복원" border="true":::

   > [!NOTE]
   > 참고: Azure Portal의 복원 창은 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 권한이 있는 경우에만 채워집니다. 이 권한을 설정하는 방법에 대한 자세한 내용은 [백업 및 복원 권한](continuous-backup-restore-permissions.md) 문서를 참조하세요.

1. 복원할 계정을 선택하고 다음 세부 정보를 입력하여 삭제된 계정을 복원합니다.

   * **복원 지점(UTC)** – 지난 30일 이내의 타임스탬프입니다. 해당 타임스탬프에 계정이 있어야 합니다. UTC로 복원 지점을 지정합니다. 복원 지점은 초 단위까지 지정할 수 있습니다.

   * **위치** – 계정을 복원해야 하는 대상 지역입니다. 원본 계정이 지정된 타임스탬프에 이 지역에 있어야 합니다. 예를 들면 미국 서부 또는 미국 동부입니다.  

   * **리소스 그룹** - 대상 계정이 만들어지고 복원되는 리소스 그룹입니다. 이 리소스 그룹은 이미 있어야 합니다.

   * **복원 대상 계정** – 대상 계정 이름은 새 계정을 만들 때와 동일한 지침을 따라야 합니다. 이 계정은 원본 계정이 있는 곳과 동일한 지역에 복원 프로세스를 통해 생성됩니다.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>복원 작업의 상태 추적

복원 작업을 시작한 후 포털의 오른쪽 상단 모서리에 있는 **알림** 벨 아이콘을 선택합니다. 그러면 복원 중인 계정의 상태를 표시하는 링크가 제공됩니다. 복원이 진행 중인 동안에는 계정 상태가 *생성 중* 이고, 복원 작업이 완료된 후에는 계정 상태가 *온라인* 으로 변경됩니다.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="" border="true":::작업이 완료되면 복원된 계정의 상태가 생성 중에서 온라인으로 변경됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md) 또는 [Azure Resource Manager](continuous-backup-restore-template.md)를 사용하여 지속적인 백업 구성 및 관리
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한 관리](continuous-backup-restore-permissions.md)
