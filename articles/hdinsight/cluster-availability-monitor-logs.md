---
title: HDInsight의 Azure Monitor 로그를 사용하여 클러스터의 가용성을 모니터링하는 방법
description: Azure Monitor 로그를 사용하여 클러스터 상태 및 가용성을 모니터링하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 299a17e23ca3eb2d954bae7335571ae1f645152e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867154"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>HDInsight의 Azure Monitor 로그를 사용하여 클러스터의 가용성을 모니터링하는 방법

HDInsight 클러스터에는 쿼리 가능 메트릭 및 로그와 구성 가능 경고를 제공하는 Azure Monitor 로그 통합이 포함됩니다. 이 문서에서는 Azure Monitor를 사용하여 클러스터를 모니터링하는 방법을 보여줍니다.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 로그 통합

Azure Monitor 로그를 사용하면 HDInsight 클러스터와 같이 여러 리소스에서 생성된 데이터를 한 곳에서 수집하고 집계하여 통합 모니터링 환경을 실현할 수 있습니다.

필수 구성 요소로 수집된 데이터를 저장하려면 Log Analytics 작업 영역이 필요합니다. 아직 만들지 않은 경우, 여기의 지침을 따라 [Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 수행할 수 있습니다.

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure Monitor 로그 통합 사용

포털의 HDInsight 클러스터 리소스 페이지에서 **Azure Monitor** 를 선택합니다. 그런 다음 **사용** 을 선택하고 드롭다운에서 Log Analytics 작업 영역을 선택합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/azure-portal-monitoring.png" alt-text="HDInsight Operations Management Suite":::

기본적으로 에지 노드를 제외한 모든 클러스터 노드에 OMS 에이전트를 설치합니다. 클러스터 에지 노드에 설치된 OMS 에이전트가 없기 때문에 기본적으로 Log Analytics에는 에지 노드 원격 분석 데이터가 없습니다.

## <a name="query-metrics-and-logs-tables"></a>메트릭 및 로그 테이블 쿼리

Azure Monitor 로그 통합을 사용하도록 설정한 후(몇 분 정도 걸릴 수 있음) **Log Analytics 작업 영역** 리소스로 이동하여 **로그** 를 선택합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/hdinsight-portal-logs.png" alt-text="Log Analytics 작업 영역 로그":::

로그에는 다음과 같은 여러 샘플 쿼리가 나열됩니다.

| 쿼리 이름                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 오늘의 컴퓨터 가용성    | 매시간 로그 전송 컴퓨터 수 차트                     |
| 하트비트 목록                 | 지난 1시간의 전체 컴퓨터 하트비트 목록                           |
| 각 컴퓨터의 마지막 하트비트 | 각 컴퓨터에서 전송된 마지막 하트비트 보기                             |
| 사용할 수 없는 컴퓨터           | 지난 5시간 동안 하트비트를 전송하지 않은 알려진 모든 컴퓨터 목록 |
| 가용성 비율               | 연결된 각 컴퓨터의 가용성 비율 계산                |

예를 들어 위 스크린샷에 표시된 것과 같이 해당 쿼리에서 **실행** 을 선택하여 **가용성 비율** 샘플 쿼리를 실행합니다. 이렇게 하면 클러스터에 있는 각 노드의 가용성 비율이 백분율로 표시됩니다. 여러 HDInsight 클러스터가 동일한 Log Analytics 작업 영역으로 메트릭을 전송하도록 설정한 경우, 표시된 클러스터의 모든 노드(에지 노드 제외)의 가용성 비율이 표시됩니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-availability-rate.png" alt-text="Log Analytics 작업 영역 로그 '가용성 비율' 샘플 쿼리":::

> [!NOTE]  
> 가용성 비율은 24시간 이상 측정되므로 정확한 가용성 비율을 확인하기 위해서는 클러스터를 최소 24시간 동안 실행해야 합니다.

오른쪽 상단 모서리에서 **고정** 을 클릭하여 테이블을 공유 대시보드에 고정할 수 있습니다. 쓰기 가능한 공유 대시보드가 없으면 여기에서 만드는 방법을 확인할 수 있습니다: [Azure Portal에서 대시보드 만들기 및 공유](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Azure Monitor 경고

메트릭 값 또는 쿼리 결과가 특정 조건을 충족할 때 트리거되는 Azure Monitor 경고를 설정할 수 있습니다. 예를 들어 하나 이상의 노드가 5시간 안에 하트비트를 보내지 않은 경우(즉, 사용할 수 없는 것으로 간주) 이메일을 보내는 경고를 만들어봅시다.

**로그** 에서 아래와 같이 해당 쿼리에서 **실행** 을 선택하여 **사용할 수 없는 컴퓨터** 샘플 쿼리를 실행합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-unavailable-computers.png" alt-text="Log Analytics 작업 영역 로그 '사용할 수 없는 컴퓨터' 샘플":::

모든 노드를 사용할 수 있는 경우 이 쿼리는 현재 결과를 0으로 반환해야 합니다. **새 경고 규칙** 을 클릭하여 이 쿼리에 대한 경고 구성을 시작합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png" alt-text="Log Analytics 작업 영역 새 경고 규칙":::

경고는 세 가지 구성요소, 즉 규칙(이 경우에는 Log Analytics 작업 영역)을 생성하는 *리소스* 와 경고를 트리거하는 *조건*, 그리고 경고가 트리거될 때 수행되는 작업을 결정하는 *작업 그룹* 의 세 가지로 이루어져 있습니다.
아래와 같이 **조건 제목** 을 클릭하여 신호 논리 구성을 마칩니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-condition-title.png" alt-text="포털 경고가 규칙 조건을 생성":::

그러면 **신호 논리 구성** 이 열립니다.

**경고 논리** 섹션을 다음과 같이 설정합니다.

*기준: 결과 수, 조건: 초과, 임계 값: 0.*

이 쿼리는 사용할 수 없는 노드만 결과로 반환하기 때문에 결과 수가 0보다 크면 경고가 발생합니다.

**평가 기준** 섹션에서 사용할 수 없는 노드를 확인하려는 빈도에 따라 **기간** 및 **빈도** 를 설정합니다.

이 경고의 목적에 따라, **기간=빈도** 인지 확인해야 합니다. 기간, 빈도 및 기타 경고 매개 변수에 대한 자세한 내용은 [여기](../azure-monitor/alerts/alerts-unified-log.md#alert-logic-definition)를 참조하세요.

신호 논리 구성이 완료되면 **완료** 를 선택합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-configure-signal-logic.png" alt-text="경고 규칙이 신호 로직을 구성":::

기존 작업 그룹이 없는 경우 **작업 그룹** 섹션에서 **새로 만들기** 를 클릭합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-new-action-group.png" alt-text="경고 규칙이 새 작업 그룹을 생성":::

그러면 **작업 그룹 추가** 가 열립니다. **작업 그룹 이름**, **짧은 이름**, **구독** 및 **리소스 그룹** 을 선택합니다. **작업** 섹션에서 **작업 이름** 을 선택하고 **작업 유형** 으로 **이메일/SMS/푸시/음성** 을 선택합니다.

> [!NOTE]
> Azure Function, LogicApp, Webhook, ITSM 및 Automation Runbook과 같은 이메일/SMS/푸시/음성 외에 경고가 트리거 할 수 있는 여러 다른 작업이 있습니다. [자세한 내용을 알아보십시오.](../azure-monitor/alerts/action-groups.md#action-specific-information)

그러면 **이메일/SMS/푸시/음성** 이 열립니다. 수신자의 **이름** 을 선택하고 **이메일** **확인란** 을 선택하고 경고를 보내려는 이메일 주소를 입력합니다. **이메일/SMS/푸시/음성** 에서  **확인** 을 선택한 다음 **작업 그룹 추가** 에서 작업 그룹 구성을 완료합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-add-action-group.png" alt-text="경고 규칙이 추가 작업 그룹을 생성":::

해당 블레이드를 닫은 후 **작업 그룹** 섹션 아래의 작업 그룹 목록을 확인해야 합니다. 마지막으로 **경고 규칙 이름** 및 **설명** 을 입력하고 **심각도** 를 선택하는 것으로 **경고 세부 정보** 섹션을 완료합니다. **경고 규칙 만들기** 를 클릭하여 완료합니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png" alt-text="포털에서 경고 규칙 생성 완료":::

> [!TIP]
> **심각도** 지정 기능은 여러 경고를 만들 때 사용할 수 있는 강력한 도구입니다. 예를 들어 단일 헤드 노드가 다운되면 경고(심각도 1)를 발생시키는 하나의 경고를 만들고, 드물지만 두 헤드 노드가 모두 다운되는 경우에 위험(심각도 0)을 발생시키는 다른 경고를 만들 수 있습니다.

이 경고에 대한 조건이 충족되면 경고가 발생하고, 경고에 대한 세부 정보를 담은 다음과 같은 이메일을 수신하게 됩니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alert-email.png" alt-text="Azure Monitor 경고 이메일 예제":::

또한 **Log Analytics 작업 영역** 의 **경고** 로 이동하여 모든 발생 경고를 심각도별로 그룹화해서 볼 수 있습니다.

:::image type="content" source="media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png" alt-text="Log Analytics 작업 영역 경고":::

심각도 그룹화(예: 위에서 강조 표시된 **심각도 1**)를 선택하면 아래와 같이 발생한 해당 심각도의 모든 경고에 대한 기록이 표시됩니다.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png" alt-text="Log Analytics 작업 영역 심각도 1 경고":::

## <a name="next-steps"></a>다음 단계

* [클러스터 가용성 - Apache Ambari](./hdinsight-cluster-availability.md)
* [Azure Monitor 로그 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)
