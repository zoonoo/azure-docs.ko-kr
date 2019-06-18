---
title: Azure Portal을 사용하여 서비스 상태 알림 보기
description: 서비스 상태 알림을 통해 Microsoft Azure에서 게시한 서비스 상태 메시지를 볼 수 있습니다.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 8cf8c3eb86f55b5595ef9a1af83ea50580bf638b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069346"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 상태 알림 보기

서비스 상태 알림은 Azure 인프라에 의해 게시 됩니다. 구독의 리소스에 대 한 정보가 있습니다. 이러한 알림은 활동의 하위 클래스 로그 이벤트 이며 수도 확인할 수 있습니다 활동 로그에 있습니다. 서비스 상태 알림은 클래스에 따라 정보만 제공하거나 실행할 수 있습니다.

다양 한 서비스 상태 알림은 클래스에 대 한 자세한 내용은 참조 하세요. [서비스 상태 알림 속성](../../service-health/service-health-notifications-properties.md)합니다.

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal에서 서비스 상태 알림 보기

1. [Azure Portal](https://portal.azure.com)에서 **모니터**를 선택합니다.

    ![모니터를 선택한 Azure Portal 메뉴 스크린샷](./media/service-notifications/home-monitor.png)

    Azure Monitor는 모든 모니터링 설정과 데이터를 하나의 통합 보기로 모읍니다. 처음에는 **활동 로그** 섹션이 열립니다.

1. **경고**를 선택합니다.

    ![경고를 선택한 활동 로그 모니터 스크린샷](./media/service-notifications/service-health-summary.png)

1. **+활동 로그 경고 추가**를 선택하고 알림을 구성하여 향후 서비스 알림을 받을 수 있습니다. 자세한 내용은 [서비스 알림에 대한 활동 로그 경고 만들기](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [서비스 상태 알림이 게시될 때마다 경고 알림](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)을 수신합니다.  
* [활동 로그 경고](../../azure-monitor/platform/activity-log-alerts.md)에 대해 자세히 알아봅니다.
