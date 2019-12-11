---
title: 경고 구성-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 메트릭 경고를 구성 하 고 액세스 하는 방법을 설명 합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a830f344023967f07ab809d67833f99e4e2958
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977610"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Portal를 사용 하 여 Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 메트릭에 대 한 경고를 설정 합니다.

이 문서에서는 Azure Portal를 사용 하 여 Azure Database for PostgreSQL 경고를 설정 하는 방법을 보여 줍니다. Azure 서비스에 대 한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

지정 된 메트릭의 값이 임계값을 초과 하는 경우 트리거할 경고를 설정 합니다. 이 경고는 조건이 처음 충족 될 때 트리거되고 나중에 계속 트리거됩니다.

트리거할 때 다음 작업을 수행 하도록 경고를 구성할 수 있습니다.
* 서비스 관리자 및 공동 관리자에 게 전자 메일 알림을 보냅니다.
* 지정 하는 추가 전자 메일에 전자 메일을 보냅니다.
* Webhook를 호출 합니다.

다음을 사용 하 여 경고 규칙에 대 한 정보를 구성 하 고 가져올 수 있습니다.
* [Azure Portalra](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal에서 메트릭에 대 한 경고 규칙을 만듭니다.
1. [Azure Portal](https://portal.azure.com/)에서 모니터링할 Azure Database for PostgreSQL 서버를 선택 합니다.

2. 사이드바의 **모니터링** 섹션에서 다음과 같이 **경고** 를 선택 합니다.

   ![경고 규칙 선택](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. **새 경고 규칙** (+ 아이콘)을 선택 합니다.

4. 아래와 같이 **규칙 만들기** 페이지가 열립니다. 필요한 정보를 입력 합니다.

   ![메트릭 경고 양식 추가](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. **조건** 섹션에서 **추가**를 선택 합니다.

6. 알림을 받을 신호 목록에서 메트릭을 선택 합니다. 이 예에서는 "Storage percent"를 선택 합니다.
   
   ![Metrika kiválasztása](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. 경고 논리를 구성 합니다.

    * **연산자** (예: "보다 큼")
    * **임계값** (예: 85%)
    * **집계 세분성** 경고를 트리거하기 전에 메트릭 규칙을 만족 해야 하는 시간 (예: "지난 30 분 동안")
    * 및 **평가 빈도** (예: "1 분")
   
   완료 **되 면 완료를** 선택 합니다.

   ![Metrika kiválasztása](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. **작업 그룹** 섹션에서 **새로 만들기** 를 선택 하 여 경고에 대 한 알림을 받을 새 그룹을 만듭니다.

9. 이름, 약식 이름, 구독 및 리소스 그룹을 사용 하 여 "작업 그룹 추가" 양식을 작성 합니다.

    ![작업 그룹](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. **이메일/SMS/푸시/음성** 동작 유형을 구성 합니다.
    
    "전자 메일 Azure Resource Manager 역할"을 선택 하 여 구독 소유자, 참가자 및 읽기 권한자에 게 알림을 보냅니다.
   
    완료 되 면 **확인을** 선택 합니다.

    ![작업 그룹](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. 경고 규칙 이름, 설명 및 심각도를 지정 합니다.

    ![작업 그룹](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. 경고 **규칙 만들기** 를 선택 하 여 경고를 만듭니다.

    몇 분 내에 경고가 활성 상태이 고 앞에서 설명한 대로 트리거됩니다.

## <a name="manage-your-alerts"></a>A riasztások kezelése

경고를 만든 후에는 경고를 선택 하 고 다음 작업을 수행할 수 있습니다.

* 이 경고와 관련 된 이전 날짜의 메트릭 임계값 및 실제 값을 보여 주는 그래프를 표시 합니다.
* 경고 규칙을 **편집** 하거나 **삭제** 합니다.
* 알림 수신을 일시적으로 중지 하거나 다시 시작 하려면 경고를 **사용 하지 않도록** 설정 하거나 **사용 하도록 설정** 합니다.

## <a name="next-steps"></a>Következő lépések
* [경고에서 웹 후크 구성](../azure-monitor/platform/alerts-webhooks.md)에 대해 자세히 알아보세요.
* [메트릭 컬렉션의 개요](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 를 확인 하 여 서비스를 사용할 수 있고 응답성을 유지할 수 있습니다.
