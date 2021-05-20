---
title: 경고 구성 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 메트릭 경고를 구성하고 액세스하는 방법을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: f5557140d77865a6d4c44316cecd512f877736e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577083"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL - 하이퍼스케일(Citus) 메트릭에 대한 경고 설정

이 문서에서는 Azure Portal을 사용하여 Azure Database for PostgreSQL 경고를 설정하는 방법을 보여 줍니다. Azure 서비스에 대한 [모니터링 메트릭](concepts-hyperscale-monitoring.md)을 기반으로 하는 경고를 받을 수 있습니다.

지정된 메트릭의 값이 임계값을 초과하는 경우 트리거되는 경고를 설정합니다. 이 경고는 조건이 처음 충족될 때 트리거되고, 이후에도 계속 트리거됩니다.

트리거되면 다음 작업을 수행하도록 경고를 구성할 수 있습니다.
* 서비스 관리자 및 공동 관리자에게 이메일 알림을 보냅니다.
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* Webhook를 호출합니다.

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.
* [Azure Portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure Portal에서 메트릭에 대한 경고 규칙 만들기
1. [Azure Portal](https://portal.azure.com/)에서 모니터링할 Azure Database for PostgreSQL 서버를 선택합니다.

2. 다음과 같이 사이드바의 **모니터링** 섹션에서 **경고** 를 선택합니다.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="경고 규칙 선택":::

3. **새 경고 규칙**(+ 아이콘)을 선택합니다.

4. 아래와 같이 **규칙 만들기** 페이지가 열립니다. 필수 정보를 입력합니다.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="메트릭 경고 양식 추가":::

5. **조건** 섹션 내에서 **추가** 를 선택합니다.

6. 신호 목록에서 경고를 발생할 메트릭을 선택합니다. 이 예제에서는 "스토리지 비율"을 선택합니다.
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="여러 신호를 볼 수 있는 신호 논리 구성 페이지를 보여 주는 스크린샷":::

7. 경고 논리를 구성합니다.

    * **연산자**(예: "보다 큼")
    * **임계값**(예: 85%)
    * **집계 세분성** - 알림이 트리거되기 전에 메트릭 규칙을 충족해야 하는 시간(예: "지난 30분")
    * **평가 빈도**(예: "1분")
   
   완료되면 **완료** 를 선택합니다.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="경고 논리를 구성할 수 있는 창을 보여 주는 스크린샷":::

8. **작업 그룹** 섹션에서 **새로 만들기** 를 선택하여 경고 알림을 받을 새 그룹을 만듭니다.

9. 이름, 약식 이름, 구독 및 리소스 그룹을 사용하여 "작업 그룹 추가" 양식을 채웁니다.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="설명된 값을 입력할 수 있는 작업 그룹 추가 양식을 보여 주는 스크린샷":::

10. **이메일/SMS/푸시/음성** 작업 유형을 구성합니다.
    
    구독 소유자, 기여자 및 읽기 권한자에게 알림을 보내려면 "이메일 Azure Resource Manager 역할"을 선택합니다.
   
    완료되면 **확인** 을 선택합니다.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="이메일/SMS/푸시/음성 창을 보여 주는 스크린샷":::

11. 경고 규칙 이름, 설명 및 심각도를 지정합니다.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="경고 세부 정보 창을 보여 주는 스크린샷"::: 

12. **경고 규칙 만들기** 를 선택하여 경고를 만듭니다.

    앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

### <a name="managing-alerts"></a>경고 관리

경고가 만들어지면 해당 경고를 선택하고, 다음 작업을 수행할 수 있습니다.

* 이 경고와 관련된 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 확인.
* 경고 규칙을 **편집** 또는 **삭제**.
* 알림 수신을 일시적으로 중지하거나 다시 시작하려면 경로를 **사용 안 함** 또는 **사용** 으로 설정.

## <a name="suggested-alerts"></a>제안되는 경고

### <a name="disk-space"></a>디스크 공간

모니터링 및 경고는 모든 프로덕션 하이퍼스케일(Citus) 서버 그룹에 중요합니다. 기본 PostgreSQL 데이터베이스가 제대로 작동하려면 사용 가능한 디스크 공간이 필요합니다. 디스크가 가득 차면 데이터베이스 서버 노드가 오프라인 상태가 되고 공간을 사용할 수 있을 때까지 시작이 거부됩니다. 이 시점에서 상황을 해결하려면 Microsoft 지원 요청이 필요합니다.

프로덕션 이외의 용도로도 모든 서버 그룹의 모든 노드에 대한 디스크 공간 경고를 설정하는 것이 좋습니다. 디스크 공간 사용량 경고는 노드에 개입하여 정상 상태로 유지하는 데 필요한 사전 경고를 제공합니다. 최상의 결과를 얻으려면 75%, 85% 및 95%의 사용량에서 일련의 경고를 시도합니다. 빠른 데이터 수집은 디스크를 더 빨리 채우므로 선택할 백분율은 데이터 수집 속도에 따라 달라집니다.

디스크가 공간 제한에 도달하면 다음 기술을 시도하여 더 많은 사용 가능한 공간을 확보합니다.

* 데이터 보존 정책을 검토합니다. 가능한 경우 오래된 데이터를 콜드 스토리지로 이동합니다.
* 서버 그룹에 [노드를 추가](howto-hyperscale-scale-grow.md#add-worker-nodes)하고, 분할된 데이터베이스의 균형을 재조정하는 것이 좋습니다. 균형을 재조정하면 데이터를 더 많은 컴퓨터에 분산시킵니다.
* 작업자 노드의 [용량을 확장](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes)하는 것이 좋습니다. 각 작업자에는 최대 2TiB의 스토리지가 있을 수 있습니다. 그러나 노드 추가가 더 빨리 완료되므로 노드 크기를 조정하기 전에 노드를 추가해야 합니다.

### <a name="cpu-usage"></a>CPU 사용량

CPU 사용량 모니터링은 성능 기준을 설정하는 데 유용합니다. 예를 들어 CPU 사용량이 일반적으로 약 40~60%임을 알 수 있습니다. CPU 사용량이 갑자기 약 95%를 가리키기 시작하면 변칙을 인식할 수 있습니다. CPU 사용량은 유기적 증가를 반영할 수 있지만, 잘못된 쿼리를 나타낼 수도 있습니다. CPU 경고를 만들 때 장기적 증가를 포착하고 일시적 급증을 무시하도록 장기 집계 세분성을 설정합니다.

## <a name="next-steps"></a>다음 단계
* [경고에서의 webhook 구성](../azure-monitor/alerts/alerts-webhooks.md)에 대해 자세히 알아봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](../azure-monitor/data-platform.md) 의 개요를 살펴봅니다.
