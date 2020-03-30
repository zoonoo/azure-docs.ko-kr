---
title: '모니터링: 아파치 암바리 & Azure 모니터 로그 - Azure HDInsight'
description: Ambari 및 Azure 모니터 로그를 사용하여 클러스터 상태 및 가용성을 모니터링하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060180"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>아파치 암바리 및 Azure 모니터 로그를 사용하여 클러스터 가용성을 모니터링하는 방법

HDInsight 클러스터에는 한눈에 상태 정보를 제공하는 아파치 암바리(Apache Ambari)와 미리 정의된 경고를 제공하는 Apache Ambari뿐만 아니라 쿼리 가능한 메트릭 및 로그뿐만 아니라 구성 가능한 경고를 제공하는 Azure Monitor 로그 통합이 포함됩니다.

이 문서는 이러한 도구를 사용하여 클러스터를 모니터링하고 Ambari 경고를 구성하고 노드 가용성을 모니터링하며 하나 이상의 노드에서 하트비트가 수신되지 않은 경우 발생되는 Azure Monitor 경고를 만드는 몇 가지 예를 안내하는 방법을 보여 줍니다. 5시간 만에

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>대시보드

Ambari 대시보드는 아래와 같이 Azure 포털의 HDInsight 개요의 **클러스터 대시보드** 섹션에서 **Ambari 홈** 링크를 선택하여 액세스할 수 있습니다. 또는 CLUSTERNAME이 클러스터의 이름인 `https://CLUSTERNAME.azurehdinsight.net` 브라우저로 이동하여 액세스할 수 있습니다.

