---
title: Azure HDInsight 클러스터 자동 크기 조정
description: Azure HDInsight 자동 크기 조정 기능을 사용하여 자동으로 아파치 하두프 스케일 클러스터
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 68bc30d08d95fe8e3d20a8ecb7af6c9710951921
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399723"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터 자동 크기 조정

> [!Important]
> Azure HDInsight 자동 크기 조정 기능은 Spark 및 Hadoop 클러스터에 대해 2019년 11월 7일에 일반 공급을 위해 릴리스되었으며 이 기능의 미리 보기 버전에서는 사용할 수 없는 개선 사항이 포함되어 있습니다. 2019년 11월 7일 이전에 Spark 클러스터를 만들고 클러스터에서 자동 크기 조정 기능을 사용하려는 경우 권장되는 경로는 새 클러스터를 만들고 새 클러스터에서 자동 크기 조정을 사용하도록 설정하는 것입니다.
>
> LLAP(대화형 쿼리) 및 HBase 클러스터에 대한 자동 크기 조정은 아직 미리 보기 상태입니다. 자동 크기 조정은 스파크, Hadoop, 대화형 쿼리 및 HBase 클러스터에서만 사용할 수 있습니다.

Azure HDInsight의 클러스터 자동 크기 조정 기능은 클러스터의 작업자 노드 수를 자동으로 위아래로 확장합니다. 클러스터의 다른 유형의 노드는 현재 확장할 수 없습니다.  새 HDInsight 클러스터를 만드는 동안 작업자 노드의 최소 및 최대 수를 설정할 수 있습니다. 그런 다음 자동 크기 조정은 분석 로드의 리소스 요구 사항을 모니터링하고 작업자 노드 수를 위 또는 아래로 확장합니다. 이 기능에 대한 추가 요금은 없습니다.

## <a name="cluster-compatibility"></a>클러스터 호환성

다음 표에서는 자동 크기 조정 기능과 호환되는 클러스터 유형 및 버전에 대해 설명합니다.

| 버전 | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| ESP없이 HDInsight 3.6 | yes | yes | yes | 예* | 예 | 예 | 예 |
| ESP없이 HDInsight 4.0 | yes | yes | yes | 예* | 예 | 예 | 예 |
| HDInsight 3.6 with ESP | yes | yes | yes | 예* | 예 | 예 | 예 |
| ESP를 갖춘 HDInsight 4.0 | yes | yes | yes | 예* | 예 | 예 | 예 |

\*HBase 클러스터는 로드 기반이 아닌 일정 기반 크기 조정에 대해서만 구성할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

HDInsight 클러스터에 대한 로드 기반 크기 조정 또는 일정 기반 크기 조정을 선택할 수 있습니다. 부하 기반 크기 조정은 최적의 CPU 사용률을 보장하고 운영 비용을 최소화하기 위해 설정한 범위 내에서 클러스터의 노드 수를 변경합니다.

일정 기반 크기 조정은 특정 시간에 적용되는 조건에 따라 클러스터의 노드 수를 변경합니다. 이러한 조건은 클러스터를 원하는 수의 노드로 확장합니다.

### <a name="metrics-monitoring"></a>메트릭 모니터링

자동 크기 조정은 지속적으로 클러스터를 모니터링하면서 다음 메트릭을 수집합니다.

|메트릭|설명|
|---|---|
|총 보류 중인 CPU|보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 코어 수입니다.|
|총 보류 중인 메모리|보류 중인 모든 컨테이너의 실행을 시작하는 데 필요한 총 메모리(MB)입니다.|
|총 무료 CPU|활성 작업자 노드에서 사용되지 않는 모든 코어의 합계입니다.|
|총 사용 비 메모리|활성 작업자 노드에서 사용되지 않는 메모리(MB)의 합계입니다.|
|노드당 사용된 메모리|작업자 노드의 부하입니다. 메모리 사용량이 10GB인 작업자 노드는 메모리 사용량이 2GB인 작업자보다 부하가 많은 것으로 간주됩니다.|
|노드당 응용 프로그램 마스터 수|작업자 노드에서 실행되는 AM(애플리케이션 마스터) 수입니다. 두 개의 AM 컨테이너를 호스팅하는 작업자 노드는 0AM 컨테이너를 호스팅하는 작업자 노드보다 더 중요한 것으로 간주됩니다.|

