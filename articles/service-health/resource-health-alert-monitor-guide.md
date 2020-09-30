---
title: Azure Portal을 사용 하 여 Resource Health 경고 만들기
description: Azure 리소스를 사용할 수 없게 되 면 알리는 Azure Portal를 사용 하 여 경고를 만듭니다.
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 127aad1691f7bb6b6c64332eefde734a809ab75f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540679"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>Azure Portal을 사용하여 리소스 상태 경고 구성

이 문서에서는 Azure Portal를 사용 하 여 리소스 상태 알림에 대 한 활동 로그 경고를 설정 하는 방법을 보여 줍니다.

Azure Resource Health는 Azure 리소스의 현재 및 과거 상태에 대한 정보를 알려줍니다. Azure Resource Health 경고는 이러한 리소스의 상태가 변경되면 거의 실시간으로 알려줍니다. Resource Health 경고를 프로그래밍 방식으로 만들면 사용자가 경고를 대량으로 생성하고 사용자 지정할 수 있습니다.

리소스 상태 알림은 활동 로그에 저장 된 대용량 정보를 제공 하는 [Azure 활동 로그](../azure-monitor/platform/platform-logs-overview.md) 에 저장 됩니다. 리소스 상태 알림에 대 한 경고를 쉽게 확인 하 고 설정할 수 있는 별도의 사용자 인터페이스가 있습니다.
Azure 리소스가 Azure 구독에 리소스 상태 알림을 보낼 때 경고를 받을 수 있습니다. 다음 항목에 따라 경고를 구성할 수 있습니다.

* 영향을 받는 구독
* 영향을 받는 리소스 유형입니다.
* 영향을 받는 리소스 그룹입니다.
* 영향을 받는 리소스입니다.
* 영향을 받는 리소스의 이벤트 상태입니다.
* 영향을 받는 리소스 상태입니다.
* 영향을 받는 리소스의 유형 (s)입니다.

다음과 같이 경고를 받는 사람도 구성할 수 있습니다.

* 기존 작업 그룹을 선택합니다.
* 새 작업 그룹을 만듭니다(향후 경고에 사용할 수 있음).

작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조하세요.

Azure Resource Manager 템플릿을 사용 하 여 리소스 상태 알림 경고를 구성 하는 방법에 대 한 자세한 내용은 [리소스 관리자 템플릿](./resource-health-alert-arm-template-guide.md)을 참조 하세요.
Azure Portal를 사용 하 여 경고 Resource Health

## <a name="resource-health-alert-using-azure-portal"></a>Azure Portal을 사용 하 여 경고 Resource Health

1. Azure [portal](https://portal.azure.com/)에서 **Service Health**를 선택 합니다.

    ![Service Health 선택](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. **Resource Health** 섹션에서 **Service Health**를 선택 합니다.
3. **리소스 상태 경고 추가** 를 선택 하 고 필드를 입력 합니다.
4. 경고 대상에서 경고를 발생 시킬 **구독**, **리소스 유형**, **리소스 그룹** 및 **리소스** 를 선택 합니다.

    ![대상 선택 영역 선택](./media/resource-health-alert-monitor-guide/alert-target.png)

5. 경고 조건에서 다음을 선택 합니다.
    1. 경고를 발생 시킬 **이벤트 상태** 입니다. 이벤트의 심각도 수준: 활성, 해결 됨, 진행 중, 업데이트 됨
    2. 경고를 받으려는 **리소스 상태** 입니다. 이벤트의 리소스 상태: 사용 가능, 사용할 수 없음, 알 수 없음, 성능 저하 됨
    3. 경고를 표시 하려는 **이유 유형** 입니다. 이 이벤트의 원인: 플랫폼이 시작 됨, 사용자가 시작한 ![ 경고 조건 선택 상태 선택](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. 경고 세부 정보 정의에서 다음 세부 정보를 입력합니다.
    1. **경고 규칙 이름**: 새 경고 규칙의 이름입니다.
    2. **설명**: 새 경고 규칙에 대한 설명입니다.
    3. **리소스 그룹에 경고 저장**: 이 새 규칙을 저장하고자 하는 리소스 그룹을 선택합니다.
7. **작업 그룹** 아래의 드롭다운 메뉴에서 이 새 경고 규칙에 할당할 작업 그룹을 지정합니다. 또는 [새 작업 그룹을 만들어](../azure-monitor/platform/action-groups.md) 새 규칙에 할당합니다. 새 그룹을 만들려면 + **새 그룹**을 선택 합니다.
8. 규칙을 만든 후 사용하도록 설정하려면 **규칙을 만들면 바로 사용**에 대해 **예**를 선택합니다.
9. **경고 규칙 만들기**를 선택합니다.

활동 로그에 대한 새 경고 규칙이 생성되고 창의 오른쪽 상단에 확인 메시지가 나타납니다.
규칙을 사용/사용하지 않도록 설정, 편집 또는 삭제할 수 있습니다. [활동 로그 규칙을 관리 하는 방법](../azure-monitor/platform/alerts-activity-log.md#view-and-manage-in-the-azure-portal)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

Resource Health에 대해 알아봅니다.

* [Azure Resource Health 개요](Resource-health-overview.md)
* [Azure Resource Health를 통해 사용할 수 있는 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)

Service Health 경고 만들기:

* [Service Health에 대한 경고 구성](./alerts-activity-log-service-notifications-portal.md) 
* [Azure 활동 로그 이벤트 스키마](../azure-monitor/platform/activity-log-schema.md)
* [Resource Manager 템플릿을 사용하여 리소스 상태 경고 구성](./resource-health-alert-arm-template-guide.md)
