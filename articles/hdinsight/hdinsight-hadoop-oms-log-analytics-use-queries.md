---
title: "Azure Log Analytics를 쿼리하여 Azure HDInsight 클러스터 모니터링 | Microsoft Docs"
description: "Azure Log Analytics에서 쿼리를 실행하여 HDInsight 클러스터에서 실행되는 작업을 모니터링하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.contentlocale: ko-kr
ms.lasthandoff: 09/09/2017

---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Azure Log Analytics를 쿼리하여 Azure HDInsight 클러스터 모니터링(미리 보기)

이 문서에서는 Azure HDInsight 클러스터에서 Azure Log Analytics를 사용하는 방법에 대한 몇 가지 시나리오를 살펴봅니다. 가장 일반적인 세 가지 시나리오는 다음과 같습니다.

* OMS에서 HDInsight 클러스터 메트릭 분석
* HDInsight 클러스터에 대한 특정 로그 메시지 검색
* 클러스터에서 발생하는 이벤트에 기반한 경고 만들기

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터에서 Azure Log Analytics를 사용하도록 구성되어 있어야 합니다. 지침은 [HDInsight 클러스터에서 Azure Log Analytics 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)을 참조하세요.

* [Log Analytics에 HDInsight 클러스터 관리 솔루션 추가](hdinsight-hadoop-oms-log-analytics-management-solutions.md)에서 설명한 대로 OMS 작업 영역에 HDInsight 클러스터와 관련된 관리 솔루션이 추가되어 있어야 합니다.

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>OMS에서 HDInsight 클러스터 메트릭 분석

이 섹션에서는 HDInsight 클러스터에 대한 특정 메트릭을 찾는 단계를 안내합니다.

1. OMS 대시보드를 엽니다. Azure Portal에서 Azure Log Analytics와 연결된 HDInsight 클러스터 블레이드를 열고, 모니터링 탭을 클릭한 다음, **OMS 대시보드 열기**를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS 대시보드 열기")

2. OMS 대시보드의 홈 화면에서 **로그 검색**을 클릭합니다.

    ![로그 검색 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "로그 검색 열기")

3. Azure Log Analytics를 사용하도록 구성된 모든 HDInsight 클러스터에 대해 사용할 수 있는 모든 메트릭을 검색하려면 [로그 검색] 창의 **여기서 검색 시작** 텍스트 상자에서 `*`를 입력합니다. ENTER 키를 누릅니다.

    ![모든 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "모든 메트릭 검색")

4. 다음과 같은 출력이 표시됩니다.

    ![모든 메트릭 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "모든 메트릭 검색 출력")

5. 왼쪽 창의 **형식** 범주 아래에서 심층적으로 분석하려는 메트릭을 검색합니다. 이 자습서에서는 `metrics_resourcemanager_queue_root_default_CL`을 선택합니다. 메트릭에 해당하는 확인란을 선택한 다음 **적용**을 클릭합니다.

    > [!NOTE]
    > **[+]자세히** 단추를 클릭하여 원하는 메트릭을 찾을 수도 있습니다. 또한 **적용** 단추는 목록의 아래쪽에 있으므로 스크롤해야 볼 수 있습니다.
    > 
    >    
    이제 텍스트 상자의 쿼리가 다음 스크린샷에서 강조 표시된 상자에 표시된 내용으로 변경됩니다.

    ![특정 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "특정 메트릭 검색")

6. 이제 이 특정 메트릭을 자세히 분석할 수 있습니다. 예를 들어 클러스터 이름별로 분류되고 10분 간격으로 사용된 리소스의 평균에 기반한 기존 출력을 구체화할 수 있습니다. 쿼리 텍스트 상자에서 다음 쿼리를 입력합니다.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![특정 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "특정 메트릭 검색")

7. 사용된 리소스의 평균에 따라 구체화하는 대신 다음 쿼리를 사용하여 10분 간격으로 최대 리소스가 사용된 시점(90번째 및 95번째 백분위수)에 따라 결과를 구체화할 수 있습니다.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![특정 메트릭 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "특정 메트릭 검색")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>HDInsight 클러스터에서 특정 로그 메시지 검색

이 섹션에서는 특정 기간의 오류 메시지를 표시하는 단계를 안내합니다. 다음에 나오는 단계는 관심 있는 오류 메시지에 도달할 수 있는 방법에 대한 하나의 예입니다. 찾으려는 오류를 확인할 수 있는 속성을 사용하면 됩니다.

