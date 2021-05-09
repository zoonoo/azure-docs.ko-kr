---
title: 예약된 유지 관리 - Azure Database for PostgreSQL - 하이퍼스케일(Citus)
description: 이 문서에서는 Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 예약된 유지 관리 기능을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 336b8ff034122373c9bd824d76c110c808e73010
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108315582"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 예약된 유지 관리

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 관리형 데이터베이스를 안전하고 안정적으로, 또 최신 상태로 유지하기 위해 정기적으로 유지 관리를 수행합니다.  유지 관리 중에 서버 그룹의 모든 노드는 새로운 기능, 업데이트 및 패치를 가져옵니다.

하이퍼스케일(Citus)에 대해 예약된 유지 관리의 핵심 기능은 다음과 같습니다.

* 서버 그룹의 모든 노드에 업데이트가 동시 적용됩니다.
* 예정된 유지 관리에 대한 알림은 Azure Service Health에 5일 전 미리 게시됩니다.
* 일반적으로 서버 그룹에 대해 성공적으로 예약된 유지 관리 이벤트 사이의 간격은 30일 이상입니다.
* 원하는 유지 관리 요일 및 해당 요일의 유지 관리 시작 시간대를 서버별로 정의할 수 있습니다.

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>유지 관리 기간 및 예정된 유지 관리 관련 알림 선택

특정 요일 및 해당일의 시간대에서 유지 관리를 예약할 수 있습니다. 또는 시스템이 자동으로 요일과 시간을 선택하도록 할 수도 있습니다. 어느 쪽이든 시스템은 유지 관리 실행 5일 전에 알림을 보냅니다. 또한 시스템은 유지 관리가 시작되고 완료된 시점을 알립니다.

추후 진행할 예약된 유지 관리에 대한 알림은 Azure Service Health에 게시되며, 다음과 같이 전송될 수 있습니다.

* 특정 주소로 이메일 전송
* Azure Resource Manager 역할로 이메일 전송
* 모바일 디바이스로 SMS(문자 메시지) 전송
* Azure 앱에 알림으로 푸시
* 음성 메시지로 전달

유지 관리 일정에 대한 기본 설정을 지정하는 경우 요일 및 기간을 선택할 수 있습니다. 지정하지 않으면 시스템이 서버 그룹의 지역 시간에서 오후 11시에서 오전 7시 사이의 시간을 선택합니다. Azure 구독의 각 하이퍼스케일(Citus) 서버 그룹에 대해 서로 다른 일정을 정의할 수 있습니다.

> [!IMPORTANT]
> 일반적으로 서버 그룹에 대해 성공적으로 예약된 유지 관리 이벤트 사이의 간격은 30일 이상입니다.
>
> 그러나 심각한 취약성과 같은 중요한 응급 업데이트의 경우 알림 기간은 5일이 안 될 수 있습니다. 최근 30일 동안 예약된 유지 관리가 성공적으로 수행된 경우에도 중요 업데이트가 서버에 적용될 수 있습니다.

예약 설정은 언제든지 업데이트할 수 있습니다. 하이퍼스케일(Citus) 서버 그룹에 대해 유지 관리가 예약되어 있고 일정을 업데이트하는 경우, 기존 이벤트는 원래 예약된 대로 계속됩니다. 설정 변경 내용은 기존 이벤트가 성공적으로 완료된 후에 적용됩니다.

유지 관리가 실패하거나 취소되면 시스템에서 알림을 만듭니다.
시스템은 현재 예약 설정에 따라 유지 관리를 다시 시도하고 다음 유지 관리 이벤트 5일 전에 알림을 보냅니다.

## <a name="next-steps"></a>다음 단계

* [유지 관리 일정 변경](howto-hyperscale-maintenance.md) 방법 알아보기
* Azure Service Health를 사용하여 [예정된 유지 관리에 대한 알림을 받는](../service-health/service-notifications.md) 방법에 대해 알아봅니다.
* [추후 진행할 예약된 유지 관리 이벤트에 대한 알림을 설정](../service-health/resource-health-alert-monitor-guide.md)하는 방법을 알아봅니다.
