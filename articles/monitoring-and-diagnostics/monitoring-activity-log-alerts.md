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
ms.date: 08/03/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c44ecc0ee61c58ab7590e9a21a5fe002068c6b83
ms.contentlocale: ko-kr
ms.lasthandoff: 08/04/2017

---
# <a name="create-activity-log-alerts"></a>활동 로그 경고 만들기

## <a name="overview"></a>개요
활동 로그 경고는 경고에 지정된 조건과 일치하는 새 활동 로그 이벤트가 발생한 경우에 활성화되는 경고입니다. 이는 Azure 리소스이므로 Resource Manager 템플릿을 사용하여 만들 수 있으며, Azure Portal에서 만들거나 업데이트하거나 또는 삭제할 수 있습니다. 이 문서는 활동 로그 경고에 대한 개념을 소개하고 Azure Portal을 사용하여 활동 로그 이벤트에 대한 경고를 설정하는 방법을 보여줍니다.

활동 로그 경고는 일반적으로 두 가지 목적 중 하나로 만들어집니다.
1. Azure 구독의 리소스에서 특정 변경이 발생할 때 알림을 받기 위해 특정 리소스 그룹 또는 리소스로 범위를 지정하기도 합니다. 예를 들어 myProductionResourceGroup에서 Virtual Machine이 삭제되는 경우 알림을 받도록 할 수 있습니다. 또는 새 규칙이 사용자 구독의 사용자에 할당되는 경우 알림을 받도록 할 수도 있습니다.
2. 서비스 상태 이벤트가 발생하는 경우 알림을 받으려면 서비스 상태 이벤트가 구독에서 리소스에 적용되는 인시던트 및 유지 관리 이벤트 알림을 포함합니다.

두 경우 모두 활동 로그 경고만 경고를 만드는 구독의 이벤트를 모니터링합니다.

