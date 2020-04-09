---
title: Azure HDInsight의 클러스터 용량 계획
description: Azure HDInsight 클러스터의 용량 및 성능 계획에 대한 주요 질문을 식별합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886996"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 클러스터의 용량 계획

HDInsight 클러스터를 배포하기 전에 필요한 성능과 규모를 결정하여 의도한 클러스터 용량을 계획합니다. 이러한 계획은 유용성 및 비용을 둘 다 최적화합니다. 일부 클러스터 용량 결정은 배포 후 변경할 수 없습니다. 성능 매개 변수가 변경되면 저장된 데이터를 손실하지 않으면서 클러스터를 분해한 후 다시 만들 수 있습니다.

용량 계획을 위해 확인할 핵심 질문은 다음과 같습니다.

* 클러스터를 어떤 지리적 지역에 배포하려고 하나요?
* 스토리지 공간은 얼마나 필요한가요?
* 어떤 클러스터 유형을 배포해야 하나요?
* 클러스터 노드는 어떤 크기 및 유형의 VM(가상 머신)을 사용해야 하나요?
* 클러스터는 몇 개의 작업자 노드가 있어야 하나요?

## <a name="choose-an-azure-region"></a>Azure 지역 선택

Azure 지역은 클러스터가 물리적으로 프로비전되는 위치를 결정합니다. 읽기 및 쓰기 대기 시간을 최소화하려면 클러스터가 데이터 근처에 있어야 합니다.

HDInsight는 여러 Azure 지역에서 사용할 수 있습니다. 가장 가까운 지역을 찾으려면 [지역별로 사용할 수 있는 제품을](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)참조하십시오.

## <a name="choose-storage-location-and-size"></a>스토리지 위치 및 크기 선택

### <a name="location-of-default-storage"></a>기본 스토리지의 위치