위의 메트릭은 60초마다 확인됩니다. 자동 크기 조정은 이러한 메트릭을 기반으로 확장 및 축소 결정을 내합니다.

### <a name="load-based-scale-conditions"></a>부하 기반 스케일 조건

다음 조건이 감지되면 자동 크기 조정이 스케일 요청을 발행합니다.

|강화|축소|
|---|---|
|총 보류 중인 CPU는 3분 이상 총 무료 CPU보다 큽합니다.|보류 중인 총 CPU가 사용 가능한 총 CPU보다 작은 시간이 10분을 초과합니다.|
|보류 중인 총 메모리가 3분 이상 동안 사용 중인 총 메모리보다 큽합니다.|보류 중인 총 메모리가 사용 가능한 총 메모리보다 작은 시간이 10분을 초과합니다.|

확장의 경우 HDInsight 서비스는 현재 CPU 및 메모리 요구 사항을 충족하는 데 필요한 새 작업자 노드 수를 계산한 다음 필요한 노드 수를 추가하기 위해 확장 요청을 발행합니다.

축소의 경우 노드당 AM 컨테이너 수와 현재 CPU 및 메모리 요구 사항에 따라 자동 크기 조정은 특정 수의 노드를 제거하는 요청을 발행합니다. 또한 서비스는 현재 작업 실행을 기반으로 제거할 노드를 검색합니다. 축소 작업은 먼저 노드를 해제한 다음 클러스터에서 제거합니다.

## <a name="get-started"></a>시작

### <a name="create-a-cluster-with-load-based-autoscaling"></a>부하 기반 자동 크기 조정을 사용하여 클러스터 만들기

부하 기반 크기 조정을 사용하여 자동 조정 기능을 사용하려면 일반 클러스터 생성 프로세스의 일부로 다음 단계를 완료합니다.

