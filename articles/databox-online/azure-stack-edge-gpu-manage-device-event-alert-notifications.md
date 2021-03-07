---
title: Azure Stack Edge Pro 리소스에 대 한 장치 이벤트 경고 알림 관리 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure Stack Edge Pro 리소스에서 장치 이벤트에 대 한 경고를 관리 하는 방법을 설명 합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443100"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Azure Stack Edge Pro 리소스에서 장치 이벤트 경고 알림 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 리소스 그룹, Azure 구독 또는 개별 Azure Stack에 지 리소스 내에서 발생 하는 장치 이벤트에 대 한 경고 알림을 트리거하거나 표시 하지 않도록 Azure Portal에서 작업 규칙을 만드는 방법을 설명 합니다. 이 문서는 Azure Stack Edge의 모든 모델에 적용 됩니다.  

## <a name="about-action-rules"></a>작업 규칙 정보

작업 규칙은 경고 알림을 트리거하거나 표시 하지 않을 수 있습니다. 작업 규칙은 *작업 그룹* 에 추가 됩니다. 알림 기본 설정 집합은 리소스 또는 리소스 집합에 대해 서로 다른 컨텍스트에서 트리거된 경고에 대해 작업 해야 하는 사용자에 게 알리는 데 사용 됩니다.

작업 규칙에 대 한 자세한 내용은 [작업 규칙 구성](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)을 참조 하세요. 작업 그룹에 대 한 자세한 내용은 [Azure Portal에서 작업 그룹 만들기 및 관리](../azure-monitor/alerts/action-groups.md)를 참조 하세요.

> [!NOTE]
> 작업 규칙 기능은 미리 보기 상태입니다. 프로세스를 구체화할 때 일부 화면과 단계가 변경 될 수 있습니다.


## <a name="create-an-action-rule"></a>작업 규칙 만들기

Azure Portal에서 다음 단계를 수행 하 여 Azure Stack에 지 장치에 대 한 작업 규칙을 만듭니다.

> [!NOTE]
> 이러한 단계는 작업 그룹에 알림을 보내는 작업 규칙을 만듭니다. 알림을 표시 하지 않는 작업 규칙을 만드는 방법에 대 한 자세한 내용은 [작업 규칙 구성](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)을 참조 하세요.

