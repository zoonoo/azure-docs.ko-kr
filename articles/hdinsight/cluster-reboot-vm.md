---
title: Azure HDInsight 클러스터에 대 한 Vm 다시 부팅
description: Azure HDInsight 클러스터에 대해 응답 하지 않는 Vm을 다시 부팅 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: 149a82526263f5e372db81b5a92a9ee90a2c76f3
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089975"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>HDInsight 클러스터에 대 한 Vm 다시 부팅

Azure HDInsight 클러스터는 가상 머신 (Vm) 그룹을 클러스터 노드로 포함 합니다. 장기 실행 클러스터의 경우 이러한 노드는 다양 한 이유로 응답 하지 않을 수 있습니다. 이 문서에서는 HDInsight 클러스터에서 응답 하지 않는 Vm을 다시 부팅 하는 방법을 설명 합니다.

## <a name="when-to-reboot"></a>다시 부팅 해야 하는 경우

> [!WARNING]
> 클러스터에서 Vm을 다시 부팅 하는 경우 노드를 사용할 수 없으며 노드의 서비스를 다시 시작 해야 합니다.

노드가 다시 부팅 되 면 클러스터가 비정상 상태가 되 고 작업이 느려지거나 실패할 수 있습니다. 활성 헤드 노드를 다시 부팅 하려고 하면 실행 중인 모든 작업이 중지 됩니다. 서비스가 실행 되 고 다시 실행 될 때까지 클러스터에 작업을 제출할 수 없습니다. 따라서 필요한 경우에만 Vm을 다시 부팅 해야 합니다. 다음과 같은 경우 Vm을 다시 부팅 하십시오.

- SSH를 사용 하 여 노드로 이동할 수 없지만 ping에 응답 합니다.
- 작업자 노드가 Ambari UI의 하트 비트 없이 다운 되었습니다.
- 임시 디스크가 노드에 꽉 찬 경우
- VM의 프로세스 테이블에는 프로세스가 완료 된 많은 항목이 있지만 "종료 된 상태"로 나열 됩니다.

> [!WARNING]
> **HBase** 및 **kafka** 클러스터에 대 한 vm을 다시 부팅 하는 경우 데이터가 손실 될 수 있으므로 주의 해야 합니다.

## <a name="use-powershell-to-reboot-vms"></a>PowerShell을 사용 하 여 Vm 다시 부팅

노드 다시 부팅 작업을 사용 하려면 노드 나열 및 노드 다시 시작에 두 단계가 필요 합니다.

1. 노드를 나열 합니다. [AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost)에서 클러스터 노드 목록을 가져올 수 있습니다.

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. 호스트를 다시 시작 합니다. 다시 부팅 하려는 노드의 이름을 가져온 후 [AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)를 사용 하 여 노드를 다시 시작 합니다.

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>REST API를 사용 하 여 Vm 다시 부팅

API 문서에서 **사용해 보기** 기능을 사용 하 여 HDInsight에 요청을 보낼 수 있습니다. 노드 다시 부팅 작업을 사용 하려면 노드 나열 및 노드 다시 시작에 두 단계가 필요 합니다.

1. 노드를 나열 합니다. REST API 또는 Ambari에서 클러스터 노드 목록을 가져올 수 있습니다. 자세한 내용은 [HDInsight 목록 호스트 REST API 작업](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)을 참조 하세요.

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. 호스트를 다시 시작 합니다. 다시 부팅 하려는 노드의 이름을 가져온 후 REST API를 사용 하 여 노드를 다시 시작 하 여 노드를 다시 부팅 합니다. 노드 이름은 클러스터 이름의 *NodeType (w)/hn/zk/gw)*  +  *x*  +  *처음 6 자*패턴을 따릅니다. 자세한 내용은 [HDInsight restart hosts REST API 작업](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)을 참조 하세요.

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

다시 부팅 하려는 노드의 실제 이름은 요청 본문의 JSON 배열에 지정 됩니다.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>다음 단계

* [다시 시작-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight 가상 컴퓨터 REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)
