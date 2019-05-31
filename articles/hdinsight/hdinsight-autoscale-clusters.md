---
title: Azure HDInsight 클러스터 (미리 보기)를 자동으로 조정
description: HDInsight 자동 크기 조정 기능을 사용하여 자동으로 클러스터 크기 조정
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 6ec981164de0ff61b0e83d54255d046a1418ed96
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000096"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Azure HDInsight 클러스터 (미리 보기)를 자동으로 조정

> [!Important]
> 자동 크기 조정 기능 월 8 일 2019 이후 생성 된 Spark, Hive 및 MapReduce 클러스터 에서만 작동 합니다. 

Azure HDInsight 클러스터 크기 조정 기능 자동으로 조정 작업자 노드 수는 클러스터의 위아래로 합니다. 현재 다른 유형의 클러스터의 노드를 확장할 수 없습니다.  새 HDInsight 클러스터를 만드는 동안 작업자 노드의 최소 및 최대 수를 설정할 수 있습니다. 다음 자동 크기 조정은 analytics 부하의 리소스 요구를 모니터링 하 고 작업자 노드 수를 확장 하거나 축소 합니다. 이 기능에 대 한 추가 비용은 없습니다.

## <a name="cluster-compatibility"></a>클러스터 호환성

다음 표에서 클러스터 형식 및 크기 조정 기능을 사용 하 여 호환 되는 버전을 설명 합니다.

| Version | Spark | Hive | LLAP | Hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 ESP 없이 | 예. | 예 | 아니오 | 아니요 | 아니요 | 아니요 | 아닙니다. |
| ESP 없이 4.0 HDInsight | 예. | 예 | 아니오 | 아니요 | 아니요 | 아니요 | 아닙니다. |
| ESP 사용 하 여 HDInsight 3.6 | 예. | 예 | 아니오 | 아니요 | 아니요 | 아니요 | 아닙니다. |
| ESP 사용 하 여 HDInsight 3.6 | 예. | 예 | 아니오 | 아니요 | 아니요 | 아니요 | 아닙니다. |

## <a name="how-it-works"></a>작동 방법

부하 기반 크기 조정 또는 일정 기반 HDInsight 클러스터에 대 한 크기 조정 규칙을 선택할 수 있습니다. 부하 기반 크기 조정 최적의 CPU 사용률을 확인 하 고 실행 비용을 최소화 하기 위해 설정할 수 있는 범위 내에서 클러스터의 노드 수를 변경 합니다.

특정 시간에 적용 되는 조건에 따라 클러스터의 노드 수가 크기 조정 변경 일정에 기반 합니다. 이러한 조건에는 원하는 수의 노드 클러스터 크기 조정.

### <a name="metrics-monitoring"></a>메트릭 모니터링

자동 크기 조정은 지속적으로 클러스터를 모니터링하면서 다음 메트릭을 수집합니다.

* **보류 중인 총 CPU**: 보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 코어 수입니다.
* **보류 중인 총 메모리**: 보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 메모리(MB)입니다.
* **사용 가능한 총 CPU**: 활성 작업자 노드에서 사용되지 않는 모든 코어의 합계입니다.
* **사용 가능한 총 메모리**: 활성 작업자 노드에서 사용되지 않는 메모리(MB)의 합계입니다.
* **노드당 사용되는 메모리**: 작업자 노드의 부하입니다. 메모리 사용량이 10GB인 작업자 노드는 메모리 사용량이 2GB인 작업자보다 부하가 많은 것으로 간주됩니다.
* **노드당 애플리케이션 마스터 수**: 작업자 노드에서 실행되는 AM(애플리케이션 마스터) 수입니다. 두 개의 오전 컨테이너를 호스트 하는 작업자 노드 0 오전 컨테이너를 호스트 하는 작업자 노드 보다 더 중요 한 간주 됩니다.

위의 메트릭은 60초마다 확인됩니다. 자동 크기 조정 이러한 메트릭을 기반으로 하는 규모 확장 및 규모 축소 결정을 내립니다.

