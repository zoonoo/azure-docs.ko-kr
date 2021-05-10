---
title: Azure Database for PostgreSQL - 하이퍼스케일(Citus) - 예약된 유지 관리 - Azure Portal
description: Azure Portal에서 Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 예약된 유지 관리 설정을 구성하는 방법을 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108119"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 예약된 유지 관리 설정 관리

Azure 구독에서 각 하이퍼스케일(Citus) 서버 그룹에 대한 유지 관리 옵션을 지정할 수 있습니다. 옵션에는 예정된 유지 관리 이벤트와 완료된 유지 관리 이벤트에 대한 유지 관리 일정 및 알림 설정이 포함됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus) 서버 그룹](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>유지 관리 일정 옵션 지정

1. 하이퍼스케일(Citus) 서버 그룹 페이지의 **설정** 제목 아래에서 **유지 관리** 를 선택하여 예약된 유지 관리 옵션을 엽니다.
2. 기본(시스템 관리) 일정은 유지 관리 시작을 위한 임의의 요일과 30분의 기간(오후 11시에서 오전 7시 사이(서버 그룹의 [Azure 지역 시간](https://go.microsoft.com/fwlink/?linkid=2143646) 기준))입니다. 이 일정을 사용자 지정하려면 **사용자 지정 일정** 을 선택합니다. 그런 다음, 기본 요일 및 30분의 유지 관리 기간의 시작 시간을 선택할 수 있습니다.

## <a name="notifications-about-scheduled-maintenance-events"></a>예약된 유지 관리 이벤트에 대한 알림

Azure Service Health를 사용하여 하이퍼스케일(Citus) 서버 그룹에서 앞으로의 예약된 유지 관리와 과거의 예약된 유지 관리에 대한 [알림을 볼](../service-health/service-notifications.md) 수 있습니다. Azure Service Health에서 경고를 [설정](../service-health/resource-health-alert-monitor-guide.md)하여 유지 관리 이벤트에 대한 알림을 받을 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 예약된 유지 관리](concepts-hyperscale-maintenance.md)에 대해 알아보기
* [Azure Service Health](../service-health/overview.md)에 대해 알아보기
