---
title: "Log Analytics를 사용하여 Azure HDInsight 클러스터 모니터링 | Microsoft Docs"
description: "Azure Log Analytics를 사용하여 HDInsight 클러스터에서 실행되는 작업을 모니터링하는 방법을 알아봅니다."
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
ms.openlocfilehash: af5b05a366c1abbe7c91d186358dba2b4a957f92
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Azure Log Analytics를 사용하여 HDInsight 클러스터 모니터링(미리 보기)

이 문서에서는 Azure Log Analytics를 사용하여 HDInsight Hadoop 클러스터 작업을 모니터링하는 방법에 대해 알아봅니다.

Log Analytics는 클라우드 및 온-프레미스 환경을 모니터링하여 해당 가용성과 성능을 유지하는 [OMS(Operations Management Suite)](../operations-management-suite/operations-management-suite-overview.md)의 서비스입니다. 이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다. 

## <a name="prerequisites"></a>필수 조건

* **Azure 구독** - 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [지금 무료 Azure 계정 만들기](https://azure.microsoft.com/free)를 참조하세요.

* **Azure HDInsight 클러스터**. Azure OMS는 현재 다음 HDInsight 클러스터 유형에서 사용할 수 있습니다.
    * Hadoop은
    * Spark
    * HBase
    * Storm
    * Kafka
    * Interactive Hive

    HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure HDInsight 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.


* **Log Analytics 작업 영역** - 이 작업 영역은 자체의 데이터 리포지토리, 데이터 원본 및 솔루션을 포함한 고유한 Log Analytics 환경으로 생각할 수 있습니다. Azure HDInsight 클러스터와 연결할 수 있는 작업 영역 하나가 이미 만들어져 있어야 합니다. 지침은 [Log Analytics 작업 영역 만들기](../log-analytics/log-analytics-get-started.md#2-create-a-workspace)를 참조하세요.

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Azure Log Analytics를 사용하도록 HDInsight 클러스터 구성

이 섹션에서는 Azure Log Analytics 작업 영역을 사용하여 작업, 디버그 로그 등을 모니터링하도록 기존 HDInsight Hadoop 클러스터를 구성합니다.

1. Azure Portal의 왼쪽 창에서 **HDInsight 클러스터**를 클릭한 다음 Azure Log Analytics로 구성하려는 클러스터의 이름을 클릭합니다.

2. 클러스터 블레이드의 왼쪽 창에서 **모니터링**을 클릭합니다.

3. 오른쪽 창에서 **사용**을 클릭한 다음 기존 Log Analytics 작업 영역을 선택합니다. **Save**를 클릭합니다.

    ![HDInsight 클러스터에 대한 모니터링 사용](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight 클러스터에 대한 모니터링 사용")

4. 클러스터에서 Log Analytics를 사용하여 모니터링하도록 구성되면 탭 위쪽에 **OMS 대시보드 열기** 옵션이 표시됩니다. 이 단추를 클릭합니다.

    ![OMS 대시보드 열기](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "OMS 대시보드 열기")

5. 메시지가 표시되면 Azure 자격 증명을 입력합니다. Microsoft OMS 대시보드에 전달됩니다.

    ![Operations Management Suite 포털](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Operations Management Suite 포털")

## <a name="next-steps"></a>다음 단계
* [Log Analytics에 HDInsight 클러스터 관리 솔루션 추가](hdinsight-hadoop-oms-log-analytics-management-solutions.md)

