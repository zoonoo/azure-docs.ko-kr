---
title: Azure Database for MySQL - 유연한 서버(미리 보기) - 예약된 유지 관리 - Azure Portal
description: Azure Portal에서 Azure Database for MySQL 유연한 서버에 대한 예약된 유지 관리 설정을 구성하는 방법에 대해 알아봅니다.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a4ff27243643a5c0fbb833a05a07e9e117196a6f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642131"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Azure Database for MySQL에 대한 예약된 유지 관리 설정 관리 – 유연한 서버

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure 구독에서 각 유연한 서버에 대한 유지 관리 옵션을 지정할 수 있습니다. 옵션에는 예정된 유지 관리 이벤트와 완료된 유지 관리 이벤트에 대한 유지 관리 일정 및 알림 설정이 포함됩니다.

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 미리 보기로 제공됩니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 완료하려면 다음이 필요합니다.

- [Azure Database for MySQL - 유연한 서버](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>유지 관리 일정 옵션 지정
 
1. MySQL server 페이지의 **설정** 제목에서 **유지 관리** 를 선택하여 예약된 유지 관리 옵션을 엽니다.
2. 기본(시스템 관리) 일정은 임의 요일과 60분 유지 관리 기간 시작 시간(현지 서버 시간으로 오후 11시에서 오전 7시 사이)입니다. 이 일정을 사용자 지정하려면 **사용자 지정 일정** 을 선택합니다. 그런 다음, 원하는 요일과 60분 유지 관리 기간 시작 시간을 선택할 수 있습니다.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>예약된 유지 관리 이벤트에 대한 알림
 
Azure Service Health를 사용하여 유연한 서버에서 임박한 예약된 유지 관리와 수행한 예약된 유지 관리에 대한 [알림을 확인](../../service-health/service-notifications.md)할 수 있습니다. Azure Service Health에서 경고를 [설정](../../service-health/resource-health-alert-monitor-guide.md)하여 유지 관리 이벤트에 대한 알림을 받을 수도 있습니다.
 
## <a name="next-steps"></a>다음 단계  
 
* [Azure Database for MySQL에서 예약된 유지 관리 - 유연한 서버](concepts-maintenance.md)에 대해 자세히 알아보기
* [Azure Service Health](../../service-health/overview.md)에 대해 알아보기
