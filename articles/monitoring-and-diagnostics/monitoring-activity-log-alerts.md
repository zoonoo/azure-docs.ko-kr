---
title: "활동 로그 경고 만들기 | Microsoft Docs"
description: "활동 로그에서 특정 이벤트가 발생하면 SMS, 웹후크 및 전자 메일을 통해 알림을 받습니다."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 709e20bc6f0d32509f2cdd4c5a9e69021238dba0
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="create-activity-log-alerts"></a>활동 로그 경고 만들기

## <a name="overview"></a>개요
활동 로그 경고는 새 활동 로그 이벤트가 경고에 지정된 조건과 일치하는 경우에 활성화되는 경고입니다. 이러한 경고는 Azure 리소스이므로 Azure Portal 또는 ARM(Azure Resource Manager)을 사용하여 관리할 수 있습니다. 이 문서에서는 Azure Portal을 사용하여 활동 로그 이벤트에 대한 경고를 설정하는 방법에 대해 설명합니다.

구독의 리소스에서 수행된 작업 또는 리소스 상태에 영향을 줄 수 있는 서비스 상태 이벤트에 대한 경고를 받을 수 있습니다. 활동 로그 경고는 경고를 배포한 특정 구독에 대한 활동 로그 이벤트를 모니터링합니다.

다음 항목에 따라 경고를 구성할 수 있습니다.
* 이벤트 범주([서비스 상태 이벤트의 경우 여기를 클릭함](monitoring-activity-log-alerts-on-service-notifications.md))
- 리소스 그룹
- 리소스
- 리소스 종류
- 작업 이름
- 알림 수준(자세한 정보 표시, 정보, 경고, 오류, 위험)
- 가동 상태
- 이벤트를 시작한 사람

>[!NOTE]
>경고에 위 조건 중 하나 이상을 지정해야 합니다. 활동 로그에서 이벤트를 만들 때마다 활성화되는 경고를 만들 필요는 없습니다.
>
>

경고가 활성화될 때 실행되는 작업을 구성할 수도 있습니다.
* 기존 작업 그룹 선택
- 나중에 미래의 경고를 위해 사용할 수 있는 새 작업 그룹 만들기

작업 그룹에 대한 자세한 내용은 [여기서](monitoring-action-groups.md) 확인할 수 있습니다.

서비스 상태 알림에 대한 활동 로그 경고는 [여기](monitoring-activity-log-alerts-on-service-notifications.md)에서 알아보세요.

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Azure Portal을 사용하여 새 작업 그룹의 활동 로그 이벤트에 대한 경고 만들기
1.  [포털](https://portal.azure.com)에서 **모니터** 서비스로 이동합니다.

    ![모니터](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  **모니터** 옵션을 클릭하여 모니터 블레이드를 엽니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.  이제 **경고** 섹션을 클릭합니다.

    ![경고](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  **활동 로그 경고 추가** 명령을 선택하고 필드를 채웁니다.

5.  **이름**에서 활동 로그 경고의 이름을 지정하고 **설명**을 선택합니다.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  **구독**은 활동 로그 경고가 저장되는 위치입니다. 현재 작동 중인 구독으로 자동으로 채워집니다. 이 구독은 경고 리소스를 배포하고 모니터링하는 구독입니다.

    ![새 작업 그룹에 경고 추가](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  **리소스 그룹**을 선택합니다. 그러면 이 경고가 **구독**에 연결됩니다.

8.  **이벤트 범주**, **리소스 그룹**, **리소스**, **리소스 종류**, **작업 이름**, **수준**, **상태** 및 **이벤트를 시작한 사람** 값을 제공하여 이 경고에서 모니터링해야 하는 이벤트를 식별합니다.

>[!NOTE]
>경고에 위 조건 중 하나 이상을 지정해야 합니다. 활동 로그에서 이벤트를 만들 때마다 활성화되는 경고를 만들 필요는 없습니다.
>
>

9.  **이름** 및 **약식 이름**을 지정하여 **새** 작업 그룹을 만듭니다. 약식 이름은 이 경고를 활성화할 때 보내는 알림에서 참조합니다.

10. 그런 다음 작업에 대해 다음 정보를 제공하여 작업 목록을 정의합니다.

    a. **이름:** 작업의 이름, 별칭 또는 식별자입니다.

    b. **작업 유형:** SMS, 전자 메일 또는 웹후크 중에서 작업 유형을 선택합니다.

    c. **세부 정보:** 선택한 작업 유형에 따라 전화 번호, 전자 메일 주소 또는 웹후크 URI를 제공합니다.

11. 경고 만들기가 완료되면 **확인** 을 선택합니다.

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

활동 로그 경고에 대한 웹후크 스키마에 대한 자세한 내용은 [여기를 클릭하세요](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>이 단계에서 정의한 작업 그룹은 향후의 모든 경고 정의에 대해 기존 작업 그룹으로 다시 사용할 수 있습니다.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Azure Portal을 사용하여 기존 작업 그룹의 활동 로그 이벤트에 대한 경고 만들기
1.  [포털](https://portal.azure.com)에서 **모니터** 서비스로 이동합니다.

    ![모니터](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  **모니터** 옵션을 클릭하여 모니터 블레이드를 엽니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.  이제 **경고** 섹션을 클릭합니다.

    ![경고](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  **활동 로그 경고 추가** 명령을 선택하고 필드를 채웁니다.

5.  **이름**에서 활동 로그 경고의 이름을 지정하고 **설명**을 선택합니다. 경고가 발생할 때 보내는 알림에 이러한 항목이 표시됩니다.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  **구독**은 활동 로그 경고가 저장되는 위치입니다. 현재 작동 중인 구독으로 자동으로 채워집니다. 이 구독은 경고 리소스를 배포하고 모니터링하는 구독입니다.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  **리소스 그룹**을 선택합니다. 그러면 이 경고가 **구독**에 연결됩니다.

8.  **이벤트 범주**, **리소스 그룹**, **리소스**, **리소스 종류**, **작업 이름**, **수준**, **상태** 및 **이벤트를 시작한 사람** 값을 제공하여 이 경고를 적용해야 하는 이벤트의 범위를 지정합니다.

9.  **다음을 통해 알림**에서 **기존 작업 그룹**을 선택합니다. 각 작업 그룹을 선택합니다.

10. 경고 만들기가 완료되면 **확인** 을 선택합니다.

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="managing-your-alerts"></a>경고 관리

경고를 만들었으면 모니터 서비스의 경고 섹션에 해당 경고가 표시됩니다. 관리하려는 경고를 선택하면 다음 작업을 수행할 수 있습니다.
* **편집**
* **삭제**
* 해당 경고에 대한 알림 수신을 일시 중지 또는 다시 시작하려면 **사용 안 함** 또는 **사용**을 설정합니다.

## <a name="next-steps"></a>다음 단계
- [경고 개요](monitoring-overview-alerts.md)에 대해 자세히 알아봅니다.
- [알림 속도 제한](monitoring-alerts-rate-limiting.md)에 대해 자세히 알아보기
- [활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md) 검토
- [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기  
- [서비스 상태 알림](monitoring-service-notifications.md)에 대해 자세히 알아보기
- [구독의 모든 자동 크기 조정 엔진 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [구독에서 실패한 모든 자동 크기 조정 규모 감축/규모 확장 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

