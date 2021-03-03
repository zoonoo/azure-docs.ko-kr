---
title: 계획 된 유지 관리 이벤트에 대 한 사전 알림 (미리 보기)
description: Azure SQL Database에 대 한 계획 된 유지 관리 전에 알림을 받습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: 07f6267a14a4604e1a43dd1a1a9930d63a419336
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690892"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>계획 된 유지 관리 이벤트에 대 한 사전 알림 (미리 보기)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

사전 알림 (미리 보기)은 [유지 관리 기간 (미리 보기)](maintenance-window.md)용으로 구성 된 데이터베이스에서 사용할 수 있습니다. 사전 알림을 통해 고객은 예정 된 이벤트를 사전에 최대 24 시간 동안 전송할 알림을 구성할 수 있습니다.

예정 된 유지 관리가 다음 24 시간 이내에 시작 될 때 텍스트, 전자 메일, Azure 푸시 알림 및 voicemails를 가져올 수 있도록 알림을 구성할 수 있습니다. 추가 알림은 유지 관리가 시작 될 때와 유지 관리가 종료 될 때 전송 됩니다.

> [!Note]
> Azure SQL 관리 되는 인스턴스에는 유지 관리 기간을 선택 하는 기능을 사용할 수 있지만 현재 Azure SQL 관리 되는 인스턴스에는 사전 알림을 사용할 수 없습니다.

## <a name="create-an-advance-notification"></a>사전 알림 만들기

유지 관리 기간이 구성 된 Azure SQL database에 대 한 고급 알림을 사용할 수 있습니다. 

알림을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.  

1. [계획 된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) 페이지로 이동 하 여 **상태 경고**, **서비스 상태 경고 추가** 를 차례로 선택 합니다.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="새 상태 경고 만들기 메뉴 옵션":::

2. **작업** 섹션에서 **작업 그룹 추가** 를 선택 합니다. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="작업 그룹 메뉴 옵션 추가":::

3. **작업 그룹 만들기** 양식을 완료 하 고 **다음: 알림** 을 선택 합니다.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="작업 그룹 폼 만들기":::

1. **알림 탭에서** **알림 유형을** 선택 합니다. **이메일/SMS 메시지/푸시/음성** 옵션은 가장 뛰어난 유연성을 제공 하며 권장 되는 옵션입니다. 알림을 구성 하려면 펜을 선택 합니다.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="알림 구성":::



   1. 열리는 *알림 추가 또는 편집* 양식을 완료 하 고 **확인을** 선택 합니다. 

   2. 작업 및 태그는 선택 사항입니다. 여기에서 추가 작업을 트리거하거나 태그를 사용 하 여 Azure 리소스를 범주화 하 고 구성할 수 있습니다. 

   4. **검토 + 만들기** 탭의 세부 정보를 확인 하 고 **만들기** 를 선택 합니다. 

7. 만들기를 선택 하면 경고 규칙 구성 화면이 열리고 작업 그룹이 선택 됩니다. 새 경고 규칙에 이름을 지정 하 고, 해당 규칙에 대 한 리소스 그룹을 선택 하 고, **경고 규칙 만들기** 를 선택 합니다. 

8. **상태 경고** 메뉴 항목을 다시 클릭 하면 경고 목록에 새 경고가 포함 됩니다. 


모든 설정을 완료했습니다. 다음에 계획 된 Azure SQL 유지 관리 이벤트가 있으면 미리 알림이 표시 됩니다.

## <a name="receiving-notifications"></a>알림 수신

다음 표에서는 받을 수 있는 일반 정보 알림을 보여 줍니다. 

|상태|설명|
|:---|:---|
|**계획 된 배포**| 유지 관리 이벤트 전에 24 시간 동안 수신 됩니다. 유지 관리는 DB xyz에 대해 오후 5 시-오전 8 시 (현지 시간) 사이의 날짜에 계획 됩니다.|
|**진행 중** |  *Xyz* 데이터베이스에 대 한 유지 관리   를 시작 하는 중입니다.| 
|**완료** | *Xyz* 데이터베이스의 유지 관리가 완료 되었습니다. |

다음 표에서는 유지 관리가 진행 되는 동안 전송 될 수 있는 추가 알림을 보여 줍니다. 

|상태|설명|
|:---|:---|
|**확장** | 유지 관리가 진행 중이지만 *xyz* 데이터베이스에 대해 완료 되지 않았습니다. 유지 관리는 다음 유지 관리 기간에 계속 됩니다.| 
|**Canceled**| *Xyz* 데이터베이스에 대 한 유지 관리가 취소 되 고 나중에 다시 예약 됩니다. |
|**차단**|*Xyz* 데이터베이스를 유지 관리 하는 동안 문제가 발생 했습니다. 다시 시작할 때 알려 드리겠습니다.| 
|**다시 시작됨**|문제가 해결 되 고 다음 유지 관리 기간에 유지 관리를 계속 합니다.|


## <a name="next-steps"></a>다음 단계

- [유지 관리 기간](maintenance-window.md)
- [유지 관리 기간 FAQ](maintenance-window-faq.yml)
- [Microsoft Azure의 경고 개요](../../azure-monitor/platform/alerts-overview.md)
- [Azure Resource Manager 역할에 메일 보내기](../../azure-monitor/platform/action-groups.md#email-azure-resource-manager-role)