1. OMS 대시보드를 엽니다. Azure Portal에서 Azure Log Analytics와 연결된 HDInsight 클러스터 블레이드를 열고, 모니터링 탭을 클릭한 다음, **OMS 대시보드 열기**를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS 대시보드 열기")

2. OMS 대시보드의 홈 화면에서 **로그 검색**을 클릭합니다.

    ![로그 검색 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "로그 검색 열기")

3. Azure Log Analytics를 사용하도록 구성된 모든 HDInsight 클러스터에 대한 모든 오류 메시지를 검색하려면 [로그 검색] 창의 **여기서 검색 시작** 텍스트 상자에서 `"Error"`(인용 부호 포함)를 입력합니다. ENTER 키를 누릅니다.

    ![모든 오류 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "모든 오류 검색")

4. 다음과 같은 출력이 표시됩니다.

    ![모든 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "모든 오류 검색 출력")

5. 왼쪽 창의 **형식** 범주 아래에서 심층적으로 분석하려는 오류 유형을 검색합니다. 이 자습서에서는 `log_sparkappsexecutors_CL`을 선택합니다. 메트릭에 해당하는 확인란을 선택한 다음 **적용**을 클릭합니다.

    ![특정 오류 검색](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "특정 오류 검색")

        
6. 이제 텍스트 상자의 쿼리가 아래에서 강조 표시된 상자에 표시된 내용으로 변경되고, 결과는 선택한 유형의 오류만 표시하도록 구체화됩니다.

    ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "특정 오류 검색 출력")

7. 이제 왼쪽 창에서 사용할 수 있는 옵션을 사용하여 이 특정 오류 목록을 자세히 분석할 수 있습니다. 예를 들어 특정 작업자 노드의 오류 메시지만 확인하도록 쿼리를 구체화할 수 있습니다.

    ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "특정 오류 검색 출력")

8. 왼쪽 창에서 적절한 시간 값을 선택하여 오류가 발생한 것으로 인지한 시간에 영역을 더 추가할 수 있습니다.

    ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "특정 오류 검색 출력")

9. 이제 찾고 있는 특정 오류가 아래에 표시됩니다. **[+]자세히 표시**를 클릭하면 실제 오류 메시지를 볼 수 있습니다.

    ![특정 오류 검색 출력](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "특정 오류 검색 출력")

## <a name="create-alerts-to-track-events"></a>이벤트를 추적하는 경고 만들기

경고를 만드는 첫 번째 단계는 경고를 트리거하는 쿼리에 도달하는 것입니다. 간단히 하기 위해 HDInsight 클러스터에서 실행되는 실패한 응용 프로그램 목록을 제공하는 다음 쿼리를 사용하겠습니다.

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

경고를 만들려는 모든 쿼리를 사용할 수 있습니다.

1. OMS 대시보드를 엽니다. Azure Portal에서 Azure Log Analytics와 연결된 HDInsight 클러스터 블레이드를 열고, 모니터링 탭을 클릭한 다음, **OMS 대시보드 열기**를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "OMS 대시보드 열기")

2. OMS 대시보드의 홈 화면에서 **로그 검색**을 클릭합니다.

    ![로그 검색 열기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "로그 검색 열기")

3. [로그 검색] 창의 **여기서 검색 시작** 텍스트 상자에서 경고를 만들려는 쿼리를 붙여넣고 Enter 키를 누른 다음 **경고** 단추를 클릭합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "쿼리를 입력하여 경고 만들기")

4. **경고 규칙 추가** 창에서 쿼리 및 기타 세부 정보를 입력하여 경고를 만든 다음 **저장**을 클릭합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "쿼리를 입력하여 경고 만들기")

    이 스크린샷에서는 경고 쿼리에서 출력을 검색하는 경우에만 전자 메일 알림을 보냅니다.

5. 기존 경고를 편집하거나 삭제할 수도 있습니다. 이렇게 하려면 OMS 포털의 모든 페이지에서 **설정** 아이콘을 클릭합니다.

    ![쿼리를 입력하여 경고 만들기](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "쿼리를 입력하여 경고 만들기")

6. **설정** 페이지에서 **경고**를 클릭하여 만든 경고를 확인합니다. 경고를 사용하거나 사용하지 않도록 설정하거나, 편집하거나, 삭제할 수도 있습니다. 자세한 내용은 [Log Analytics에서 경고 규칙 작업](../log-analytics/log-analytics-alerts-creating.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

* [OMS Log Analytics 작업(영문)](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics에서 경고 규칙 만들기](../log-analytics/log-analytics-alerts-creating.md)

