---
title: Azure HDInsight 클러스터 (미리 보기)를 자동으로 조정
description: HDInsight 자동 크기 조정 기능을 사용하여 자동으로 클러스터 크기 조정
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 9631e4b82ceb14a98740491b98288d75dd23f9a3
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501011"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Azure HDInsight 클러스터 (미리 보기)를 자동으로 조정

>[!Important]
>HDInsight 자동 크기 조정 기능은 현재 미리 보기로 제공에서 됩니다. 전자 메일을 보내 주십시오 hdiautoscalepm@microsoft.com 구독에 대해 사용 하도록 설정 하는 자동 크기 조정 해야 합니다.

Azure HDInsight의 클러스터 자동 크기 조정 기능은 미리 정의된 범위 내에서 부하를 기반으로 클러스터의 작업자 노드 수를 자동으로 조정합니다. 새 HDInsight 클러스터를 만드는 동안 작업자 노드의 최소 및 최대 수를 설정할 수 있습니다. 그 후 자동 크기 조정 기능은 분석 부하의 리소스 요구 사항을 모니터링하면서 그에 따라 작업자 노드 수를 늘리거나 줄입니다. 이 기능을 사용하기 위한 추가 요금은 없습니다.

## <a name="getting-started"></a>시작

### <a name="create-a-cluster-with-the-azure-portal"></a>Azure Portal을 사용하여 클러스터 만들기

> [!Note]
> 자동 크기 조정은 현재 Azure HDInsight Hive, MapReduce 및 Spark 클러스터 버전 3.6에서만 지원됩니다.

자동 크기 조정 기능을 사용 하려면 정상 클러스터를 만드는 프로세스의 일부로 다음을 수행 합니다.

1. **빨리 만들기** 대신에 **사용자 지정(크기, 설정, 앱)** 을 선택합니다.
2. **사용자 지정**  5단계(**클러스터 크기**)에서 **작업자 노드 자동 크기 조정** 확인란을 선택합니다.
3. 다음 속성에 대해 원하는 값을 입력 합니다.  

    * 초기 **작업자 노드 수**  
    * **최소** 작업자 노드 수  
    * **최대** 작업자 노드 수  

![작업자 노드 자동 크기 조정 옵션을 사용하도록 설정](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

초기 작업자 노드 수는 최소 작업자 노드 수~최대 작업자 노드 수 사이여야 합니다(최댓값 및 최솟값 포함). 이 값은 클러스터를 만들 때 초기 크기를 정의합니다. 최소 작업자 노드 수는 0보다 커야 합니다.

각 노드 형식의 VM 유형을 선택하면 전체 클러스터의 예상 비용 범위를 볼 수 있습니다. 그러면 예산에 맞게 이러한 설정을 조정할 수 있습니다.

구독에는 각 Azure 지역에 대한 용량 할당량이 있습니다. 헤드 노드의 코어 수와 최대 작업자 노드 수를 더한 값이 용량 할당량을 초과하면 안 됩니다. 그러나 이 할당량은 소프트 제한이며, 언제든지 지원 티켓을 만들어서 간편하게 할당량을 늘릴 수 있습니다.

> [!Note]  
> 총 코어 할당량 제한을 초과하면 '최대 노드 수가 이 Azure 지역에서 사용 가능한 코어 수를 초과했습니다. 다른 Azure 지역을 선택하거나 지원 팀에 문의하여 할당량을 늘리세요'라는 내용의 오류 메시지를 받게 됩니다.

Azure Portal을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 클러스터 만들기

Azure Resource Manager 템플릿을 사용하여 HDInsight 클러스터를 만들려면 아래 JSON 코드 조각에 표시된 대로 `minInstanceCount` 및 `maxInstanceCount` 속성을 사용하여 `computeProfile` > `workernode` 섹션에 `autoscale` 노드를 추가합니다.

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

Resource Manager 템플릿을 사용하여 클러스터를 만드는 방법에 대한 자세한 내용은 [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.  

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>실행 중인 클러스터에 자동 크기 조정 사용 및 사용 안 함

만 사용 하도록 설정 하거나 새 HDInsight 클러스터에 대 한 자동 크기 조정을 비활성화할 수 있습니다.

## <a name="monitoring"></a>모니터링

클러스터 메트릭의 일부로 클러스터 규모 확장 및 규모 축소 기록을 볼 수 있습니다. 어제, 지난주 또는 좀 더 긴 기간의 크기 조정 작업도 모두 나열할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

### <a name="metrics-monitoring"></a>메트릭 모니터링

자동 크기 조정은 지속적으로 클러스터를 모니터링하면서 다음 메트릭을 수집합니다.

1. **보류 중인 총 CPU**: 보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 코어 수입니다.
2. **보류 중인 총 메모리**: 보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 메모리(MB)입니다.
3. **사용 가능한 총 CPU**: 활성 작업자 노드에서 사용되지 않는 모든 코어의 합계입니다.
4. **사용 가능한 총 메모리**: 활성 작업자 노드에서 사용되지 않는 메모리(MB)의 합계입니다.
5. **노드당 사용되는 메모리**: 작업자 노드의 부하입니다. 메모리 사용량이 10GB인 작업자 노드는 메모리 사용량이 2GB인 작업자보다 부하가 많은 것으로 간주됩니다.
6. **노드당 애플리케이션 마스터 수**: 작업자 노드에서 실행되는 AM(애플리케이션 마스터) 수입니다. 두 개의 오전 컨테이너를 호스트 하는 작업자 노드 0 오전 컨테이너를 호스트 하는 작업자 노드 보다 더 중요 한 간주 됩니다.

위의 메트릭은 60초마다 확인됩니다. 자동 크기 조정 결정을 내릴 규모 확장 및 규모 축소 이러한 메트릭을 기반으로 합니다.

### <a name="cluster-scale-up"></a>클러스터 규모

다음 조건이 감지 되 면 자동 크기 조정 규모 요청을 실행 합니다.

* 3 분 이상 CPU 보류 중인 총 사용 가능한 총 CPU 보다 큽니다.
* 3 분 이상에 대 한 보류 중인 메모리의 총 사용 가능한 총 메모리 보다 큽니다.

현재 CPU 및 메모리 요구 사항을 충족 하는 새 작업자 노드 수를 추가 하는 규모 요청을 실행 한 다음 특정 새 작업자 노드 수가 필요 함을 계산 됩니다.

### <a name="cluster-scale-down"></a>클러스터 확장 / 축소

다음 조건이 감지 되 면 자동 크기 조정 규모 축소 요청을 실행 합니다.

* 보류 중인 총 CPU가 사용 가능한 총 CPU보다 작은 시간이 10분을 초과합니다.
* 보류 중인 총 메모리가 사용 가능한 총 메모리보다 작은 시간이 10분을 초과합니다.

노드 및 현재 CPU 및 메모리 요구 사항 마다 오전 컨테이너 수에 따라 자동 크기 조정 요청을 실행 특정 개수의 노드를 제거 하는 노드는 제거에 대 한 잠재적인 후보를 지정 합니다. 규모 축소 노드 서비스 해제 트리거할 및 노드 완전히 해제 되 면 제거 됩니다.

## <a name="next-steps"></a>다음 단계

* [크기 조정 모범 사례](hdinsight-scaling-best-practices.md)에서 클러스터 크기를 수동으로 조정하는 모범 사례에 대해 알아보세요.