![HDInsight 리소스 포털 보기](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

그러면 클러스터 로그인 사용자 이름과 암호가 표시됩니다. 클러스터를 만들 때 선택한 자격 증명을 입력합니다.

그런 다음 HDInsight 클러스터의 상태를 간략하게 파악할 수 있는 몇 가지 메트릭을 표시하는 위젯이 포함된 Ambari 대시보드로 이동합니다. 이러한 위젯에는 라이브 DataNodes(작업자 노드) 및 JournalNodes(사육사 노드), NameNodes(헤드 노드) 가동 시간 등의 메트릭과 Spark 및 Hadoop 클러스터에 대한 YARN ResourceManager 가동 시간과 같은 특정 클러스터 유형에 특정메트릭이 표시됩니다.

![아파치 암바리 사용 대시보드 디스플레이](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>호스트 - 개별 노드 상태 보기

개별 노드의 상태 정보를 볼 수도 있습니다. **호스트** 탭을 선택하여 클러스터의 모든 노드 목록을 보고 각 노드에 대한 기본 정보를 확인합니다. 각 노드 이름 왼쪽에 있는 녹색 검사는 모든 구성 요소가 노드에 위업되어 있음을 나타냅니다. 구성 요소가 노드에 다운된 경우 녹색 검사 대신 빨간색 경고 삼각형이 표시됩니다.

![HDInsight 아파치 암바리 호스트 보기](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

그런 다음 노드 **이름을** 선택하여 특정 노드에 대한 자세한 호스트 메트릭을 볼 수 있습니다. 이 보기는 각 개별 구성 요소의 상태/가용성을 보여 주며

![아파치 암바리, 단일 노드 보기 호스팅](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>암바리 경고

Ambari는 또한 특정 이벤트에 대한 알림을 제공할 수 있는 몇 가지 구성 가능한 경고를 제공합니다. 경고가 트리거되면 경고 수가 포함된 빨간색 배지가 Ambari의 왼쪽 위 모서리에 표시됩니다. 이 배지를 선택하면 현재 경고 목록이 표시됩니다.

![아파치 암바리 현재 경고 카운트](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

경고 정의 및 해당 상태 목록을 보려면 아래와 같이 **경고 탭을** 선택합니다.

![암바리 경고 정의 보기](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari는 가용성과 관련된 미리 정의된 많은 경고를 제공합니다.

| 경고 이름                        | 설명   |
|---|---|
| 데이터노드 상태 요약           | 이 서비스 수준 경고는 비정상 데이터노드가 있는 경우 트리거됩니다.|
| 네임노드 고가용성 상태 | 활성 NameNode 또는 대기 NameNode가 실행되지 않는 경우 이 서비스 수준 경고가 트리거됩니다.|
| 백분율 저널노드 사용 가능    | 클러스터의 Down JournalNodes 수가 구성된 임계 임계값보다 큰 경우 이 경고가 트리거됩니다. 그것은 저널 노드 프로세스 검사의 결과를 집계합니다. |
| 백분율 데이터 노드 사용 가능       | 이 경고는 클러스터의 down DataNodes 수가 구성된 임계 임계값보다 큰 경우 트리거됩니다. DataNode 프로세스 검사 결과를 집계합니다.|

클러스터의 가용성을 모니터링하는 데 도움이 되는 Ambari 경고의 전체 목록은 [여기에서](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)확인할 수 있습니다.

경고에 대한 세부 정보를 보거나 조건을 수정하려면 경고 **이름을** 선택합니다. **데이터노드 상태 요약을** 예로 들어 보겠습니다. 경고에 대한 설명과 '경고' 또는 '중요' 경고를 트리거하는 특정 기준 및 기준에 대한 검사 간격을 볼 수 있습니다. 구성을 편집하려면 구성 상자의 오른쪽 위 모서리에 있는 **편집** 단추를 선택합니다.

![아파치 암바리 경고 구성](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

여기서는 설명을 편집하고 더 중요한 것은 경고 또는 중요 경고에 대한 확인 간격 및 임계값을 편집할 수 있습니다.

![암바리 경고 구성 편집 보기](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

이 예제에서는 2개의 비정상 DataNodes가 중요 경고를 트리거하고 1비정상 DataNode가 경고만 트리거되도록 할 수 있습니다. 편집이 완료되면 **저장을** 선택합니다.

### <a name="email-notifications"></a>이메일 알림

Ambari 경고에 대한 이메일 알림을 선택적으로 구성할 수도 있습니다. 이렇게 하려면 경고 탭에서 왼쪽 상단의 **작업** 단추를 클릭한 다음 알림 관리를 **클릭합니다.** **Manage Notifications.**

![Ambari 알림 작업 관리](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

경고 알림 관리를 위한 대화 상자가 열립니다. 대화 **+** 상자 의 맨 아래에 있는 필드를 선택하고 필요한 필드를 작성하여 이메일을 보낼 이메일 서버 세부 정보를 Ambari에 제공합니다.

> [!TIP]
> Ambari 이메일 알림을 설정하면 많은 HDInsight 클러스터를 관리할 때 한 곳에서 알림을 받을 수 있습니다.

## <a name="azure-monitor-logs-integration"></a>Azure 모니터 로그 통합

Azure Monitor 로그를 사용하면 HDInsight 클러스터와 같은 여러 리소스에서 생성된 데이터를 한 곳에서 수집하고 집계하여 통합된 모니터링 환경을 구현할 수 있습니다.

필수 구성 조건으로 수집된 데이터를 저장하려면 로그 분석 작업 영역이 필요합니다. 아직 만들지 않은 경우 여기에서 지침을 따르십시오. [Create a Log Analytics Workspace](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure 모니터 로그 통합 사용

포털의 HDInsight 클러스터 리소스 페이지에서 **Azure 모니터를**선택합니다. 그런 다음 **사용 옵션을** 선택하고 드롭다운에서 Log Analytics 작업 영역을 선택합니다.

![HDInsight 운영 관리 제품군](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>메트릭 및 로그 테이블 쿼리

Azure Monitor 로그 통합이 활성화되면(몇 분 정도 걸릴 수 있음) **로그 분석 작업 영역** 리소스로 이동하여 **로그를 선택합니다.**

![로그 분석 작업 영역 로그](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

로그에는 다음과 같은 여러 샘플 쿼리가 나열됩니다.

| 쿼리 이름                      | 설명                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 오늘 컴퓨터 가용성    | 매시간 로그를 보내는 컴퓨터 수 차트                     |
| 하트비트 목록                 | 지난 1시간의 모든 컴퓨터 하트비트 나열                           |
| 각 컴퓨터의 마지막 하트비트 | 각 컴퓨터에서 보낸 마지막 하트비트 표시                             |
| 사용할 수 없는 컴퓨터           | 지난 5시간 동안 하트비트를 보내지 않은 알려진 모든 컴퓨터 나열 |
| 가용성 요금               | 연결된 각 컴퓨터의 가용성 비율 계산                |

예를 들어 위의 스크린샷과 같이 해당 쿼리에서 **실행을** 선택하여 **가용성 비율** 샘플 쿼리를 실행합니다. 그러면 클러스터의 각 노드의 가용성 비율이 백분율로 표시됩니다. 여러 HDInsight 클러스터를 사용하여 동일한 Log Analytics 작업 영역으로 메트릭을 전송한 경우 표시되는 클러스터의 모든 노드에 대한 가용성 비율이 표시됩니다.

![로그 분석 작업 영역 로그 '가용성 속도' 샘플 쿼리](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> 가용성 비율은 24시간 동안 측정되므로 정확한 가용성을 보기 전에 클러스터를 최소 24시간 동안 실행해야 합니다.

오른쪽 상단 모서리에 **있는 Pin을** 클릭하여 이 테이블을 공유 대시보드에 고정할 수 있습니다. 쓰기 가능한 공유 대시보드가 없는 경우 여기에서 [대시보드 를 만들고 공유하는](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)방법을 확인할 수 있습니다.

### <a name="azure-monitor-alerts"></a>Azure 모니터 경고

메트릭 값 또는 쿼리 결과가 특정 조건을 충족할 때 트리거되는 Azure Monitor 경고를 설정할 수도 있습니다. 예를 들어 하나 이상의 노드가 5시간 내에 하트비트를 보내지 않은 경우(즉, 사용할 수 없는 것으로 추정되는) 전자 메일을 보낼 수 있는 경고를 만들어 보겠습니다.

Logs에서 아래와 같이 해당 쿼리에서 **실행을** 선택하여 **사용할 수 없는 컴퓨터** 샘플 쿼리를 **실행합니다.**

![로그 분석 작업 영역 로그 '사용할 수 없는 컴퓨터' 샘플](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

모든 노드를 사용할 수 있는 경우 이 쿼리는 현재 0개의 결과를 반환해야 합니다. **새 경고 규칙을** 클릭하여 이 쿼리에 대한 경고 구성을 시작합니다.

![로그 분석 작업 영역 새 경고 규칙](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

경고에는 규칙을 만들 리소스(이 경우 로그 분석 작업 영역), 경고를 트리거할 *조건* 및 경고가 트리거될 때 발생할 작업을 결정하는 *작업 그룹의* 세 가지 구성 요소가 있습니다. *resource*
아래와 같이 **조건 제목을**클릭하여 신호 논리 구성을 완료합니다.

![포털 경고 만들기 규칙 조건](media/hdinsight-cluster-availability/portal-condition-title.png)

그러면 **신호 논리 구성이**열립니다.

다음과 같이 **경고 논리** 섹션을 설정합니다.

*기준: 결과 수, 조건: 임계값보다 크며 임계값: 0.*

이 쿼리는 사용할 수 없는 노드만 결과로 반환되므로 결과 수가 0보다 크면 경고가 발생합니다.

**평가** 섹션에서 사용할 수 없는 노드를 확인할 빈도에 따라 **기간** 및 **빈도를** 설정합니다.

이 경고의 목적을 위해 **기간=빈도를** 확인해야 합니다. 기간, 빈도 및 기타 경고 매개 변수에 대한 자세한 내용은 [여기에서](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)확인할 수 있습니다.

신호 논리 구성이 완료되면 **완료를** 선택합니다.

![경고 규칙은 신호 논리를 구성합니다.](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

기존 작업 그룹이 아직 없는 경우 **작업 그룹** 아래에서 **새 만들기** 섹션을 클릭합니다.

![경고 규칙은 새 작업 그룹을 만듭니다.](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

그러면 **작업 그룹 추가가**열립니다. 작업 **그룹 이름,** **짧은 이름,** **구독**및 **리소스 그룹을 선택합니다.** **작업** 섹션에서 **작업 이름을** 선택하고 **전자 메일/SMS/푸시/음성을** **작업 유형으로 선택합니다.**

> [!NOTE]
> Azure 함수, LogicApp, Webhook, ITSM 및 자동화 Runbook과 같은 전자 메일/SMS/푸시/음성 외에 경고가 트리거할 수 있는 다른 여러 작업이 있습니다. [더 알아보세요.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

그러면 **이메일/SMS/푸시/음성이**열립니다. 받는 사람의 **이름을** 선택하고 **전자 메일** 확인란을 **선택하고** 경고를 보낼 이메일 주소를 입력합니다. **이메일/SMS/푸시/음성에서**확인을 선택한 다음 **작업 그룹 추가에서** **확인을** 선택하여 작업 그룹 구성을 완료합니다.

![경고 규칙으로 작업 그룹 추가](media/hdinsight-cluster-availability/portal-add-action-group.png)

이러한 블레이드를 닫으면 **작업** 그룹 섹션 아래에 작업 그룹이 나열됩니다. 마지막으로 경고 **규칙 이름** 및 **설명을** 입력하고 **심각도**를 선택하여 **경고 세부 정보** 섹션을 완료합니다. 경고 규칙 만들기를 클릭하여 **완료합니다.**

![포털은 경고 규칙 완료를 만듭니다.](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> **심각도를** 지정하는 기능은 여러 경고를 만들 때 사용할 수 있는 강력한 도구입니다. 예를 들어 단일 헤드 노드가 다운되면 경고(Sev 1)를 발생시키는 경고 하나와 두 헤드 노드가 모두 다운되는 드문 경우 Critical(Sev 0)을 발생시키는 또 다른 경고를 만들 수 있습니다.

이 경고에 대한 조건이 충족되면 경고가 발생하며 다음과 같은 경고 세부 정보가 있는 전자 메일이 전송됩니다.

![Azure 모니터 경고 전자 메일 예제](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

또한 **로그 분석 작업 영역의**경고로 이동하여 심각도별로 그룹화하여 발생한 모든 경고를 볼 수도 **있습니다.**

![로그 분석 작업 영역 경고](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

심각도 그룹화(예: 위에서 강조 표시된 대로 **Sev 1)를** 선택하면 아래와 같이 발생한 심각도의 모든 경고에 대한 레코드가 표시됩니다.

![로그 분석 작업 영역은 하나의 경고를 sev](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>다음 단계

- [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)
