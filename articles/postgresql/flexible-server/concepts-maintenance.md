---
title: 예약 된 유지 관리-Azure Database for PostgreSQL-유연한 서버
description: 이 문서에서는 Azure Database for PostgreSQL 유연한 서버에서 예약 된 유지 관리 기능을 설명 합니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336312"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Azure Database for PostgreSQL-유연한 서버에서 예약 된 유지 관리
 
Azure Database for PostgreSQL 유연한 서버는 정기적으로 유지 관리를 수행 하 여 관리 되는 데이터베이스를 안전 하 고 안정적으로 최신 상태로 유지 합니다. 유지 관리 중에 서버는 새로운 기능, 업데이트 및 패치를 가져옵니다.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL-유연한 서버는 미리 보기 상태입니다.
 
## <a name="selecting-a-maintenance-window"></a>유지 관리 기간 선택
 
특정 요일 및 해당 날짜 내의 시간 창에서 유지 관리를 예약할 수 있습니다. 또는 시스템에서 자동으로 요일 및 시간 기간을 선택 하도록 할 수 있습니다. 어느 쪽이 든 시스템은 유지 관리를 실행 하기 5 일 전에 경고를 표시 합니다. 시스템은 유지 관리가 시작 된 시기와 성공적으로 완료 된 시기를 알려 드리겠습니다.
 
예정 된 예정 된 유지 관리에 대 한 알림은 다음과 같을 수 있습니다.
 
* 특정 주소에 전자 메일로 전송
* Azure Resource Manager 역할에 전자 메일로 전송
* SMS (문자 메시지)에서 모바일 장치로 전송
* Azure 앱에 대 한 알림으로 푸시 됨
* 음성 메시지로 배달 됨
 
유지 관리 일정에 대한 기본 설정을 지정하는 경우 요일 및 기간을 선택할 수 있습니다. 지정하지 않으면 시스템이 서버의 지역 시간에서 오후 11시에서 오전 7시 사이의 시간을 선택합니다. Azure 구독의 각 유연한 서버에 대해 서로 다른 일정을 정의할 수 있습니다. 
 
> [!IMPORTANT]
> 일반적으로 서버에 대해 성공적으로 예약된 유지 관리 이벤트 사이의 간격은 30일 이상입니다.
>
> 그러나 심각한 취약성과 같은 중요한 응급 업데이트의 경우 알림 기간은 5일이 안 될 수 있습니다. 최근 30일 동안 예약된 유지 관리가 성공적으로 수행된 경우에도 중요 업데이트가 서버에 적용될 수 있습니다.

일정 설정은 언제 든 지 업데이트할 수 있습니다. 유연한 서버에 대 한 유지 관리가 예약 되어 있고 일정 기본 설정을 업데이트 하는 경우 현재 이벤트는 예약 된 대로 진행 되 고 일정 설정 변경 내용은 성공적으로 완료 될 때 적용 됩니다. 

시스템에 의해 유지 관리 이벤트가 취소 되거나 성공적으로 완료 되지 않으면 시스템은 각각 취소 또는 실패 한 유지 관리 이벤트에 대 한 알림을 각각 만듭니다. 다음으로 유지 관리를 수행 하려는 시도는 현재 일정 설정에 따라 예약 되 고 5 일 전에 알림을 받게 됩니다.
 
## <a name="next-steps"></a>다음 단계
 
* [유지 관리 일정을 변경](how-to-maintenance-portal.md) 하는 방법 알아보기
* Azure Service Health를 사용 하 여 [예정 된 유지 관리에 대 한 알림을 받는](../../service-health/service-notifications.md) 방법에 대해 알아봅니다.
* 예정 된 예정 [된 유지 관리 이벤트에 대 한 경고를 설정](../../service-health/resource-health-alert-monitor-guide.md) 하는 방법을 알아봅니다.