활동 로그 이벤트에 대한 JSON 개체의 모든 최상위 속성에 따라 활동 로그 경고를 구성할 수 있지만, 포털에는 다음과 같은 가장 일반적인 옵션만 표시됩니다.
- **범주** - 관리, 서비스 상태, 자동 크기 조정, 권장 사항 중 하나입니다. [활동 로그 범주에 대한 정보는 이 문서를 참조하고](./monitoring-overview-activity-logs.md#categories-in-the-activity-log) [서비스 상태 이벤트는 여기에서 확인하세요](./monitoring-activity-log-alerts-on-service-notifications.md).
- **리소스 그룹**
- **리소스**
- **리소스 종류**
- **작업 이름** - Resource Manager RBAC 작업 이름입니다.
- **수준** - 이벤트의 심각도 수준(자세한 정보, 정보 제공, 경고, 오류, 중요)입니다.
- **상태** - 이벤트의 상태입니다. 일반적으로 “시작됨”, “실패” 또는 “성공”입니다.
- **이벤트를 시작한 사람** - “호출자”로 알려져 있습니다. 작업을 수행한 사용자의 이메일 주소 또는 Active Directory 식별자입니다.

>[!NOTE]
>경고에서 앞에 나온 조건 중 둘 이상을 지정해야 하며, 하나는 범주에 해당해야 합니다. 활동 로그에서 이벤트를 만들 때마다 활성화되는 경고를 만들 필요는 없습니다.
>
>

활동 로그 경고가 활성화되면 작업 그룹을 사용하여 작업 또는 알림을 생성합니다. 작업 그룹은 알림 채널을 중앙집중화하고 그룹화하기 위해 여러 경고에서 참조될 수 있는 알림 수신자 집합(이메일 주소, 웹후크 URL 또는 SMS 전화 번호)을 다시 사용할 수 있습니다. 활동 로그 경고에서 기존 작업 그룹을 사용하거나 활동 로그 경고를 정의하는 동안 새 작업 그룹을 만들 수 있습니다. 작업 그룹에 대한 자세한 내용은 [여기서](monitoring-action-groups.md) 확인할 수 있습니다.

서비스 상태 알림에 대한 활동 로그 경고는 [여기](monitoring-activity-log-alerts-on-service-notifications.md)에서 알아보세요.

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-with-the-azure-portal"></a>Azure Portal을 사용하여 새 작업 그룹의 활동 로그 이벤트에 대한 경고 만들기
1.  [포털](https://portal.azure.com)에서 **모니터** 블레이드로 이동합니다.

    ![모니터](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  **모니터** 옵션을 클릭하여 모니터 블레이드를 엽니다. 처음에는 **활동 로그** 블레이드가 열립니다.

3.  이제 **경고** 블레이드를 클릭합니다.

    ![경고](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  **활동 로그 경고 추가** 명령을 선택하고 필드를 입력합니다.

5.  **이름**에서 활동 로그 경고의 이름을 지정하고 **설명**을 선택합니다.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

6.  선택한 **구독**은 활동 로그 경고가 저장되는 위치입니다. 현재 작동 중인 구독으로 자동으로 채워집니다. 이는 경고 리소스를 배포하고 활동 로그 이벤트를 모니터링하는 구독입니다.

    ![Add-Alert-New-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

7.  이 경고 리소스가 만들어지는 **리소스 그룹**을 선택합니다. 이는 경고*에 의해* 모니터링되는 리소스 그룹이 아니라 *경고 리소스 자체가 배포되는* 리소스 그룹입니다.

8.  필요에 따라 표시된 추가 필터 집합을 수정하는 **이벤트 범주**를 선택합니다. 관리 이벤트의 경우 여기에는 이 경고에서 모니터링해야 하는 이벤트를 식별하기 위해 **리소스 그룹**, **리소스**, **리소스 종류**, **작업 이름**, **수준**, **상태** 및 **이벤트를 시작한 사람** 값으로 필터링되는 옵션이 포함됩니다.

>[!NOTE]
>경고에 앞에 나온 조건 중 하나 이상을 지정해야 합니다. 활동 로그에서 이벤트를 만들 때마다 활성화되는 경고를 만들 필요는 없습니다.
>
>

9.  **이름** 및 **약식 이름**을 지정하여 **새** 작업 그룹을 만듭니다. 약식 이름은 이 경고를 활성화할 때 보내는 알림에서 참조합니다.

10. 그런 다음 작업에 대해 다음 정보를 제공하여 작업 목록을 정의합니다.

    a. **이름:** 작업의 이름, 별칭 또는 식별자입니다.

    b. **작업 유형:** SMS, 전자 메일 또는 웹후크 중에서 작업 유형을 선택합니다.

    c. **세부 정보:** 선택한 작업 유형에 따라 전화 번호, 이메일 주소 또는 웹후크 URI를 제공합니다.

11. 경고 만들기가 완료되면 **확인**을 선택합니다.

경고가 완전히 전파된 다음, 새 이벤트가 경고의 조건과 일치할 때 활성화되고 트리거하는 데 몇 분 정도 걸립니다.

[활동 로그 경고에 대한 웹후크 스키마에 대한 자세한 내용은 이 문서를 참조하세요](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>이러한 단계에서 정의한 작업 그룹은 향후의 모든 경고 정의에 대해 기존 작업 그룹으로 다시 사용할 수 있습니다.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-with-the-azure-portal"></a>Azure Portal을 사용하여 기존 작업 그룹의 활동 로그 이벤트에 대한 경고 만들기
1.  [포털](https://portal.azure.com)에서 **모니터** 블레이드로 이동합니다.

    ![모니터](./media/monitoring-activity-log-alerts/home-monitor.png)
2.  **모니터** 옵션을 클릭하여 모니터 블레이드를 엽니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.  이제 **경고** 섹션을 클릭합니다.

    ![경고](./media/monitoring-activity-log-alerts/alerts-blades.png)
4.  **활동 로그 경고 추가** 명령을 선택하고 필드를 입력합니다.

5.  **이름**에서 활동 로그 경고의 이름을 지정하고 **설명**을 선택합니다. 이 경고가 발생할 때 보내는 알림에 이러한 항목이 표시됩니다.

    ![Add-Alert](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)
6.  선택한 **구독**은 활동 로그 경고가 저장되는 위치입니다. 현재 작동 중인 구독으로 자동으로 채워집니다. 이는 경고 리소스를 배포하고 활동 로그 이벤트를 모니터링하는 구독입니다.

    ![Add-Alert-Existing-Action-Group](./media/monitoring-activity-log-alerts/activity-log-alert-existing-action-group.png)
7.  이 경고 리소스가 만들어지는 **리소스 그룹**을 선택합니다. 이는 경고*에 의해* 모니터링되는 리소스 그룹이 아니라 *경고 리소스 자체가 배포되는* 리소스 그룹입니다.

8.  필요에 따라 표시된 추가 필터 집합을 수정하는 **이벤트 범주**를 선택합니다. 관리 이벤트의 경우 여기에는 이 경고에서 모니터링해야 하는 이벤트를 식별하기 위해 **리소스 그룹**, **리소스**, **리소스 종류**, **작업 이름**, **수준**, **상태** 및 **이벤트를 시작한 사람** 값으로 필터링되는 옵션이 포함됩니다.

9.  **다음을 통해 알림**에서 **기존 작업 그룹**을 선택합니다. 목록에서 기존 작업 그룹을 선택합니다.

10. 경고 만들기가 완료되면 **확인**을 선택합니다.

경고가 완전히 전파된 다음, 새 이벤트가 경고의 조건과 일치할 때 활성화되고 트리거하는 데 몇 분 정도 걸립니다.

## <a name="managing-your-alerts"></a>경고 관리

경고를 만들었으면 모니터 블레이드의 경고 섹션에 해당 경고가 표시됩니다. 관리하려는 경고를 선택하면 다음 작업을 수행할 수 있습니다.
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

