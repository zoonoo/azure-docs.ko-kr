---
title: '모니터링: Apache Ambari & Azure Monitor logs-Azure HDInsight'
description: Ambari 및 Azure Monitor 로그를 사용 하 여 클러스터 상태 및 가용성을 모니터링 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060180"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Apache Ambari 및 Azure Monitor 로그를 사용 하 여 클러스터 가용성을 모니터링 하는 방법

HDInsight 클러스터에는 상태 정보를 한눈에 볼 수 있는 Apache Ambari와 미리 정의 된 경고 뿐만 아니라 쿼리 가능한 메트릭과 로그를 제공 하는 구성 가능한 경고를 제공 하는 Azure Monitor 로그 통합이 모두 포함 되어 있습니다.

이 문서에서는 이러한 도구를 사용 하 여 클러스터를 모니터링 하 고, Ambari 경고를 구성 하 고, 노드 가용성을 모니터링 하며, 하나 이상의 노드에서 5 시간 안에 하트 비트를 받지 못한 경우 발생 하는 Azure Monitor 경고를 만드는 방법을 보여 줍니다.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>대시보드

아래와 같이 Azure Portal HDInsight 개요의 **클러스터 대시보드** 섹션에서 **Ambari home** 링크를 선택 하 여 Ambari 대시보드에 액세스할 수 있습니다. 또는 브라우저에서로 `https://CLUSTERNAME.azurehdinsight.net` 이동 하 여 액세스할 수 있습니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.

