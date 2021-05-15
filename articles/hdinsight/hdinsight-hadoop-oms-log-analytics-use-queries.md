---
title: Azure Monitor 로그를 쿼리하여 Azure HDInsight 클러스터 모니터링
description: Azure Monitor 로그에서 쿼리를 실행하여 HDInsight 클러스터에서 실행되는 작업을 모니터링하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 3cf97039983ecec44a7c3a32e178fdcf9f9c45ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872186"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor Logs를 쿼리하여 HDInsight 클러스터 모니터링

Azure HDInsight 클러스터에서 Azure Monitor 로그를 사용하는 방법에 대한 몇 가지 기본적인 시나리오를 살펴봅니다.

* [HDInsight 클러스터 메트릭 분석](#analyze-hdinsight-cluster-metrics)
* [이벤트 경고 만들기](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure Monitor 로그를 사용하도록 HDInsight 클러스터를 구성해야 하고, HDInsight 클러스터와 관련된 Azure Monitor 로그 모니터링 솔루션이 작업 영역에 추가되어 있어야 합니다. 지침은 [HDInsight 클러스터에서 Azure Monitor 로그 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)을 참조하세요.

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight 클러스터 메트릭 분석

HDInsight 클러스터에 대한 특정 메트릭을 조회하는 방법을 알아봅니다.

1. Azure Portal에서 HDInsight 클러스터에 연결된 Log Analytics 작업 영역을 엽니다.
1. **일반** 에서 **로그** 를 선택합니다.
1. Azure Monitor 로그를 사용하도록 구성된 모든 HDInsight 클러스터에 대해 모든 사용 가능한 메트릭을 검색하려면 검색 상자에 다음 쿼리를 입력한 다음, **실행** 을 선택합니다. 결과를 검토합니다.

    ```kusto
    search *
    ```

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png" alt-text="Apache Ambari 분석 모든 메트릭 검색":::

1. 왼쪽 메뉴에서 **필터** 탭을 선택합니다.

1. **형식** 아래에서 **하트비트** 를 선택합니다. 그런 다음, **적용 및 실행** 을 선택합니다.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png" alt-text="로그 분석 검색별 메트릭":::

1. 텍스트 상자의 쿼리가 다음과 같이 변경됩니다.

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. 왼쪽 메뉴에 제공되는 옵션을 사용하여 더 자세히 알아볼 수 있습니다. 예를 들면 다음과 같습니다.

   - 특정 노드에서 로그를 보려면 다음을 수행합니다.

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png" alt-text="특정 오류 검색 output1":::

   - 특정 시간에 로그를 보려면 다음을 수행합니다.

     :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png" alt-text="특정 오류 검색 output2":::

1. **적용 및 실행** 을 선택하고 결과를 검토합니다. 또한 쿼리는 다음과 같이 업데이트되었습니다.

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>추가 샘플 쿼리

클러스터 이름별로 분류된 10분 간격으로 사용되는 평균 리소스를 기반으로 한 샘플 쿼리:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

사용된 리소스의 평균에 따라 구체화하는 대신 다음 쿼리를 사용하여 10분 간격으로 최대 리소스가 사용된 시점(90번째 및 95번째 백분위수)에 따라 결과를 구체화할 수 있습니다.

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>추적 이벤트에 대한 경고 만들기

경고를 만드는 첫 번째 단계는 경고를 트리거하는 쿼리에 도달하는 것입니다. 경고를 만들려는 모든 쿼리를 사용할 수 있습니다.

1. Azure Portal에서 HDInsight 클러스터에 연결된 Log Analytics 작업 영역을 엽니다.
1. **일반** 에서 **로그** 를 선택합니다.
1. 경고를 만들려는 항목에 대해 다음 쿼리를 실행한 다음, **실행** 을 선택합니다.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    쿼리는 HDInsight 클러스터에서 실행되는 실패한 애플리케이션의 목록을 제공합니다.

1. 페이지 맨 위에서 **새 경고 규칙** 을 선택합니다.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png" alt-text="새 경고 규칙":::

1. **규칙 만들기** 창에서 쿼리 및 기타 세부 정보를 입력하여 경고를 만든 다음, **경고 규칙 만들기** 를 선택합니다.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png" alt-text="경고 조건 정의.":::

### <a name="edit-or-delete-an-existing-alert"></a>기존 경고를 편집 또는 삭제

1. Azure Portal에서 Log Analytics 작업 영역을 엽니다.

1. 왼쪽 메뉴의 **모니터링** 에서 **메트릭** 을 선택합니다.

1. 위쪽에서 **경고 규칙 관리** 를 선택합니다.

1. 편집하거나 삭제하려는 경고를 선택합니다.

1. **저장**, **취소**, **사용 안 함** 및 **삭제** 와 같은 옵션이 있습니다.

    :::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png" alt-text="HDInsight Azure Monitor 로그 경고 삭제 편집":::

자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)
* [Azure Monitor에서 뷰 디자이너를 사용하여 사용자 지정 보기 만들기](../azure-monitor/visualize/view-designer.md)
