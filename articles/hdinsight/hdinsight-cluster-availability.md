---
title: Azure HDInsight에서 Apache Ambari를 사용 하 여 클러스터 가용성을 모니터링 하는 방법
description: Apache Ambari를 사용 하 여 클러스터 상태 및 가용성을 모니터링 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 615e23dc388f36f5ae1cd7e0d846acc14ffa2236
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086418"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Ambari를 사용 하 여 클러스터 가용성을 모니터링 하는 방법

HDInsight 클러스터에는 상태 정보를 한눈에 볼 때 미리 정의 된 경고를 제공 하는 Apache Ambari이 포함 되어 있습니다.

이 문서에서는 Ambari를 사용 하 여 클러스터를 모니터링 하 고, Ambari 경고를 구성 하 고, 노드 가용성을 모니터링 하 고, 하나 이상의 노드에서 5 시간 안에 하트 비트를 받지 못한 경우에 발생 하는 Azure Monitor 경고를 만드는 방법을 보여 줍니다.

## <a name="dashboard"></a>대시보드

아래와 같이 Azure Portal HDInsight 개요의 **클러스터 대시보드** 섹션에서 **Ambari home** 링크를 선택 하 여 Ambari 대시보드에 액세스할 수 있습니다. 또는 브라우저에서로 이동 하 여 액세스할 수 있습니다 `https://CLUSTERNAME.azurehdinsight.net` . 여기서 CLUSTERNAME은 클러스터의 이름입니다.

![HDInsight 리소스 포털 보기](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

그러면 클러스터 로그인 사용자 이름 및 암호를 입력 하 라는 메시지가 표시 됩니다. 클러스터를 만들 때 선택한 자격 증명을 입력합니다.

그런 다음 HDInsight 클러스터의 상태에 대 한 간략 한 개요를 제공 하는 몇 가지 메트릭을 보여 주는 위젯을 포함 하는 Ambari 대시보드로 이동 합니다. 이러한 위젯을 통해 live DataNodes (작업자 노드) 수, JournalNodes (사육 아웃 노드), Namenode (헤드 노드) 작동 시간 등의 메트릭과 특정 클러스터 유형과 관련 된 메트릭 (예: Spark 및 Hadoop 클러스터의 경우 YARN ResourceManager 작동 시간)을 보여 줍니다.

![Apache Ambari use 대시보드 표시](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>호스트 – 개별 노드 상태 보기

또한 개별 노드에 대 한 상태 정보를 볼 수 있습니다. **호스트** 탭을 선택 하 여 클러스터의 모든 노드 목록을 확인 하 고 각 노드에 대 한 기본 정보를 확인 합니다. 각 노드 이름의 왼쪽에 있는 녹색 확인은 모든 구성 요소가 노드에 있는지를 나타냅니다. 노드에서 구성 요소가 다운 된 경우 녹색 확인 대신 빨간색 경고 삼각형이 표시 됩니다.

![HDInsight Apache Ambari 호스트 뷰](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

그런 다음 노드 **이름을** 선택 하 여 해당 특정 노드에 대 한 자세한 호스트 메트릭을 볼 수 있습니다. 이 보기에는 각 개별 구성 요소의 상태/가용성이 표시 됩니다.

![Apache Ambari는 단일 노드 뷰를 호스팅합니다.](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Ambari 경고

또한 Ambari는 특정 이벤트에 대 한 알림을 제공할 수 있는 몇 가지 구성 가능한 경고를 제공 합니다. 경고가 트리거되면 경고 수가 포함 된 빨간색 배지에서 Ambari의 왼쪽 위 모서리에 표시 됩니다. 이 배지를 선택 하면 현재 경고 목록이 표시 됩니다.

![Apache Ambari 현재 경고 수](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

경고 정의 및 해당 상태 목록을 보려면 아래와 같이 **경고** 탭을 선택 합니다.

![Ambari 경고 정의 보기](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari는 다음을 포함 하 여 가용성과 관련 된 미리 정의 된 많은 경고를 제공 합니다.

| 경고 이름                        | 설명   |
|---|---|
| DataNode 상태 요약           | 비정상 DataNodes 있는 경우이 서비스 수준 경고가 트리거됩니다.|
| NameNode 고가용성 상태 | 활성 NameNode 또는 Standby NameNode가 실행 되 고 있지 않으면이 서비스 수준 경고가 트리거됩니다.|
| 사용 가능한 JournalNodes 백분율    | 이 경고는 클러스터의 down JournalNodes 수가 구성 된 위험 임계값을 초과 하는 경우에 트리거됩니다. JournalNode 프로세스 검사의 결과를 집계 합니다. |
| 사용 가능한 DataNodes 백분율       | 이 경고는 클러스터의 down DataNodes 수가 구성 된 위험 임계값을 초과 하는 경우에 트리거됩니다. DataNode 프로세스 검사의 결과를 집계 합니다.|

클러스터의 가용성을 모니터링 하는 데 도움이 되는 Ambari 경고의 전체 목록은 [여기](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)에서 찾을 수 있습니다.

경고에 대 한 세부 정보를 보거나 조건을 수정 하려면 경고의 **이름을** 선택 합니다. **DataNode Health Summary** 를 예로 들어 보겠습니다. 경고에 대 한 설명 뿐만 아니라 ' 경고 ' 또는 ' 위험 ' 경고를 트리거하는 특정 조건 및 조건에 대 한 확인 간격을 볼 수 있습니다. 구성을 편집 하려면 구성 상자의 오른쪽 위 모퉁이에 있는 **편집** 단추를 선택 합니다.

![Apache Ambari 경고 구성](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

여기에서 설명을 편집할 수 있으며, 더 중요 한 것은 경고 또는 중요 한 알림에 대 한 확인 간격과 임계값을 편집할 수 있습니다.

![Ambari 경고 구성 편집 뷰](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

이 예에서는 두 비정상 DataNodes 트리거를 중요 한 경고로 설정 하 고, 1 비정상 DataNode 경고를 트리거할 수 있습니다. 편집이 완료 되 면 **저장** 을 선택 합니다.

## <a name="email-notifications"></a>이메일 알림

필요에 따라 Ambari 경고에 대 한 전자 메일 알림을 구성할 수도 있습니다. 이렇게 하려면 **경고** 탭에서 왼쪽 위에 있는 **작업** 단추를 클릭 한 다음 **알림 관리** 를 클릭 합니다.

![Ambari 알림 관리 작업](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

경고 알림을 관리 하는 대화 상자가 열립니다. **+** 대화 상자의 맨 아래에서를 선택 하 고 필수 필드를 입력 하 여 전자 메일을 보낼 전자 메일 서버 세부 정보를 Ambari에 제공 합니다.

> [!TIP]
> Ambari 전자 메일 알림을 설정 하는 것은 많은 HDInsight 클러스터를 관리할 때 한 곳에서 경고를 수신 하는 좋은 방법일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)
- [클러스터 가용성 - Azure Monitor 로그](./cluster-availability-monitor-logs.md)
- [Azure Monitor 로그 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Apache Ambari 이메일 알림](apache-ambari-email.md)
