---
title: "Azure Log Analytics에 HDInsight 클러스터 관리 솔루션 추가 | Microsoft Docs"
description: "Azure Log Analytics를 사용하여 HDInsight 클러스터에 대한 사용자 지정 보기를 만드는 방법을 알아봅니다."
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
ms.date: 09/12/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>Log Analytics(미리 보기)에 HDInsight 클러스터 관리 솔루션 추가

HDInsight는 Azure Log Analytics에 추가할 수 있는 클러스터와 관련된 관리 솔루션을 제공합니다. [관리 솔루션](../log-analytics/log-analytics-add-solutions.md)은 OMS에 기능을 추가하여 Log Analytics에 추가 데이터 및 분석 도구를 제공합니다. 이러한 솔루션은 HDInsight 클러스터에서 중요한 성능 메트릭을 수집하고, 메트릭을 검색할 수 있는 도구를 제공합니다. 또한 HDInsight에서 지원되는 대부분의 클러스터 유형에 대해 시각화 및 대시보드를 제공합니다. 솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다. 

이 문서에서는 클러스터와 관련된 관리 솔루션을 OMS 작업 영역에 추가하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터에서 Azure Log Analytics를 사용하도록 구성되어 있어야 합니다. 지침은 [HDInsight 클러스터에서 Azure Log Analytics 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)을 참조하세요.

## <a name="add-cluster-specific-management-solutions"></a>클러스터와 관련된 관리 솔루션 추가

이 섹션에서는 HBase 클러스터 관리 솔루션을 기존 OMS 작업 영역에 추가합니다. 다른 HDInsight 클러스터 유형에 대해서는 유사한 솔루션이 곧 제공될 예정입니다.

1. OMS 대시보드를 엽니다. Azure Portal에서 Azure Log Analytics와 연결된 HDInsight 클러스터 블레이드를 열고, 모니터링 탭을 클릭한 다음, **OMS 대시보드 열기**를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "OMS 대시보드 열기")

1. OMS 대시보드의 왼쪽 창에서 **솔루션 갤러리** 또는 [뷰 디자이너] 아이콘을 클릭합니다.

    ![OMS에 관리 솔루션 추가](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "OMS에 관리 솔루션 추가")

2. [솔루션 갤러리]에서 **HDInsight HBase 모니터링**을 찾은 다음 타일을 클릭합니다.

    ![HBase 관리 솔루션 찾기](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "HBase 관리 솔루션 찾기")

3. 다음 화면에서 **추가**를 클릭합니다.

     ![HBase 관리 솔루션 추가](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "HBase 관리 솔루션 추가")

4. 이제 HBase 관리 솔루션에 대한 타일이 OMS 대시보드에 표시됩니다. OMS와 연결된 클러스터(이 문서에 대한 필수 조건의 일부)가 HBase 클러스터인 경우 타일에는 클러스터 이름과 해당 클러스터의 노드 수가 표시됩니다.

    ![추가된 HBase 관리 솔루션](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "추가된 HBase 관리 솔루션")

## <a name="next-steps"></a>다음 단계

* [Azure Log Analytics를 쿼리하여 HDInsight 클러스터 모니터링](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>참고 항목

* [OMS Log Analytics 작업(영문)](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics에서 경고 규칙 만들기](../log-analytics/log-analytics-alerts-creating.md)

