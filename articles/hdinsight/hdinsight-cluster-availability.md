---
title: Azure HDInsight에서 Apache Ambari를 사용하여 클러스터 가용성을 모니터링하는 방법
description: Apache Ambari를 사용하여 클러스터 상태 및 가용성을 모니터링하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: dcbe44defeb89ad2f67833b263e5f4983070a46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863533"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Ambari를 사용하여 클러스터 가용성을 모니터링하는 방법

HDInsight 클러스터에는 상태 정보 개요와 미리 정의된 경고를 제공하는 Apache Ambari가 포함되어 있습니다.

이 문서에서는 Ambari를 사용하여 클러스터를 모니터링하는 방법을 보여 주고 Ambari 경고를 구성하고, 노드 가용성 비율을 모니터링하고, 5시간 안에 하나 이상의 노드에서 하트비트를 수신하지 못한 경우에 발생하는 Azure Monitor 경고를 만드는 몇 가지 예를 살펴봅니다.

## <a name="dashboard"></a>대시보드

아래와 같이 Azure Portal에서 HDInsight 개요의 **클러스터 대시보드** 섹션에서 **Ambari 홈** 링크를 선택하여 Ambari 대시보드에 액세스할 수 있습니다. 또는 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`으로 이동하여 액세스할 수 있습니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.

:::image type="content" source="media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png" alt-text="HDInsight 리소스 포털 뷰":::

그러면 클러스터 로그인 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다. 클러스터를 만들 때 선택한 자격 증명을 입력합니다.

그런 다음 HDInsight 클러스터의 상태에 대한 간략한 개요를 제공하는 몇 가지 메트릭을 보여 주는 위젯을 포함하는 Ambari 대시보드로 이동합니다. 해당 위젯은 라이브 DataNodes(작업자 노드) 및 JournalNodes(ZooKeeper 노드) 수, Namenode(헤드 노드) 작동 시간 등과 같은 메트릭과 Spark 및 Hadoop 클러스터의 YARN ResourceManager 작동 시간과 같이 특정 클러스터 유형과 관련된 메트릭을 보여 줍니다.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-dashboard.png" alt-text="Apache Ambari 사용자 대시보드 디스플레이":::

## <a name="hosts--view-individual-node-status"></a>호스트 – 개별 노드 상태 보기

또한 개별 노드에 대한 상태 정보를 볼 수 있습니다. **호스트** 탭을 선택하여 클러스터의 모든 노드 목록을 확인하고 각 노드에 대한 기본 정보를 확인합니다. 각 노드 이름의 왼쪽에 있는 녹색 확인 표시는 모든 구성 요소가 노드에 있음을 나타냅니다. 노드에서 구성 요소가 다운된 경우 녹색 확인 표시 대신 빨간색 경고 삼각형이 표시됩니다.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts1.png" alt-text="HDInsight Apache Ambari 호스트 뷰":::

그런 다음 노드의 **이름** 을 선택하여 해당 특정 노드에 대한 자세한 호스트 메트릭을 확인할 수 있습니다. 이 뷰에는 각 개별 구성 요소의 상태/가용성이 표시됩니다.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts-node.png" alt-text="Apache Ambari 호스트 단일 노드 뷰":::

## <a name="ambari-alerts"></a>Ambari 경고

또한 Ambari는 특정 이벤트에 대한 알림을 제공할 수 있는 몇 가지 구성 가능한 경고를 제공합니다. 경고가 트리거되면 Ambari의 왼쪽 상단에 경고 수가 포함된 빨간색 배지에 표시됩니다. 이 배지를 선택하면 현재 경고 목록이 표시됩니다.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-alerts.png" alt-text="Apache Ambari 현재 경고 수":::

경고 정의 및 해당 상태의 목록을 보려면 아래와 같이 **경고** 탭을 선택합니다.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alerts-definitions.png" alt-text="Ambari 경고 정의 뷰":::

Ambari는 다음을 포함하여 가용성과 관련된 미리 정의된 많은 경고를 제공합니다.

| 경고 이름                        | Description   |
|---|---|
| DataNode 상태 요약           | 이 서비스 수준 경고는 비정상 DataNodes가 있는 경우 트리거됩니다.|
| NameNode 고가용성 상태 | 이 서비스 수준 경고는 Active NameNode 또는 Standby NameNode가 실행되고 있지 않은 경우 트리거됩니다.|
| 사용 가능한 JournalNodes 백분율    | 이 경고는 클러스터에서 다운된 JournalNodes 수가 구성된 위험 임계값을 초과하는 경우 트리거됩니다. JournalNode 프로세스 검사 결과를 집계합니다. |
| 사용 가능한 DataNodes 백분율       | 이 경고는 클러스터에서 다운된 DataNodes 수가 구성된 위험 임계값을 초과하는 경우에 트리거됩니다. DataNode 프로세스 검사 결과를 집계합니다.|


경고에 대한 세부 정보를 보거나 기준을 수정하려면 경고의 **이름** 을 선택합니다. **DataNode 상태 요약** 을 예로 들어 보겠습니다. 경고에 대한 설명뿐만 아니라 ‘경고’ 또는 ‘위험 경고’를 트리거하는 특정 기준과 기준 검사 간격을 볼 수 있습니다. 구성을 수정하려면 구성 상자의 오른쪽 상단에 있는 **편집** 단추를 선택합니다.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration.png" alt-text="Apache Ambari 경고 구성":::

여기에서 설명을 편집할 수 있으며, 무엇보다도 경고 또는 위험 경고에 대한 검사 간격과 임계값을 편집할 수 있습니다.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png" alt-text="Ambari 경고 구성 편집 뷰":::

이 예에서는 2개의 비정상 DataNodes가 중요한 알림을 트리거하고 1개의 비정상 DataNode가 경고만 트리거하도록 설정할 수 있습니다. 편집이 완료되면 **저장** 을 선택합니다.

## <a name="email-notifications"></a>메일 알림

필요에 따라 Ambari 경고에 대한 메일 알림을 구성할 수도 있습니다. 이렇게 하려면 **경고** 탭에서 왼쪽 상단에 있는 **작업** 단추를 클릭한 다음 **알림 관리** 를 클릭합니다.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-manage-notifications.png" alt-text="Ambari 알림 관리 작업":::

경고 알림을 관리하는 대화 상자가 열립니다. 대화 상자의 하단에서 **+** 를 선택하고 필수 필드를 입력하여 메일을 보낼 메일 서버 세부 정보를 Ambari에 제공합니다.

> [!TIP]
> Ambari 메일 알림을 설정하는 것은 많은 HDInsight 클러스터를 관리할 때 한 곳에서 경고를 수신하는 좋은 방법일 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](./hdinsight-business-continuity.md)
- [클러스터 가용성 - Azure Monitor 로그](./cluster-availability-monitor-logs.md)
- [Azure Monitor 로그 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Apache Ambari 이메일 알림](apache-ambari-email.md)