### <a name="load-based-cluster-scale-up"></a>부하 기반 클러스터 강화

다음 조건이 감지 되 면 자동 크기 조정 규모 요청을 실행 합니다.

* 3 분 넘게 CPU 보류 중인 총 사용 가능한 총 CPU 보다 큽니다.
* 3 분 넘게 보류 중인 메모리의 총 사용 가능한 총 메모리 보다 큽니다.

HDInsight 서비스는 현재 CPU 및 메모리 요구 사항을 충족 하는 데 필요한 새 작업자 노드 수를 계산 하 고 필요한 노드 수를 추가 하기 위한 규모 요청을 발급 합니다.

### <a name="load-based-cluster-scale-down"></a>부하 기반 클러스터 축소

다음 조건이 감지 되 면 자동 크기 조정 규모 축소 요청을 실행 합니다.

* 보류 중인 총 CPU가 사용 가능한 총 CPU보다 작은 시간이 10분을 초과합니다.
* 보류 중인 총 메모리가 사용 가능한 총 메모리보다 작은 시간이 10분을 초과합니다.

자동 크기 조정 당 노드 및 현재 CPU 및 메모리 요구 사항 오전 컨테이너 수에 따라 특정 개수의 노드를 제거 하는 요청을 발급 합니다. 또한 서비스는 현재 작업 실행에 따라 제거에 적합 한 노드를 검색 합니다. 규모 축소 작업 노드를 먼저 decommissions 및 클러스터에서 제거 됩니다.

## <a name="get-started"></a>시작하기

### <a name="create-a-cluster-with-load-based-autoscaling"></a>부하 기반 자동 크기 조정을 사용 하 여 클러스터 만들기

부하 기반 크기 조정을 사용 하 여 자동 크기 조정 기능을 사용 하려면 정상 클러스터를 만드는 프로세스의 일부로 다음 단계를 완료 합니다.

