---
title: Azure Portal을 사용하여 서비스 상태 알림 보기
description: 서비스 상태 알림을 통해 Microsoft Azure에서 게시한 서비스 상태 메시지를 볼 수 있습니다.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: c8017d23e0f7e3ab7bf48c9d7ba6b930fa80d0da
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451412"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 상태 알림 보기

서비스 상태 알림은 azure 인프라에 의해 [azure 활동 로그](../azure-monitor/platform/activity-logs-overview.md)에 게시 됩니다.  알림에는 구독에서 리소스에 대 한 정보가 포함 됩니다. 활동 로그에 저장 된 대용량 정보를 제공 하는 경우, 서비스 상태 알림에 대 한 경고를 보다 쉽게 확인 하 고 설정할 수 있는 별도의 사용자 인터페이스가 있습니다. 

서비스 상태 알림은 클래스에 따라 정보만 제공하거나 실행할 수 있습니다.

다양 한 서비스 상태 알림 클래스에 대 한 자세한 내용은 [서비스 상태 알림 속성](service-health-notifications-properties.md)을 참조 하세요.

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal에서 서비스 상태 알림 보기

1. [Azure Portal](https://portal.azure.com)에서 **모니터**를 선택합니다.

    ![모니터를 선택한 Azure Portal 메뉴 스크린샷](./media/service-notifications/home-monitor.png)

    Azure Monitor는 모든 모니터링 설정과 데이터를 하나의 통합 보기로 모읍니다. 처음에는 **활동 로그** 섹션이 열립니다.

1. **경고**를 선택합니다.

    ![경고를 선택한 활동 로그 모니터 스크린샷](./media/service-notifications/service-health-summary.png)

1. **+활동 로그 경고 추가**를 선택하고 알림을 구성하여 향후 서비스 알림을 받을 수 있습니다. 자세한 내용은 [서비스 알림에 대한 활동 로그 경고 만들기](../azure-monitor/platform/alerts-activity-log-service-notifications.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [활동 로그 경고](../azure-monitor/platform/activity-log-alerts.md)에 대해 자세히 알아봅니다.
