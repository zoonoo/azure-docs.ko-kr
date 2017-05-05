---
title: "서비스 알림에 대한 활동 로그 경고 수신 | Microsoft Docs"
description: "Azure 서비스가 발생할 때 SMS, 전자 메일 또는 웹후크를 통해 알림을 받습니다."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 57e136c1027cfa7c789803409cef354f3d52d222
ms.lasthandoff: 03/31/2017


---
# <a name="create-activity-log-alerts-on-service-notifications"></a>서비스 알림에 대한 활동 로그 경고 만들기
## <a name="overview"></a>개요
이 문서에서는 Azure Portal을 사용하여 서비스 상태 알림에 대한 활동 로그 경고를 설정하는 방법을 보여 줍니다.  

Azure 구독에 대한 서비스 상태 알림을 기준으로 경고를 받을 수 있습니다.  
다음 항목에 따라 경고를 구성할 수 있습니다.
- 서비스 상태 알림 클래스(인시던트, 유지 관리, 정보 등)
- 알림의 상태(활성 및 해결)
- 알림 수준(정보, 경고, 오류)

다음과 같이 경고를 받는 사람도 구성할 수 있습니다.
- 기존 작업 그룹 선택
- 나중에 미래의 경고를 위해 사용할 수 있는 새 작업 그룹 만들기

작업 그룹에 대한 자세한 내용은 [여기서](monitoring-action-groups.md) 확인할 수 있습니다.

다음을 사용하여 서비스 상태 알림 경고에 대한 정보를 구성하고 가져올 수 있습니다.
- [Azure 포털](monitoring-activity-log-alerts-on-service-notifications.md)
- [Resource Manager 템플릿](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-with-the-azure-portal"></a>Azure Portal에서 새 작업 그룹에 대한 서비스 상태 알림의 경고 만들기
1.    [포털](https://portal.azure.com)에서 **모니터** 서비스로 이동합니다.

    ![모니터](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)

2.    **모니터** 옵션을 클릭하여 모니터 블레이드를 엽니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.    이제 **경고** 섹션을 클릭합니다.

    ![경고](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)

4.    **활동 로그 경고 추가** 명령을 선택하고 필드를 채웁니다.

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)

5.    **이름**에서 활동 로그 경고의 이름을 지정하고 **설명**을 선택합니다. 경고가 발생할 때 보내는 알림에 이러한 항목이 표시됩니다.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-new-action-group.png)

6.    **구독**은 현재 작동 중인 구독에 자동으로 채워져야 합니다.

7.    이 경고에 대한 **리소스 그룹**을 선택합니다.

8.    **이벤트 범주** 아래에서 ‘서비스 상태’ 옵션을 선택합니다. 알림을 받으려는 서비스 상태 알림의 **유형, 상태** 및 **수준**을 선택합니다.

9.    **이름** 및 **약식 이름**을 지정하여 **새** 작업 그룹을 만듭니다. 약식 이름은 이 경고가 발생할 때 보내는 알림에서 참조됩니다.

10.    그런 다음 받는 사람에 대한 다음 항목을 제공하여 받는 사람 목록을 정의합니다.

    a. **이름:** 받는 사람의 이름, 별칭 또는 식별자입니다.

    b. **작업 유형:** SMS, 전자 메일 또는 웹후크를 통해 받는 사람에게 연락하도록 선택합니다.

    c. **세부 정보:** 선택한 작업 유형에 따라 전화 번호, 전자 메일 주소 또는 웹후크 URI를 제공합니다.

11.    경고 만들기가 완료되면 **확인** 을 선택합니다.

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

활동 로그 경고에 대한 웹후크 스키마에 대한 자세한 내용은 [여기를 클릭하세요](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>이 단계에서 정의한 작업 그룹은 향후의 모든 경고 정의에 대해 기존 작업 그룹으로 다시 사용할 수 있습니다.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-with-the-azure-portal"></a>Azure Portal에서 기존 작업 그룹에 대한 서비스 상태 알림의 경고 만들기
1.    [포털](https://portal.azure.com)에서 **모니터** 서비스로 이동합니다.

    ![모니터](./media/monitoring-activity-log-alerts-on-service-notifications/home-monitor.png)
2.    **모니터** 옵션을 클릭하여 모니터 블레이드를 엽니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.    이제 **경고** 섹션을 클릭합니다.

    ![경고](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades.png)
4.    **활동 로그 경고 추가** 명령을 선택하고 필드를 채웁니다.

    ![Add-Alert](./media/monitoring-activity-log-alerts-on-service-notifications/add-activity-log-alert.png)
5.    **이름**에서 활동 로그 경고의 이름을 지정하고 **설명**을 선택합니다. 경고가 발생할 때 보내는 알림에 이러한 항목이 표시됩니다.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-service-notification-existing-action-group.png)
6.    **구독**은 현재 작동 중인 구독에 자동으로 채워져야 합니다.

7.    이 경고에 대한 **리소스 그룹**을 선택합니다.

8.    **이벤트 범주** 아래에서 ‘서비스 상태’ 옵션을 선택합니다. 알림을 받으려는 서비스 상태 알림의 **유형, 상태** 및 **수준**을 선택합니다.

9.    **다음을 통해 알림**에서 **기존 작업 그룹**을 선택합니다. 각 작업 그룹을 선택합니다.

10.    경고 만들기가 완료되면 **확인** 을 선택합니다.

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="managing-your-alerts"></a>경고 관리

경고를 만들었으면 모니터 서비스의 경고 섹션에 해당 경고가 표시됩니다. 관리하려는 경고를 선택하면 다음 작업을 수행할 수 있습니다.
* **편집**
* **삭제**
* 해당 경고에 대한 알림 수신을 일시 중지 또는 다시 시작하려면 **사용 안 함** 또는 **사용**을 설정합니다.

## <a name="next-steps"></a>다음 단계:
[서비스 상태 알림](monitoring-service-notifications.md)에 대해 자세히 알아보기  
[활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md) 검토, [활동 로그 경고의 개요](monitoring-overview-alerts.md) 확인, 알림을 가져오는 방법에 알아보기  
[작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기

