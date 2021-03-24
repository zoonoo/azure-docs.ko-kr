---
title: 예약 된 유지 관리-Azure Database for PostgreSQL-Hyperscale (Citus)
description: 이 문서에서는 Citus (Azure Database for PostgreSQL-Hyperscale)의 예약 된 유지 관리 기능을 설명 합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027602"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)의 예약 된 유지 관리

Citus (Azure Database for PostgreSQL-Hyperscale)는 관리 되는 데이터베이스를 안전 하 고 안정적 이며 최신 상태로 유지 하기 위해 정기적으로 유지 관리를 수행 합니다.  유지 관리 중에 서버 그룹의 모든 노드는 새로운 기능, 업데이트 및 패치를 가져옵니다.

Citus (Hyperscale)에 대해 예약 된 유지 관리의 핵심 기능은 다음과 같습니다.

* 서버 그룹의 모든 노드에 업데이트가 동시에 적용 됩니다.
* 예정 된 유지 관리에 대 한 알림이 사전에 5 일 Azure Service Health 게시 됩니다.
* 일반적으로 서버 그룹에 대 한 유지 관리 이벤트는 30 일 이상으로 유지 됩니다.

## <a name="notification-about-upcoming-maintenance"></a>예정 된 유지 관리에 대 한 알림

예정 된 예정 된 유지 관리에 대 한 알림은 Azure Service Health에 게시 되며 다음이 될 수 있습니다.

* 특정 주소에 전자 메일로 전송
* Azure Resource Manager 역할에 전자 메일로 전송
* SMS (문자 메시지)에서 모바일 장치로 전송
* Azure 앱에 알림으로 푸시
* 음성 메시지로 전달

> [!IMPORTANT]
> 일반적으로 서버 그룹에 대해 예약 된 유지 관리 이벤트는 30 일 이상으로 유지 됩니다.
>
> 그러나 심각한 취약성과 같은 중요한 응급 업데이트의 경우 알림 기간은 5일이 안 될 수 있습니다. 최근 30일 동안 예약된 유지 관리가 성공적으로 수행된 경우에도 중요 업데이트가 서버에 적용될 수 있습니다.

유지 관리가 실패 하거나 취소 되 면 시스템에서 알림을 만듭니다.
현재 일정 설정에 따라 유지 관리를 다시 시도 하 고 다음 유지 관리 이벤트 전에 5 일 전에 알립니다.

## <a name="next-steps"></a>다음 단계

* Azure Service Health를 사용 하 여 [예정 된 유지 관리에 대 한 알림을 받는](../service-health/service-notifications.md) 방법에 대해 알아봅니다.
* 예정 된 예정 [된 유지 관리 이벤트에 대 한 경고를 설정](../service-health/resource-health-alert-monitor-guide.md) 하는 방법을 알아봅니다.
