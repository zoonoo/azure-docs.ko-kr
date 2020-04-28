---
title: Azure Monitor 로그를 쿼리하여 Azure HDInsight 클러스터 모니터링
description: Azure Monitor 로그에 대해 쿼리를 실행 하 여 HDInsight 클러스터에서 실행 중인 작업을 모니터링 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74803830"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor 로그를 쿼리하여 HDInsight 클러스터 모니터링

Azure Monitor 로그를 사용 하 여 Azure HDInsight 클러스터를 모니터링 하는 방법에 대 한 몇 가지 기본적인 시나리오를 알아봅니다.

* [HDInsight 클러스터 메트릭 분석](#analyze-hdinsight-cluster-metrics)
* [이벤트 경고 만들기](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>전제 조건

Azure Monitor 로그를 사용 하도록 HDInsight 클러스터를 구성 하 고 작업 영역에 HDInsight 클러스터 관련 Azure Monitor 로그 모니터링 솔루션을 추가 해야 합니다. 지침은 [HDInsight 클러스터에서 Azure Monitor 로그 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)을 참조 하세요.

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight 클러스터 메트릭 분석

HDInsight 클러스터에 대한 특정 메트릭을 조회하는 방법을 알아봅니다.

1. Azure Portal에서 HDInsight 클러스터에 연결된 Log Analytics 작업 영역을 엽니다.
1. **일반**에서 **로그**를 선택 합니다.
1. Azure Monitor 로그를 사용 하도록 구성 된 모든 HDInsight 클러스터에 대해 사용 가능한 모든 메트릭에 대해 모든 메트릭을 검색 하려면 검색 상자에 다음 쿼리를 입력 하 고 **실행**을 선택 합니다. 결과를 검토합니다.

    ```kusto
    search *
    ```

    ![Apache Ambari analytics 모든 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "모든 메트릭 검색")

1. 왼쪽 메뉴에서 **필터** 탭을 선택 합니다.

1. **유형**에서 **하트 비트**를 선택 합니다. 그런 다음 **적용 & 실행**을 선택 합니다.

    ![log analytics 검색 별 메트릭](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "특정 메트릭 검색")

1. 텍스트 상자의 쿼리가 다음과 같이 변경 됩니다.

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. 왼쪽 메뉴에서 제공 되는 옵션을 사용 하 여 더 자세히 알아볼 수 있습니다. 예를 들면 다음과 같습니다.

    - 특정 노드에서 로그를 보려면 다음을 수행 합니다.

        ![특정 오류 검색 output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "특정 오류 검색 output1")

    - 특정 시간에 로그를 보려면 다음을 수행 합니다.

        ![특정 오류 검색 output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "특정 오류 검색 output2")

1. **적용 & 실행** 을 선택 하 고 결과를 검토 합니다. 또한 쿼리는 다음으로 업데이트 되었습니다.

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>추가 샘플 쿼리

클러스터 이름으로 분류 된 10 분 간격으로 사용 된 리소스의 평균을 기반으로 하는 샘플 쿼리입니다.

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
1. **일반**에서 **로그**를 선택 합니다.
1. 경고를 만들려는 다음 쿼리를 실행 하 고 **실행**을 선택 합니다.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    쿼리는 HDInsight 클러스터에서 실행되는 실패한 애플리케이션의 목록을 제공합니다.

1. 페이지 위쪽에서 **새 경고 규칙** 을 선택 합니다.

    ![쿼리를 입력 하 여 변칙 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "쿼리를 입력 하 여 변칙 만들기")

1. **규칙 만들기** 창에서 쿼리 및 기타 세부 정보를 입력하여 경고를 만든 다음, **경고 규칙 만들기**를 선택합니다.

    ![쿼리를 입력 하 여 alert2 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "쿼리를 입력 하 여 alert2 만들기")

### <a name="edit-or-delete-an-existing-alert"></a>기존 경고 편집 또는 삭제

1. Azure Portal에서 Log Analytics 작업 영역을 엽니다.

1. 왼쪽 메뉴의 **모니터링**아래에서 **경고**를 선택 합니다.

1. 위쪽에서 **경고 규칙 관리**를 선택 합니다.

1. 편집하거나 삭제하려는 경고를 선택합니다.

1. **저장**, **취소**, **사용 안 함** 및 **삭제**와 같은 옵션이 있습니다.

    ![HDInsight Azure Monitor 로그 경고 삭제 편집](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-metric.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/log-query/get-started-queries.md)
* [Azure Monitor에서 뷰 디자이너를 사용 하 여 사용자 지정 보기 만들기](../azure-monitor/platform/view-designer.md)
