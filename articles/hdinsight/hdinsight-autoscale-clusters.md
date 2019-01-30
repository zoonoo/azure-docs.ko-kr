---
title: Azure HDInsight 클러스터 자동 크기 조정
description: HDInsight 자동 크기 조정 기능을 사용하여 자동으로 클러스터 크기 조정
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 043c83e2039d87b1650ba17f770ce16a2ad2c13d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811165"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터 자동 크기 조정

Azure HDInsight의 클러스터 자동 크기 조정 기능은 미리 정의된 범위 내에서 부하를 기반으로 클러스터의 작업자 노드 수를 자동으로 조정합니다. 새 HDInsight 클러스터를 만드는 동안 작업자 노드의 최소 및 최대 수를 설정할 수 있습니다. 그 후 자동 크기 조정 기능은 분석 부하의 리소스 요구 사항을 모니터링하면서 그에 따라 작업자 노드 수를 늘리거나 줄입니다. 이 기능을 사용하기 위한 추가 요금은 없습니다.

## <a name="getting-started"></a>시작하기

### <a name="create-cluster-with-azure-portal"></a>Azure Portal을 사용하여 클러스터 만들기

> [!Note]
> 자동 크기 조정은 현재 Azure HDInsight Hive, MapReduce 및 Spark 클러스터 버전 3.6에서만 지원됩니다.

[Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)의 단계를 수행하다가 5단계 **클러스터 크기**에 도달하면 아래와 같이 **작업자 노드 자동 크기 조정(미리 보기)** 을 선택합니다. 

![작업자 노드 자동 크기 조정 옵션을 사용하도록 설정](./media/hdinsight-autoscale-clusters/worker-node-autoscale-option.png)

이 옵션을 선택하면 다음을 지정할 수 있습니다.

* 초기 작업자 노드 수
* 최소 작업자 노드 수
* 최대 작업자 노드 수

초기 작업자 노드 수는 최소 작업자 노드 수~최대 작업자 노드 수 사이여야 합니다(최댓값 및 최솟값 포함). 이 값은 클러스터를 만들 때 초기 크기를 정의합니다. 최소 작업자 노드 수는 0보다 커야 합니다.

각 노드 형식의 VM 유형을 선택하면 전체 클러스터의 예상 비용 범위를 볼 수 있습니다. 그러면 예산에 맞게 이러한 설정을 조정할 수 있습니다.

구독에는 각 Azure 지역에 대한 용량 할당량이 있습니다. 헤드 노드의 코어 수와 최대 작업자 노드 수를 더한 값이 용량 할당량을 초과하면 안 됩니다. 그러나 이 할당량은 소프트 제한이며, 언제든지 지원 티켓을 만들어서 간편하게 할당량을 늘릴 수 있습니다.

> [!Note]
> 총 코어 할당량 제한을 초과하면 '최대 노드 수가 이 Azure 지역에서 사용 가능한 코어 수를 초과했습니다. 다른 Azure 지역을 선택하거나 지원 팀에 문의하여 할당량을 늘리세요'라는 내용의 오류 메시지를 받게 됩니다.

### <a name="create-cluster-with-an-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 클러스터 만들기

Resource Manager 템플릿을 사용하여 HDInsight 클러스터를 만들 때 "computeProfile" "작업자 노드" 섹션에서 다음 설정을 추가해야 합니다.

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>실행 중인 클러스터에 자동 크기 조정 사용 및 사용 안 함

비공개 미리 보기 기간에는 실행 중인 클러스터에 자동 크기 조정을 사용할 수 없습니다. 클러스터를 만들 때 사용해야 합니다.

비공개 미리 보기 기간에는 실행 중인 클러스터에 자동 크기 조정을 사용하지 않거나 자동 크기 조정 설정을 수정할 수 없습니다. 설정을 삭제 또는 수정하려면 클러스터를 삭제하고 새 클러스터를 만들어야 합니다.

## <a name="monitoring"></a>모니터링

클러스터 메트릭의 일부로 클러스터 강화 및 축소 기록을 볼 수 있습니다. 어제, 지난 주 또는 좀 더 긴 기간의 크기 조정 작업을 모두 나열할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

### <a name="metrics-monitoring"></a>메트릭 모니터링

자동 크기 조정은 지속적으로 클러스터를 모니터링하면서 다음 메트릭을 수집합니다.

1. **보류 중인 총 CPU**: 보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 코어 수입니다.
2. **보류 중인 총 메모리**: 보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 메모리(MB)입니다.
3. **사용 가능한 총 CPU**: 활성 작업자 노드에서 사용되지 않는 모든 코어의 합계입니다.
4. **사용 가능한 총 메모리**: 활성 작업자 노드에서 사용되지 않는 메모리(MB)의 합계입니다.
5. **노드당 사용되는 메모리**: 작업자 노드의 부하입니다. 메모리 사용량이 10GB인 작업자 노드는 메모리 사용량이 2GB인 작업자보다 부하가 많은 것으로 간주됩니다.
6. **노드당 애플리케이션 마스터 수**: 작업자 노드에서 실행되는 AM(애플리케이션 마스터) 수입니다. AM 컨테이너 2개를 호스팅하는 작업자 노드는 AM 컨테이너 0개를 호스팅하는 작업자 노드보다 중요한 것으로 간주됩니다.

위의 메트릭은 60초마다 확인됩니다. 자동 크기 조정은 이러한 메트릭을 기반으로 강화 및 규모 축소 의사 결정을 내립니다.

### <a name="cluster-scale-up"></a>클러스터 강화

다음 조건이 감지되면 자동 크기 조정에서 강화 요청을 실행합니다.

* 보류 중인 총 CPU가 사용 가능한 총 CPU보다 큰 시간이 1분을 초과합니다.
* 보류 중인 총 메모리가 사용 가능한 총 메모리보다 큰 시간이 1분을 초과합니다.

현재 CPU 및 메모리 요구 사항을 충족하기 위해 필요한 새 작업자 노드 N개를 계산한 다음, 새 작업자 노드 N개를 요청하여 강화 요청을 실행합니다.

### <a name="cluster-scale-down"></a>클러스터 규모 축소

다음 조건이 감지되면 자동 크기 조정에서 규모 축소 요청을 실행합니다.

* 보류 중인 총 CPU가 사용 가능한 총 CPU보다 작은 시간이 10분을 초과합니다.
* 보류 중인 총 메모리가 사용 가능한 총 메모리보다 작은 시간이 10분을 초과합니다.

자동 크기 조정은 현재 CPU 및 메모리 요구 사항뿐 아니라 노드당 AM 컨테이너 수에 따라 N개 노드를 제거하는 요청을 실행하고, 잠재적인 제거 후보가 될 노드를 지정합니다. 기본적으로 한 번의 주기에 두 개 노드가 제거됩니다.

## <a name="next-steps"></a>다음 단계

* [크기 조정 모범 사례](hdinsight-scaling-best-practices.md)에서 클러스터 크기를 수동으로 조정하는 모범 사례에 대해 알아보세요.