1. **빨리 만들기** 대신에 **사용자 지정(크기, 설정, 앱)** 을 선택합니다.
1. **사용자 지정** 5 단계 (**클러스터 크기**)를 확인 합니다 **작업자 노드 자동 크기 조정** 확인란을 선택 합니다.
1. 옵션을 선택 **부하 기반** 아래에서 **자동 크기 조정 유형**합니다.
1. 다음 속성에 대해 원하는 값을 입력 합니다.  

    * 초기 **작업자 노드 수**  
    * **최소** 작업자 노드 수  
    * **최대** 작업자 노드 수  

    ![작업자 노드 부하 기반 자동 크기 조정 옵션을 사용 하도록 설정](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

초기 작업자 노드 수는 최소 작업자 노드 수~최대 작업자 노드 수 사이여야 합니다(최댓값 및 최솟값 포함). 이 값은 클러스터를 만들 때 초기 크기를 정의합니다. 최소 작업자 노드 수는 0보다 커야 합니다.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>일정 기반 자동 크기 조정을 사용 하 여 클러스터 만들기

일정 기반 크기 조정을 사용 하 여 자동 크기 조정 기능을 사용 하려면 정상 클러스터를 만드는 프로세스의 일부로 다음 단계를 완료 합니다.

1. **빨리 만들기** 대신에 **사용자 지정(크기, 설정, 앱)** 을 선택합니다.
1. **사용자 지정** 5 단계 (**클러스터 크기**)를 확인 합니다 **작업자 노드 자동 크기 조정** 확인란을 선택 합니다.
1. 입력 합니다 **작업자 노드 수**, 클러스터 크기 조정에 대 한 제한을 제어 합니다.
1. 옵션을 선택 **일정 기반** 아래에서 **자동 크기 조정 유형**합니다.
1. 클릭 **구성** 열려는 합니다 **자동 크기 조정 구성** 창입니다.
1. 사용자 표준 시간대를 선택 하 고 클릭 **+ 조건 추가**
1. 새 조건에 적용 해야 하는 요일을 선택 합니다.
1. 클러스터 크기를 조정 해야 하는 노드의 수와 조건을 수행 해야 하는 시간을 편집 합니다.
1. 필요에 따라 조건을 더 추가 합니다.

    ![작업자 노드 일정 기반 자동 크기 조정 옵션을 사용 하도록 설정](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

노드 수가 1 및 조건을 추가 하기 전에 입력 한 작업자 노드의 수 사이 여야 합니다.

### <a name="final-creation-steps"></a>최종 만들기 단계

부하 기반 및 일정 기반 크기 조정에 대 한 클릭 하 여 작업자 노드에 대 한 VM 유형 선택 **작업자 노드 크기** 하 고 **헤드 노드 크기**합니다. 각 노드 유형에 대 한 VM 유형을 선택 하면 전체 클러스터에 대 한 예상된 비용 범위를 볼 수 있습니다. 예산에 맞는 VM 유형을 조정 합니다.

![작업자 노드 일정 기반 자동 크기 조정 옵션을 사용 하도록 설정](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

구독에는 각 Azure 지역에 대한 용량 할당량이 있습니다. 헤드 노드의 코어 수와 최대 작업자 노드 수를 더한 값이 용량 할당량을 초과하면 안 됩니다. 그러나 이 할당량은 소프트 제한이며, 언제든지 지원 티켓을 만들어서 간편하게 할당량을 늘릴 수 있습니다.

> [!Note]  
> '이 지역에서 사용 가능한 코어를 초과 하는 최대 노드, 다른 지역을 선택 하거나 하십시오 할당량을 늘리려면 지원에 문의 합니다.' 없다는 오류 메시지를 받게 총 코어 할당량 한도 초과 하는 경우

Azure Portal을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 클러스터 만들기

#### <a name="load-based-autoscaling"></a>부하 기반 자동 크기 조정

만들면 HDInsight 클러스터를 부하 기반 자동 크기 조정을 사용 하 여는 Azure Resource Manager 템플릿을 추가 하 여는 `autoscale` 노드를 `computeProfile`  >  `workernode` 속성을 사용 하 여 섹션 `minInstanceCount` 및 `maxInstanceCount` 으로 다음 json 코드 조각에 나와 있습니다.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
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

#### <a name="schedule-based-autoscaling"></a>일정 기반 자동 크기 조정

만들면 HDInsight 클러스터를 일정 기반 자동 크기 조정을 사용 하 여는 Azure Resource Manager 템플릿을 추가 하 여는 `autoscale` 노드를 `computeProfile`  >  `workernode` 섹션입니다. 합니다 `autoscale` 노드를 포함을 `recurrence` 있는 `timezone` 및 `schedule` 변경 수행는 때를 설명 하는 합니다.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>실행 중인 클러스터에 자동 크기 조정 사용 및 사용 안 함

을 실행 중인 클러스터에서 자동 크기 조정을 사용 하도록 설정 하려면 선택 **클러스터 크기** 아래에서 **설정**합니다. 누른 **자동 크기 조정 사용**합니다. 자동 크기 조정 하려면 부하 또는 일정 기반 크기 조정에 대 한 옵션을 입력 하는 유형을 선택 합니다. 끝으로, **저장**을 클릭합니다.

![작업자 노드 일정 기반 자동 크기 조정 옵션을 사용 하도록 설정](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="best-practices"></a>모범 사례

### <a name="choosing-load-based-or-schedule-based-scaling"></a>부하 또는 일정 기반 크기 조정 선택

모드 선택에 대 한 결정을 내리기 전에 다음 요소를 고려 합니다.

* 부하 분산: 클러스터의 부하를 따르지 일치 패턴을 특정 날짜에서 특정 시간에 있습니다. 그렇지 않은 경우 부하 기반 예약 하는 것이 더 좋습니다.
* SLA 요구 사항: 자동 크기 조정 확장 하는 것은 예측 하는 대신 반응 형입니다. 부하 증가 하 고 클러스터를 대상 크기로 가능 해야 하는 경우 시작 될 때 사이 충분 한 지연이 됩니까? 엄격한 SLA 요구 사항이 되어 부하 고정된 알려진된 패턴을 '따라 일정' 더 나은 옵션입니다.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>구성 확장의 대기 시간을 고려 하거나 규모 축소 작업

크기 조정 작업을 완료 하려면 10 ~ 20 분 정도 걸릴 수 있습니다. 사용자 지정된 일정을 설정할 때이 지연을 계획 합니다. 예를 들어, 클러스터 크기를 20 오전 9:00에 있을 경우 일정 트리거를 설정 8:30 AM 같은 이전 시점으로 오전 9 시까지 크기 조정 작업이 완료 되도록 합니다.

### <a name="preparation-for-scaling-down"></a>규모를 축소 하는 것에 대 한 준비

클러스터 크기 조정 프로세스를 하는 동안 자동 크기 조정에는 대상 크기에 맞게 노드 서비스 해제 됩니다. 해당 노드에서 작업 실행 중인 경우 자동 크기 조정 작업이 완료 될 때까지 기다릴 수 있습니다. 각 작업자 노드에 HDFS의 역할도 역할을 하므로 임시 데이터 나머지 노드로 이동 합니다. 따라서 모든 임시 데이터를 호스트할 나머지 노드에 충분 한 공간이 있는지 확인 해야 합니다. 

실행 중인 작업을 실행 하 고 완료 계속 됩니다. 보류 중인 작업이 더 적은 수의 사용 가능한 작업자 노드를 사용 하 여 정상적으로 예약 되기 위해 대기 합니다.

## <a name="monitoring"></a>모니터링

### <a name="cluster-status"></a>클러스터 상태

Azure portal에 나열 된 클러스터 상태는 자동 크기 조정 작업을 모니터링할 수 있습니다.

![작업자 노드 부하 기반 자동 크기 조정 옵션을 사용 하도록 설정](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

아래 목록에 표시 될 수 있는 클러스터 상태 메시지를 모두 설명 합니다.

| 클러스터 상태 | 설명 |
|---|---|
| 실행 중 | 클러스터를 정상적으로 작동 합니다. 모든 이전 자동 크기 조정 작업을 성공적으로 완료 했습니다. |
| 업데이트 중  | 클러스터 자동 크기 조정 구성 업데이트 중입니다.  |
| HDInsight 구성  | 클러스터를 강화 또는 규모 축소 작업 진행 중입니다.  |
| 업데이트 오류  | HDInsight에는 자동 크기 조정 구성 업데이트 하는 동안 문제가 발생 했습니다. 고객은 업데이트를 다시 시도 또는 자동 크기 조정을 사용 하지 않도록 설정할 수 있습니다.  |
| 오류  | 클러스터를 사용 하 여 문제가 발생 하 고 사용할 수 없는 합니다. 이 클러스터를 삭제 하 고 새로 만드십시오.  |

클러스터에서 노드의 현재 수를 보려면,로 이동 합니다 **클러스터 크기** 에서 차트를 **개요** 클러스터에 대 한 페이지 또는 클릭 **클러스터 크기** 에서  **설정**합니다.

### <a name="operation-history"></a>작업 기록

클러스터 메트릭의 일부로 클러스터 규모 확장 및 규모 축소 기록을 볼 수 있습니다. 지난 일, 주 또는 다른 기간을 통해 모든 크기 조정 작업을 나열할 수도 있습니다.

선택 **메트릭을** 아래에서 **모니터링**합니다. 클릭 **메트릭 추가** 하 고 **활성 작업자 수** 에서 **메트릭** 드롭다운 상자입니다. 시간 범위를 변경 하려면 오른쪽 위에 있는 단추를 클릭 합니다.

![작업자 노드 일정 기반 자동 크기 조정 옵션을 사용 하도록 설정](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>다음 단계

* [크기 조정 모범 사례](hdinsight-scaling-best-practices.md)에서 클러스터 크기를 수동으로 조정하는 모범 사례에 대해 알아보세요.