1. Azure Portal에서 Azure Stack Edge 장치로 이동한 후 **모니터링 > 경고** 로 이동 합니다. **작업 관리** 를 선택 합니다.

   ![경고 모니터링, 작업 보기 관리](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. **작업 규칙 (미리 보기)** 을 선택한 다음 **+ 새 작업 규칙** 을 선택 합니다.

   ![작업 관리, 동작 규칙 옵션](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. **작업 규칙 만들기** 화면에서 **범위** 를 사용 하 여 Azure 구독, 리소스 그룹 또는 대상 리소스를 선택 합니다. 작업 규칙은 해당 범위 내에서 생성 되는 모든 경고에 대해 작동 합니다.

   1. 범위 **선택** 을 선택 합니다.

      ![새 작업 규칙의 범위를 선택 하십시오.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. 작업 규칙에 대 한 **구독** 을 선택 하 고 선택적으로 **리소스** 유형별로 필터링 합니다. 에 지 리소스를 Azure Stack 필터링 하려면 **Data Box Edge 장치 (dataBoxEdge)** 를 선택 합니다.

      **리소스** 영역에는 선택한 항목에 따라 사용 가능한 리소스가 나열 됩니다.
  
      ![범위 선택 화면에서 사용 가능한 리소스](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. 규칙을 적용 하려는 각 리소스의 확인란을 선택 합니다. 구독, 리소스 그룹 또는 개별 리소스를 선택할 수 있습니다. 그런 후 **완료** 를 선택합니다.

      ![작업 규칙 범위에 대 한 샘플 설정](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      **작업 규칙 만들기** 화면에 선택한 범위가 표시 됩니다.

      ![Azure Stack Edge 작업 규칙에 대 한 완료 된 범위](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. **필터** 옵션을 사용 하 여 선택한 범위 내에 있는 경고의 하위 집합에 대 한 규칙의 응용 프로그램 범위를 좁힐 수 있습니다.

   1. **추가** 를 선택 하 여 **필터 추가** 창을 엽니다.

      ![작업 규칙에 필터를 추가 하는 옵션](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. **필터** 아래에서 적용 하려는 각 필터를 추가 합니다. 각 필터에 대해 필터 유형, **연산자** 및 **값** 을 선택 합니다.
   
      필터 옵션 목록은 [필터 조건](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria)을 참조 하세요.

      아래 샘플 필터는 모니터 서비스가 Azure Stack에 지 리소스에 대해 발생 하는 심각도 수준 2, 3 및 4의 모든 경고에 적용 됩니다.

      필터 추가가 완료 되 면 **완료** 를 선택 합니다.
   
      ![작업 규칙에 대 한 샘플 필터](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. **작업 규칙 만들기** 화면에서 **작업 그룹** 을 선택 하 여 알림을 보내는 규칙을 만듭니다. 그런 다음 **작업** 을 통해 **선택** 을 선택 합니다.

   ![알림을 보내는 작업 규칙을 만들기 위한 작업 그룹 옵션](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > 알림을 표시 하지 않는 규칙을 만들려면 **비** 표시 제거를 선택 합니다. 자세한 내용은 [작업 규칙 구성](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)을 참조 하세요.

6. 이 작업 규칙에 사용 하려는 작업 그룹을 선택 합니다. 그런 다음, **선택** 을 선택합니다. 선택한 작업 그룹의 알림 기본 설정에 새 작업 규칙이 추가 됩니다.

   새 작업 그룹을 만들어야 하는 경우 **+ 작업 그룹 만들기** 를 선택 하 고 [Azure Portal를 사용 하 여 작업 그룹 만들기](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)의 단계를 따릅니다.

   ![규칙에 사용할 작업 그룹을 선택한 다음 선택을 선택 합니다.](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. 새 작업 규칙에 **이름** 및 **설명을** 지정 하 고 리소스 그룹에 규칙을 할당 합니다.

9. 새 규칙은 기본적으로 사용 하도록 설정 되어 있습니다. 규칙 사용을 즉시 시작 하지 않으려면 **규칙 업데이트 만들기 사용** 에 대해 **아니요** 를 선택 합니다.

10. 설정을 마치면 **만들기** 를 선택 합니다.

    ![경고 알림을 보낼 작업 규칙에 대 한 완료 된 설정](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    **작업 규칙 (미리 보기)** 화면이 열리지만 새 작업 규칙이 즉시 표시 되지 않을 수 있습니다. **모든** 리소스 그룹에 포커스가 있습니다.

11. 새 작업 규칙을 보려면 해당 규칙에 대 한 리소스 그룹을 선택 합니다.

    ![새 규칙이 표시 된 작업 규칙 화면](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>알림 확인

새 이벤트가 작업 규칙 범위 내에 있는 리소스에 대 한 경고를 트리거할 때 알림이 발생 합니다.

규칙에 대 한 작업 그룹은 알림을 받는 사람을 설정 하 고 전자 메일, SMS (Short Message Service) 메시지 또는 둘 모두에 대해 전송 된 알림 유형을 설정 합니다.

경고가 트리거된 후 알림을 수신 하는 데 몇 분 정도 걸릴 수 있습니다.

전자 메일 알림은 다음과 유사 하 게 표시 됩니다.

![작업 규칙에 대 한 샘플 메일 알림](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>다음 단계

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- 장치 이벤트, 하드웨어 상태 및 메트릭 차트를 검토 하는 방법에 대 한 정보는 [Azure Stack Edge Pro 모니터링](azure-stack-edge-monitor.md) 을 참조 하세요. 
- Azure Stack Edge Pro GPU 장치에 대 한 Azure Monitor 최적화에 대 한 자세한 내용은 [Azure Monitor 사용](azure-stack-edge-gpu-enable-azure-monitor.md) 을 참조 하세요.
- 개별 경고를 관리 하는 방법에 대 한 정보는 [Azure Monitor 링크 대상을 사용 하 여 메트릭 경고 만들기, 보기 및 관리를](../azure-monitor/alerts/alerts-metric.md) 참조 하세요.