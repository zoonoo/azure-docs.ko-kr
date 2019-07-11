---
title: Azure Portal을 사용하여 서비스 상태 알림 보기
description: 서비스 상태 알림을 통해 Microsoft Azure에서 게시한 서비스 상태 메시지를 볼 수 있습니다.
author: stephbaron
ms.author: stbaron
services: monitoring
ms.service: service-health
ms.topic: conceptual
ms.date: 6/27/2019
ms.subservice: ''
ms.openlocfilehash: d2e18ae28e79590cb04ee0045341ea817be4a3bc
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538355"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 상태 알림 보기

에 Azure 인프라에서 서비스 상태 알림 게시 되는 [Azure 활동 로그](../azure-monitor/platform/activity-logs-overview.md)합니다.  알림을 구독 아래에 있는 리소스에 대 한 정보를 포함 합니다. 활동 로그에 저장 하는 정보의 양이 클 들어 경우 쉽게 보기 및 서비스 상태 알림에 대 한 경고를 설정 하기 위한 별도 사용자 인터페이스 

서비스 상태 알림은 클래스에 따라 정보만 제공하거나 실행할 수 있습니다.

다양 한 서비스 상태 알림은 클래스에 대 한 자세한 내용은 참조 하세요. [서비스 상태 알림 속성](service-health-notifications-properties.md)합니다.

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal에서 서비스 상태 알림 보기

1. [Azure Portal](https://portal.azure.com)에서 **모니터**를 선택합니다.

    ![모니터를 선택한 Azure Portal 메뉴 스크린샷](./media/service-notifications/home-monitor.png)

    Azure Monitor는 모든 모니터링 설정과 데이터를 하나의 통합 보기로 모읍니다. 처음에는 **활동 로그** 섹션이 열립니다.

1. **경고**를 선택합니다.

    ![경고를 선택한 활동 로그 모니터 스크린샷](./media/service-notifications/service-health-summary.png)

1. **+활동 로그 경고 추가**를 선택하고 알림을 구성하여 향후 서비스 알림을 받을 수 있습니다. 자세한 내용은 [서비스 알림에 대한 활동 로그 경고 만들기](../azure-monitor/platform/alerts-activity-log-service-notifications.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [활동 로그 경고](../azure-monitor/platform/activity-log-alerts.md)에 대해 자세히 알아봅니다.