Azure Storage 계정 또는 Azure Data Lake Storage에 해당하는 기본 스토리지는 클러스터와 동일한 위치에 있어야 합니다. Azure Storage는 모든 위치에서 사용할 수 있습니다. 데이터 레이크 스토리지 Gen1은 일부 지역에서 사용할 수 있습니다 - 현재 [데이터 레이크 스토리지 가용성을](https://azure.microsoft.com/global-infrastructure/services/?products=storage)참조하십시오.

### <a name="location-of-existing-data"></a>기존 데이터의 위치

기존 저장소 계정 또는 Data Lake Storage를 클러스터의 기본 저장소로 사용하려면 클러스터를 동일한 위치에 배포해야 합니다.

### <a name="storage-size"></a>스토리지 크기

배포된 클러스터에서 추가 Azure Storage 계정을 연결하거나 다른 데이터 레이크 저장소에 액세스할 수 있습니다. 모든 저장소 계정은 클러스터와 동일한 위치에 있어야 합니다. 데이터 레이크 저장소는 다른 위치에 있을 수 있지만 먼 거리에서는 약간의 대기 시간이 발생할 수 있습니다.

Azure 저장소에는 일부 [용량 제한이](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)있으며 데이터 레이크 저장소 Gen1은 거의 무제한입니다.

클러스터는 다른 스토리지 계정 조합에 액세스할 수 있습니다. 일반적인 예제:

* 데이터 양이 단일 Blob Storage 컨테이너의 스토리지 용량을 초과하기 쉬운 경우
* Blob 컨테이너에 대한 액세스 속도가 임계값을 초과하여 제한이 발생하는 경우
* 데이터를 만들려는 경우 클러스터에서 사용할 수 있는 Blob 컨테이너에 이미 업로드했습니다.
* 보안상의 이유나 관리 간소화를 위해 스토리지의 다른 부분을 격리하려고 하는 경우

성능 향상을 위해서는 스토리지 계정당 하나의 컨테이너만 사용합니다.

## <a name="choose-a-cluster-type"></a>클러스터 유형 선택

클러스터 유형에 따라 HDInsight 클러스터가 실행되도록 구성된 워크로드가 결정됩니다. 유형은 [아파치 하두프,](./hadoop/apache-hadoop-introduction.md) [아파치 스톰,](./storm/apache-storm-overview.md) [아파치 카프카,](./kafka/apache-kafka-introduction.md)또는 [아파치 스파크를](./spark/apache-spark-overview.md)포함한다. 사용 가능한 클러스터 형식의 대한 자세한 설명은 [Azure HDInsight 소개](hdinsight-overview.md#cluster-types-in-hdinsight)를 참조하세요. 각 클러스터 유형은 필요한 노드 크기 및 개수를 포함하는 배포 토폴로지를 갖습니다.

## <a name="choose-the-vm-size-and-type"></a>VM 크기 및 유형 선택

클러스터 유형마다 노드 유형 집합이 있으며 각 노드 유형은 VM 크기 및 유형에 대한 특정 옵션을 제공합니다.

사용하는 애플리케이션에 대해 최적의 클러스터 크기를 결정하려면 클러스터 용량을 벤치마킹하고 지정된 대로 크기를 늘릴 수 있습니다. 예를 들어 시뮬레이트한 워크로드 또는 *카나리아 쿼리*를 사용할 수 있습니다. 다양한 크기의 클러스터에서 시뮬레이션된 워크로드를 실행합니다. 의도한 성능에 도달할 때까지 크기를 점진적으로 늘립니다. 카나리아 쿼리는 다른 프로덕션 쿼리 사이에 주기적으로 삽입하여 클러스터에 충분한 리소스가 있는지 여부를 표시할 수 있습니다.

워크로드에 적합한 VM 제품군을 선택하는 방법에 대한 자세한 내용은 [클러스터에 적합한 VM 크기 선택을](hdinsight-selecting-vm-size.md)참조하십시오.

## <a name="choose-the-cluster-scale"></a>클러스터 확장 선택

클러스터 확장은 VM 노드의 수량에 따라 결정됩니다. 모든 클러스터 유형에는 특정 축척이 있는 노드 유형과 확장을 지원하는 노드 유형이 있습니다. 예를 들어 클러스터에는 정확히 3개의 [아파치 ZooKeeper](https://zookeeper.apache.org/) 노드 또는 두 개의 헤드 노드가 필요할 수 있습니다. 분산 방식으로 데이터 처리를 수행하는 작업자 노드는 추가 작업자 노드의 이점을 누릴 수 있습니다.

클러스터 유형에 따라 작업자 노드 수를 늘리면 추가 계산 용량(예: 코어 증가)이 추가됩니다. 노드가 많을수록 전체 클러스터에 필요한 총 메모리가 증가하여 처리 중인 데이터의 메모리 내 저장소를 지원합니다. VM 크기 및 유형 선택과 마찬가지로 올바른 클러스터 축척을 선택하는 것은 일반적으로 경험적으로 도달합니다. 시뮬레이션된 워크로드 또는 카나리아 쿼리를 사용합니다.

최대 부하 요구 사항을 충족하도록 클러스터를 확장할 수 있습니다. 그런 다음 추가 노드가 더 이상 필요하지 않으면 다시 축소합니다. [자동 크기 조정 기능을](hdinsight-autoscale-clusters.md) 사용하면 미리 결정된 메트릭 및 타이밍에 따라 클러스터를 자동으로 확장할 수 있습니다. 클러스터를 수동으로 확장하는 방법에 대한 자세한 내용은 [HDInsight 클러스터 확장을](hdinsight-scaling-best-practices.md)참조하십시오.

### <a name="cluster-lifecycle"></a>클러스터 수명 주기

클러스터의 수명에 대한 요금이 청구됩니다. 클러스터가 필요한 특정 시간만 있는 경우 [Azure Data Factory를 사용하여 온디맨드 클러스터를 만듭니다.](hdinsight-hadoop-create-linux-clusters-adf.md) 클러스터를 프로비전하고 삭제하는 PowerShell 스크립트를 만든 다음 [Azure Automation](https://azure.microsoft.com/services/automation/)을 사용하여 해당 스크립트를 예약할 수도 있습니다.

> [!NOTE]  
> 클러스터를 삭제하면 해당 기본 Hive 메타스토어도 삭제됩니다. 다음에 클러스터를 다시 만들 때를 대비해서 metastore를 보존하려면 Azure Database 또는 [Apache Oozie](https://oozie.apache.org/)와 같은 외부 메타데이터 저장소를 사용합니다.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>클러스터 작업 오류 격리

여러 맵을 병렬로 실행하고 다중 노드 클러스터의 구성 요소를 줄이기 때문에 오류가 발생할 수 있습니다. 문제를 격리하려면 분산 테스트를 시도해 보십시오. 단일 작업자 노드 클러스터에서 동시 여러 작업을 실행합니다. 그런 다음 이 방법을 확장하여 두 개 이상의 노드를 포함하는 클러스터에서 동시에 여러 작업을 실행합니다. Azure에서 단일 노드 HDInsight 클러스터를 만들려면 *`Custom(size, settings, apps)`* 포털에서 새 클러스터를 프로비전할 때 이 옵션을 사용하고 클러스터 **크기** 섹션에서 작업자 수 노드 *수에* 대해 1값을 사용합니다.

## <a name="quotas"></a>할당량

대상 클러스터 VM 크기, 확장 및 유형을 결정했으면 구독의 현재 할당량 용량 제한을 확인합니다. 할당량 제한에 도달하면 새 클러스터를 배포할 수 없습니다. 또는 작업자 노드를 더 추가하여 기존 클러스터를 확장할 수 있습니다. 유일한 할당량 제한은 각 구독에 대한 지역 수준에서 존재하는 CPU 코어 할당량입니다. 예를 들어 구독에는 미국 동부 지역에서 30 코어 제한이 있을 수 있습니다.

사용 가능한 코어를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. HDInsight 클러스터의 **개요** 페이지로 이동합니다.
3. 왼쪽 메뉴에서 **할당량 제한을**선택합니다.

   페이지에는 사용 가능한 코어 수, 사용 가능한 코어 수 및 총 코어가 표시됩니다.

할당량 증가를 요청해야 하는 경우 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 페이지 왼쪽 하단에서 **도움말 + 지원을** 선택합니다.
1. **새 지원 요청**을 선택합니다.
1. **새 지원 요청** 페이지의 **기본 사항** 탭에서 다음 옵션을 선택합니다.

   - **문제 유형**: **서비스 및 구독 한도(할당량)**
   - **구독**: 수정할 구독
   - **할당량 유형**: **HDInsight**

     ![HDInsight 코어 할당량을 늘리는 지원 요청 만들기](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. **다음 을 선택합니다: 솔루션 >>. **
1. 세부 **정보** 페이지에서 문제에 대한 설명을 입력하고 문제의 심각도, 기본 연락처 방법 및 기타 필수 필드를 선택합니다.
1. **다음 을 선택: 검토 + >>만들기 **.
1. **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

> [!NOTE]  
> 프라이빗 영역에서 HDInsight 코어 할당량을 늘려야 하는 경우 [허용 목록에 추가 요청을 제출합니다](https://aka.ms/canaryintwhitelist).

[지원 서비스에 문의하여 할당량 증가를 요청](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)할 수 있습니다.

일부 고정 된 할당량 제한이 있습니다. 예를 들어 단일 Azure 구독에는 최소 10,000개의 코어가 있을 수 있습니다. 이러한 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [아파치 하두프, 스파크, 카프카 등으로 HDInsight에서 클러스터를](hdinsight-hadoop-provision-linux-clusters.md)설정합니다.
* [클러스터 성능 모니터링](hdinsight-key-scenarios-to-monitor.md): 클러스터의 용량에 영향을 줄 수 있는 HDInsight 클러스터를 모니터링하는 주요 시나리오에 대해 알아봅니다.