1. 구성 **+ 가격 책정** 탭에서 **자동 크기 조정 확인란을** 선택합니다.
1. **자동 크기 조정 유형에서**부하 **기반** 을 선택합니다.
1. 다음 속성에 대해 원하는 값을 입력합니다.  

    * **작업자**노드의 초기 **노드 수입니다.**
    * **작업자** 노드의 최소 수입니다.
    * **최대** 작업자 노드 수입니다.

    ![작업자 노드 로드 기반 자동 크기 조정](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

초기 작업자 노드 수는 최소 작업자 노드 수~최대 작업자 노드 수 사이여야 합니다(최댓값 및 최솟값 포함). 이 값은 클러스터가 생성될 때 클러스터의 초기 크기를 정의합니다. 최소 작업자 노드 수는 3개 이상으로 설정해야 합니다. 클러스터를 3개 미만으로 확장하면 파일 복제가 부족하여 안전 모드에 갇힐 수 있습니다.  자세한 내용은 [안전 모드에 갇히기](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)를 참조하십시오.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>일정 기반 자동 크기 조정을 사용하여 클러스터 만들기

일정 기반 크기 조정을 사용하여 자동 조정 기능을 사용하려면 일반 클러스터 생성 프로세스의 일부로 다음 단계를 완료합니다.

1. 구성 **+ 가격 책정** 탭에서 **자동 크기 조정 확인란을** 선택합니다.
1. 클러스터 확장 제한을 제어하는 **Worker 노드의** **노드 수를** 입력합니다.
1. **자동 크기 조정 유형에서** **일정 기반** 옵션을 선택합니다.
1. **자동 크기 조정 구성** 창을 열려면 **구성을** 선택합니다.
1. 시간대를 선택한 다음 **+ 조건 추가를** 클릭합니다.
1. 새 조건이 적용되는 요일을 선택합니다.
1. 조건이 적용되는 시간과 클러스터의 크기를 조정해야 하는 노드 수를 편집합니다.
1. 필요한 경우 조건을 더 추가합니다.

    ![작업자 노드 일정 기반 생성 사용](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

노드 수는 조건을 추가하기 전에 입력한 작업자 노드의 최대 수인 3개에서 최대 수여야 합니다.

### <a name="final-creation-steps"></a>최종 생성 단계

로드 기반 및 일정 기반 크기 조정모두에서 **노드 크기**아래의 드롭다운 목록에서 VM을 선택하여 작업자 노드에 대한 VM 유형을 선택합니다. 각 노드 유형에 대한 VM 유형을 선택한 후 전체 클러스터의 예상 비용 범위를 확인할 수 있습니다. 예산에 맞게 VM 유형을 조정합니다.

![작업자 노드 일정 기반 자동 크기 조정 노드 크기 사용](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

구독에는 각 Azure 지역에 대한 용량 할당량이 있습니다. 헤드 노드의 총 코어 수와 최대 작업자 노드 수가 결합된 코어수는 용량 할당량을 초과할 수 없습니다. 그러나 이 할당량은 소프트 제한이며, 언제든지 지원 티켓을 만들어서 간편하게 할당량을 늘릴 수 있습니다.

> [!Note]  
> 총 코어 할당량 제한을 초과하면 '최대 노드가 이 리전의 사용 가능한 코어를 초과했다는 오류 메시지가 나타납니다.

Azure Portal을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 클러스터 만들기

#### <a name="load-based-autoscaling"></a>부하 기반 자동 크기 조정

아래 `autoscale` json 스니펫과 `computeProfile`  >  `workernode` `minInstanceCount` `maxInstanceCount` 같이 속성이 있는 섹션에 노드를 추가하여 로드 기반 Azure 리소스 관리자 템플릿을 사용하여 HDInsight 클러스터를 만들 수 있습니다.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
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

섹션에 노드를 추가하여 Azure 리소스 관리자 템플릿의 일정 기반 자동 `autoscale` 크기 `computeProfile`  >  `workernode` 조정이 있는 HDInsight 클러스터를 만들 수 있습니다. 노드에는 `autoscale` `recurrence` 변경이 수행되는 `schedule` 시기를 설명하는 a가 `timezone` 포함되어 있습니다.

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

#### <a name="using-the-azure-portal"></a>Azure Portal 사용

실행 중인 클러스터에서 자동 크기 조정을 사용하려면 **설정**에서 **클러스터 크기를** 선택합니다. 그런 다음 **자동 크기 조정 을 선택합니다.** 원하는 자동 크기 조정 유형을 선택하고 부하 기반 또는 일정 기반 크기 조정에 대한 옵션을 입력합니다. 마지막으로 **저장**을 선택합니다.

![작업자 노드 일정 기반 자동 스케일 실행 클러스터 사용](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>REST API 사용

REST API를 사용하여 실행 중인 클러스터에서 자동 크기 조정을 사용하거나 사용하지 않으려면 아래 코드 조각에 표시된 대로 자동 크기 조정 끝점에 POST 요청을 합니다.

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

요청 페이로드에 적절한 매개 변수를 사용합니다. 아래의 json 페이로드를 사용하여 자동 크기 조정을 활성화할 수 있습니다. 페이로드를 `{autoscale: null}` 사용하여 자동 크기 조정을 사용하지 않도록 설정합니다.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

모든 페이로드 매개 변수에 대한 전체 설명은 [부하 기반 자동 크기 조정 을 사용하도록 설정하는](#load-based-autoscaling) 이전 섹션을 참조하십시오.

## <a name="best-practices"></a>모범 사례

### <a name="choosing-load-based-or-schedule-based-scaling"></a>부하 기반 또는 일정 기반 스케일링 선택

선택할 모드를 결정하기 전에 다음 요소를 고려하십시오.

* 클러스터 를 만드는 동안 자동 조정을 활성화합니다.
* 최소 노드 수는 3개 이상이어야 합니다.
* 부하 분산: 클러스터의 로드가 특정 요일에 일관된 패턴을 따릅니다. 그렇지 않은 경우 로드 기반 스케줄링이 더 나은 옵션입니다.
* SLA 요구 사항: 자동 크기 조정은 예측대신 반응적입니다. 부하가 증가하기 시작하는 시기와 클러스터가 목표 크기로 조정되어야 하는 시점 사이에 충분한 지연이 있습니까? 엄격한 SLA 요구 사항이 있고 부하가 알려진 고정된 패턴인 경우 '일정 기반'이 더 나은 옵션입니다.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>확장 또는 확장 작업의 대기 시간 고려

크기 조정 작업이 완료되는 데 10~20분 정도 걸릴 수 있습니다. 사용자 지정된 일정을 설정할 때 이 지연을 계획합니다. 예를 들어 클러스터 크기를 오전 9:00에 20이어야 하는 경우 크기 조정 작업이 오전 9:00까지 완료되도록 일정 트리거를 오전 8:30과 같은 이전 시간으로 설정합니다.

### <a name="preparation-for-scaling-down"></a>축소 준비

클러스터 축소 프로세스 중에 자동 크기 조정은 대상 크기에 맞게 노드를 해제합니다. 해당 노드에서 실행 중인 작업이 있는 경우 자동 크기 조정은 작업이 완료될 때까지 기다립니다. 각 작업자 노드도 HDFS에서 역할을 하므로 임시 데이터는 나머지 노드로 이동됩니다. 따라서 나머지 노드에 모든 임시 데이터를 호스트할 공간이 충분한지 확인해야 합니다.

실행 중인 작업은 계속 실행되고 완료됩니다. 보류 중인 작업은 사용 가능한 작업자 노드 수가 적어 정상적으로 예약될 때까지 기다립니다.

### <a name="minimum-cluster-size"></a>최소 클러스터 크기

클러스터를 3개 미만으로 축소하지 마십시오. 클러스터를 3개 미만으로 확장하면 파일 복제가 부족하여 안전 모드에 갇힐 수 있습니다.  자세한 내용은 [안전 모드에 갇히기](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)를 참조하십시오.

## <a name="monitoring"></a>모니터링

### <a name="cluster-status"></a>클러스터 상태

Azure 포털에 나열된 클러스터 상태는 자동 크기 조정 활동을 모니터링하는 데 도움이 될 수 있습니다.

![작업자 노드 로드 기반 자동 스케일 클러스터 상태 활성화](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

표시되는 모든 클러스터 상태 메시지는 아래 목록에 설명되어 있습니다.

| 클러스터 상태 | 설명 |
|---|---|
| 실행 중 | 클러스터가 정상적으로 작동합니다. 이전의 모든 자동 크기 조정 활동이 성공적으로 완료되었습니다. |
| 업데이트하는 중  | 클러스터 자동 크기 조정 구성이 업데이트되고 있습니다.  |
| HDInsight 구성  | 클러스터 확장 또는 축소 작업이 진행 중입니다.  |
| 오류 업데이트  | HDInsight는 자동 크기 조정 구성 업데이트 중에 문제가 발생했습니다. 고객은 업데이트를 다시 시도하거나 자동 크기 조정을 사용하지 않도록 선택할 수 있습니다.  |
| Error  | 클러스터에 문제가 있어 사용할 수 없습니다. 이 클러스터를 삭제하고 새 클러스터를 만듭니다.  |

클러스터의 현재 노드 수를 보려면 클러스터의 **개요** 페이지에서 **클러스터 크기** 차트로 이동하거나 **설정**에서 **클러스터 크기를** 선택합니다.

### <a name="operation-history"></a>작업 기록

클러스터 확장 및 축소 기록을 클러스터 메트릭의 일부로 볼 수 있습니다. 또한 지난 날, 주 또는 기타 기간 동안의 모든 크기 조정 작업을 나열할 수도 있습니다.

**모니터링**에서 **메트릭을 선택합니다.** 그런 다음 **메트릭 드롭다운** 상자에서 **Metric** 메트릭 추가 및 **활성 작업자 수를** 선택합니다. 오른쪽 상단에 있는 단추를 선택하여 시간 범위를 변경합니다.

![작업자 노드 일정 기반 자동 크기 조정 메트릭 사용](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>다음 단계

[크기 조정 모범 사례](hdinsight-scaling-best-practices.md)에서 클러스터 크기를 수동으로 조정하는 모범 사례에 대해 알아보세요.
