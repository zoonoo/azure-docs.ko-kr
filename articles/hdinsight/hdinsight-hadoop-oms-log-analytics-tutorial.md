---
title: Log Analytics를 사용하여 Azure HDInsight 클러스터 모니터링
description: Azure Log Analytics를 사용하여 HDInsight 클러스터에서 실행되는 작업을 모니터링하는 방법을 알아봅니다.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 1a526bfdd951e4dc2897ec52c03bfbe96ee14d4c
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403681"
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Azure Log Analytics를 사용하여 HDInsight 클러스터 모니터링

HDInsight에서 Hadoop 클러스터 작업을 모니터링하기 위해 Azure Log Analytics를 사용하도록 설정하는 방법과 HDInisght 모니터링 솔루션을 추가하는 방법에 대해 알아봅니다.

[Log Analytics](../log-analytics/log-analytics-overview.md)는 클라우드 및 온-프레미스 환경을 모니터링하여 해당 가용성 및 성능을 유지하는 서비스입니다. 이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **Log Analytics 작업 영역** - 이 작업 영역은 자체의 데이터 리포지토리, 데이터 원본 및 솔루션을 포함한 고유한 Log Analytics 환경으로 생각할 수 있습니다. 지침은 [Log Analytics 작업 영역 만들기](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)를 참조하세요.

* **Azure HDInsight 클러스터**. Log Analytics는 현재 다음 HDInsight 클러스터 유형에서 사용할 수 있습니다.

  * Hadoop은
  * HBase
  * 대화형 쿼리
  * Kafka
  * Spark
  * Storm

  HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

> [!NOTE]
> 성능 향상을 위해 동일한 지역에 HDInsight 클러스터와 Log Analytics 작업 영역을 모두 배치하는 것이 좋습니다. Azure Log Analytics를 모든 Azure 지역에서 사용할 수 있는 것은 아닙니다.

## <a name="enable-log-analytics-by-using-the-portal"></a>포털을 사용하여 Log Analytics 사용

이 섹션에서는 Azure Log Analytics 작업 영역을 사용하여 작업, 디버그 로그 등을 모니터링하도록 기존 HDInsight Hadoop 클러스터를 구성합니다.

1. Azure Portal에서 HDInsight 클러스터를 엽니다.
2. 왼쪽 창에서 **모니터링**을 선택합니다.
3. 오른쪽 창에서 **사용**을 선택하고, 기존 Log Analytics 작업 영역을 선택한 다음, **저장**을 선택합니다.

    ![HDInsight 클러스터에 대한 모니터링 사용](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight 클러스터에 대한 모니터링 사용")

    설정을 저장하는 데 몇 분 정도 걸립니다.

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 Log Analytics 사용

Azure PowerShell을 사용하여 Log Analytics를 사용할 수 있습니다. cmdlet는 다음과 같습니다.

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
      [-Name] <CLUSTER NAME>
      [-WorkspaceId] <LOG ANALYTICS WORKSPACE NAME>
      [-PrimaryKey] <LOG ANALYTICS WORKSPACE PRIMARY KEY>
      [-ResourceGroupName] <RESOURCE GROUIP NAME>
```

[Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0)를 참조하세요.

사용하지 않으려면 cmdlet은 다음과 같습니다.

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
       [-Name] <CLUSTER NAME>
       [-ResourceGroupName] <RESOURCE GROUP NAME>
```

[Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0)를 참조하세요.

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight 클러스터 관리 솔루션 설치

HDInsight는 Azure Log Analytics에 추가할 수 있는 클러스터와 관련된 관리 솔루션을 제공합니다. [관리 솔루션](../log-analytics/log-analytics-add-solutions.md)은 Log Analytics에 기능을 추가하여 추가 데이터 및 분석 도구를 제공합니다. 이러한 솔루션은 HDInsight 클러스터에서 중요한 성능 메트릭을 수집하고, 메트릭을 검색할 수 있는 도구를 제공합니다. 또한 HDInsight에서 지원되는 대부분의 클러스터 유형에 대해 시각화 및 대시보드를 제공합니다. 솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다.

다음은 사용 가능한 HDInsight 솔루션입니다.

* HDInsight Hadoop 모니터링
* HDInsight HBase 모니터링
* HDInsight 대화형 쿼리 모니터링
* HDInsight Kafka 모니터링
* HDInsight Spark 모니터링
* HDInsight Storm 모니터링

관리 솔루션을 설치하기 위한 지침은 [Azure의 관리 솔루션](../monitoring/monitoring-solutions.md#install-a-management-solution)을 참조하세요. 실험하려면 HDInsight Hadoop 모니터링 솔루션을 설치합니다. 완료되면 **요약**에 **HDInsightHadoop** 타일이 나열됩니다. **HDInsightHadoop** 타일을 선택합니다. HDInsightHadoop 솔루션은 다음과 같습니다.

![HDInsight 모니터링 솔루션 보기](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

클러스터는 새로운 클러스터이므로 보고서에 작업이 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Log Analytics를 쿼리하여 HDInsight 클러스터 모니터링](hdinsight-hadoop-oms-log-analytics-use-queries.md)