![HDInsight 리소스 포털 보기](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

그러면 클러스터 로그인 사용자 이름 및 암호를 입력 하 라는 메시지가 표시 됩니다. 클러스터를 만들 때 선택한 자격 증명을 입력합니다.

그런 다음 HDInsight 클러스터의 상태에 대 한 간략 한 개요를 제공 하는 몇 가지 메트릭을 보여 주는 위젯을 포함 하는 Ambari 대시보드로 이동 합니다. 이러한 위젯을 통해 live DataNodes (작업자 노드) 수, JournalNodes (사육 아웃 노드), Namenode (헤드 노드) 작동 시간 등의 메트릭과 특정 클러스터 유형과 관련 된 메트릭 (예: Spark 및 Hadoop 클러스터의 경우 YARN ResourceManager 작동 시간)을 보여 줍니다.

![Apache Ambari use 대시보드 표시](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>호스트 – 개별 노드 상태 보기

또한 개별 노드에 대 한 상태 정보를 볼 수 있습니다. **호스트** 탭을 선택 하 여 클러스터의 모든 노드 목록을 확인 하 고 각 노드에 대 한 기본 정보를 확인 합니다. 각 노드 이름의 왼쪽에 있는 녹색 확인은 모든 구성 요소가 노드에 있는지를 나타냅니다. 노드에서 구성 요소가 다운 된 경우 녹색 확인 대신 빨간색 경고 삼각형이 표시 됩니다.

![HDInsight Apache Ambari 호스트 뷰](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

그런 다음 노드 **이름을** 선택 하 여 해당 특정 노드에 대 한 자세한 호스트 메트릭을 볼 수 있습니다. 이 보기에는 각 개별 구성 요소의 상태/가용성이 표시 됩니다.

![Apache Ambari는 단일 노드 뷰를 호스팅합니다.](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari 경고

또한 Ambari는 특정 이벤트에 대 한 알림을 제공할 수 있는 몇 가지 구성 가능한 경고를 제공 합니다. 경고가 트리거되면 경고 수가 포함 된 빨간색 배지에서 Ambari의 왼쪽 위 모서리에 표시 됩니다. 이 배지를 선택 하면 현재 경고 목록이 표시 됩니다.

![Apache Ambari 현재 경고 수](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

경고 정의 및 해당 상태 목록을 보려면 아래와 같이 **경고** 탭을 선택 합니다.

![Ambari 경고 정의 보기](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari는 다음을 포함 하 여 가용성과 관련 된 미리 정의 된 많은 경고를 제공 합니다.

| 경고 이름                        | Description   |
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

### <a name="email-notifications"></a>이메일 알림

필요에 따라 Ambari 경고에 대 한 전자 메일 알림을 구성할 수도 있습니다. 이렇게 하려면 **경고** 탭에서 왼쪽 위에 있는 **작업** 단추를 클릭 한 다음 **알림 관리** 를 클릭 합니다.

![Ambari 알림 관리 작업](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

경고 알림을 관리 하는 대화 상자가 열립니다. 대화 상자의 **+** 맨 아래에서를 선택 하 고 필수 필드를 입력 하 여 전자 메일을 보낼 전자 메일 서버 세부 정보를 Ambari에 제공 합니다.

> [!TIP]
> Ambari 전자 메일 알림을 설정 하는 것은 많은 HDInsight 클러스터를 관리할 때 한 곳에서 경고를 수신 하는 좋은 방법일 수 있습니다.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 로그 통합

Azure Monitor 로그를 사용 하면 HDInsight 클러스터와 같은 여러 리소스에 의해 생성 된 데이터를 한 곳에서 수집 하 고 집계 하 여 통합 모니터링 환경을 구현할 수 있습니다.

필수 구성 요소로, 수집 된 데이터를 저장 하려면 Log Analytics 작업 영역이 필요 합니다. 아직 만들지 않은 경우 다음 지침 [에 따라 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)를 수행할 수 있습니다.

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure Monitor 로그 통합 사용

포털의 HDInsight 클러스터 리소스 페이지에서 **Azure Monitor**를 선택 합니다. 그런 다음 **사용** 을 선택 하 고 드롭다운에서 Log Analytics 작업 영역을 선택 합니다.

![HDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>메트릭 및 로그 테이블 쿼리

Azure Monitor 로그 통합이 사용 하도록 설정 되 면 (몇 분 정도 걸릴 수 있음) **Log Analytics 작업 영역** 리소스로 이동 하 여 **로그**를 선택 합니다.

![작업 영역 로그 Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

로그는 다음과 같은 다양 한 샘플 쿼리를 나열 합니다.

| 쿼리 이름                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 현재 사용 가능한 컴퓨터    | 매 시간 마다 로그를 전송 하는 컴퓨터 수를 차트로 표시 합니다.                     |
| 하트 비트 나열                 | 지난 1 시간 동안 모든 컴퓨터 하트 비트 나열                           |
| 각 컴퓨터의 마지막 하트 비트 | 각 컴퓨터에서 보낸 마지막 하트 비트 표시                             |
| 사용할 수 없는 컴퓨터           | 지난 5 시간 동안 하트 비트를 보내지 않은 모든 알려진 컴퓨터를 나열 합니다. |
| 가용성 률               | 연결 된 각 컴퓨터의 가용성 률 계산                |

예를 들어 위의 스크린샷에 표시 된 것 처럼 해당 쿼리에서 **실행** 을 선택 하 여 **Availability rate** 샘플 쿼리를 실행 합니다. 이렇게 하면 클러스터의 각 노드에 대 한 가용성 비율이 백분율로 표시 됩니다. 동일한 Log Analytics 작업 영역에 메트릭을 전송 하기 위해 여러 HDInsight 클러스터를 사용 하도록 설정한 경우 해당 클러스터의 모든 노드에 대 한 가용성 비율이 표시 됩니다.

![작업 영역 로그의 ' availability rate ' 샘플 쿼리 Log Analytics](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> 가용성 속도는 24 시간 동안 측정 되므로 정확한 가용성 요금은 표시 되기까지 최소 24 시간 동안 클러스터를 실행 해야 합니다.

오른쪽 위 모서리에서 **고정** 을 클릭 하 여이 테이블을 공유 대시보드에 고정할 수 있습니다. 쓰기 가능한 공유 대시보드가 없으면 [Azure Portal에서 대시보드를 만들고 공유](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)하는 방법을 확인할 수 있습니다.

### <a name="azure-monitor-alerts"></a>Azure Monitor 경고

메트릭 값 또는 쿼리 결과가 특정 조건에 맞을 때 트리거되는 Azure Monitor 경고를 설정할 수도 있습니다. 예를 들어 하나 이상의 노드가 5 시간 안에 하트 비트를 보내지 않은 경우 (즉, 사용할 수 없는 것으로 간주 됨) 전자 메일을 보내는 경고를 만들어 보겠습니다.

**로그**에서 아래와 같이 해당 쿼리에서 **실행** 을 선택 하 여 **사용할 수 없는 computers** 샘플 쿼리를 실행 합니다.

![작업 영역 로그의 ' 사용할 수 없는 컴퓨터 ' 샘플 Log Analytics](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

모든 노드를 사용할 수 있는 경우이 쿼리는 지금은 0 개의 결과를 반환 해야 합니다. **새 경고 규칙** 을 클릭 하 여이 쿼리에 대 한 경고 구성을 시작 합니다.

![Log Analytics 작업 영역 새 경고 규칙](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

경고에는 규칙을 만들 *리소스* (이 경우 Log Analytics 작업 영역), 경고를 트리거하는 *조건* 및 경고가 트리거될 때 발생 하는 동작을 결정 하는 *동작 그룹* 의 세 가지 구성 요소가 있습니다.
아래와 같이 **조건 제목을**클릭 하 여 신호 논리 구성을 마칩니다.

![포털 경고 규칙 만들기 조건](media/hdinsight-cluster-availability/portal-condition-title.png)

그러면 **신호 논리 구성**이 열립니다.

**경고 논리** 섹션을 다음과 같이 설정 합니다.

*기준: 결과 수, 조건: 보다 큼, 임계값: 0.*

이 쿼리는 사용할 수 없는 노드만 결과로 반환 하므로 결과 수가 0 보다 크면 경고가 발생 합니다.

**평가 기준** 섹션에서 사용할 수 없는 노드를 확인 하려는 빈도에 따라 **기간** 및 **빈도** 를 설정 합니다.

이 경고의 목적을 위해 **Period = Frequency** 를 확인 하는 것이 좋습니다. 기간, 빈도 및 기타 경고 매개 변수에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)에서 찾을 수 있습니다.

신호 논리 구성이 완료 되 면 **완료** 를 선택 합니다.

![경고 규칙이 신호 논리를 구성 합니다.](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

기존 작업 그룹이 아직 없는 경우 **작업 그룹** 섹션에서 **새로 만들기** 를 클릭 합니다.

![경고 규칙이 새 작업 그룹을 만듭니다.](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

그러면 **작업 그룹 추가**가 열립니다. **작업 그룹 이름**, **약식 이름**, **구독**및 **리소스 그룹을 선택 합니다.** **작업 섹션에서** 작업 **이름을** 선택 하 고 **전자 메일/SMS/푸시/음성** 을 **작업 유형으로 선택 합니다.**

> [!NOTE]
> Azure Function, LogicApp, Webhook, ITSM 및 Automation Runbook과 같은 전자 메일/a p i/푸시/음성 외에 경고에서 트리거할 수 있는 다른 작업은 여러 가지가 있습니다. [더 알아보세요.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

그러면 **이메일/SMS/푸시/음성이**열립니다. 받는 사람에 대 한 **이름을** 선택 하 고 **전자 메일** 상자 **를 선택 하** 고 경고를 보낼 전자 메일 주소를 입력 합니다. **이메일/SMS/푸시/음성**에서 **확인** 을 선택한 다음 **작업 그룹 추가** 에서 작업 그룹 구성을 완료 합니다.

![경고 규칙은 추가 작업 그룹을 만듭니다.](media/hdinsight-cluster-availability/portal-add-action-group.png)

이러한 블레이드가 닫힌 **후 작업 그룹 섹션에** 나열 된 작업 그룹을 확인 해야 합니다. 마지막으로 **경고 규칙 이름과** **설명을** 입력 하 고 **심각도**를 선택 하 여 **경고 세부 정보** 섹션을 완료 합니다. **경고 규칙 만들기** 를 클릭 하 여 완료 합니다.

![포털에서 경고 규칙 만들기 완료](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> **심각도** 를 지정 하는 기능은 여러 경고를 만들 때 사용할 수 있는 강력한 도구입니다. 예를 들어 단일 헤드 노드가 다운 되는 경우 경고를 생성 하는 경고 (심각도 1)를 생성 하 고, 두 헤드 노드가 모두 다운 될 가능성이 없는 이벤트에서 위험 (심각도 0)을 발생 시키는 다른 경고를 만들 수 있습니다.

이 경고에 대 한 조건이 충족 되 면 경고가 발생 하 고 다음과 같은 경고 세부 정보가 포함 된 전자 메일을 받게 됩니다.

![Azure Monitor 경고 전자 메일 예제](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

**Log Analytics 작업 영역**에서 **경고** 로 이동 하 여 발생 한 모든 경고를 심각도 별로 그룹화 하 여 볼 수도 있습니다.

![Log Analytics 작업 영역 경고](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

심각도 그룹화 (위에서 강조 표시 된 대로 **심각도 1** )를 선택 하면 아래와 같이 발생 한 심각도의 모든 경고에 대 한 레코드가 표시 됩니다.

![Log Analytics 작업 영역 심각도 1 개 경고](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>다음 단계

- [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)
