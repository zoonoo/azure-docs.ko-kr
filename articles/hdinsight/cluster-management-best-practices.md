---
title: 클러스터 관리 모범 사례 - Azure HDInsight
description: HDInsight 클러스터 관리를 위한 모범 사례를 알아보십시오.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782022"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight 클러스터 관리 모범 사례

HDInsight 클러스터 관리를 위한 모범 사례를 알아보십시오.

## <a name="how-do-i-create-hdinsight-clusters"></a>HDInsight 클러스터를 만들려면 어떻게 해야 합니까?

| 옵션 | 문서 |
|---|---|
| Azure 데이터 팩터리 | [Azure Data Factory를 사용하여 HDInsight에서 주문형 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| 사용자 지정 리소스 관리자 템플릿 | [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| 빠른 시작 템플릿 | [HDInsight 퀵스타트 템플릿](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure 샘플 | [HDInsight Azure 샘플](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Azure portal | [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Azure CLI를 사용하여 HDInsight 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Azure PowerShell을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Azure REST API를 사용하여 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK (.NET, 파이썬, 자바) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [파이썬,](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python) [자바](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [이동](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> 클러스터를 만들고 이전에 만든 클러스터에서 클러스터 이름을 다시 사용하는 경우 클러스터를 만들기 전에 이전 클러스터 삭제가 완료될 때까지 기다립니다.

## <a name="how-do-i-customize-hdinsight-clusters"></a>HDInsight 클러스터를 사용자 지정하려면 어떻게 해야 합니까?

| 옵션 | 문서 |
|---|---|
| 스크립트 작업 | [스크립트 작업을 사용하여 Azure HDInsight 클러스터 사용자 지정](./hdinsight-hadoop-customize-cluster-linux.md) |
| 부트스트랩 | [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| 외부 Metastore | [Azure HDInsight에서 외부 메타데이터 저장소 사용](./hdinsight-use-external-metadata-stores.md) |
| 사용자 지정 Ambari DB | [사용자 지정 Ambari DB로 HDInsight 클러스터 설정](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>클러스터를 만들 때 발생할 수 있는 오류는 무엇입니까?

| Error | 자세한 정보 |
|---|---|
| 할당량 없음 | 각 리전에서 구독에서 만들 수 있는 할당량 수에 대한 할당량이 있습니다. 자세한 내용은 [용량 계획: 할당량을](./hdinsight-capacity-planning.md)참조하십시오. |
| 더 이상 사용할 수 있는 IP 주소가 없습니다. | 각 VNet에는 제한된 수의 IP 주소가 있습니다. HDInsight 클러스터를 만들 때 각 노드(사육사 및 게이트웨이 노드 포함)는 이러한 할당된 IP 주소 중 일부를 사용합니다. 모든 IP 주소가 사용 중이면 이 오류가 발생합니다.  |
| NSG(네트워크 보안 그룹) 규칙은 HDInsight 리소스 공급자와의 통신을 허용하지 않습니다. | NSG 또는 사용자 정의 경로(DR)를 사용하여 HDInsight 클러스터에 대한 인바운드 트래픽을 제어하는 경우 클러스터가 중요한 Azure 상태 및 관리 서비스와 통신할 수 있는지 확인해야 합니다. 자세한 내용은 [Azure HDInsight에 대한 NSG(네트워크 보안 그룹) 서비스 태그를](./hdinsight-service-tags.md) 참조하십시오. |
| 클러스터 이름 재사용 | 이전에 사용한 클러스터 이름을 사용하는 경우 클러스터를 다시 만들기 전에 X 분의 수를 기다려야 합니다. 그렇지 않으면 리소스가 이미 존재한다는 메시지가 표시됩니다. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>HDInsight 클러스터 실행 관리를 관리하려면 어떻게 해야 합니까?

| 옵션 | 문서 |
|---|---|
| 자동 크기 조정 | [Azure HDInsight 클러스터 자동 크기 조정](./hdinsight-autoscale-clusters.md) |
| 수동 크기 조정 | [Azure HDInsight 클러스터 확장](./hdinsight-scaling-best-practices.md) |
| 암바리로 모니터링| [Azure HDInsight에서 클러스터 성능 모니터링](./hdinsight-key-scenarios-to-monitor.md) |
| Azure 모니터 로그를 통해 모니터링 | [Azure Monitor Logs를 사용하여 HDInsight 클러스터 모니터링](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>삭제된 HDInsight 클러스터를 확인하려면 어떻게 해야 합니까?

### <a name="azure-monitor-logs"></a>Azure Monitor 로그

Azure Monitor 로그와 함께 다음 쿼리를 사용하여 삭제된 클러스터를 모니터링할 수 있습니다.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>다음 단계

* [HDInsight 클러스터의 용량 계획](./hdinsight-capacity-planning.md)
* [Azure HDInsight의 기본 및 권장 노드 구성은 무엇입니까?](./hdinsight-supported-node-configuration.md)