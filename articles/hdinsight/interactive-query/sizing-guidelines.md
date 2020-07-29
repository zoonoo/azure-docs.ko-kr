---
title: Azure HDInsight의 대화형 쿼리 클러스터 크기 조정 가이드
description: Azure HDInsight의 대화형 쿼리 크기 조정 가이드
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a81eff1dcf48996c319933aa4dd46170043b943b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663651"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Azure HDInsight의 대화형 쿼리 클러스터 크기 조정 가이드

이 문서에서는 일반적인 워크로드에서 적절한 성능을 얻을 수 있도록 HDInsight 대화형 쿼리 클러스터(LLAP)의 크기를 조정하는 방법을 설명합니다. 이 문서에서 제공하는 권장 사항은 일반적이며 워크로드에 따라 특별한 조정이 필요할 수 있습니다.

## <a name="default-vm-types-for-interactive-query"></a>대화형 쿼리의 기본 VM 유형

| 노드 유형 | 인스턴스 | 크기 |
|---|---|---|
| Head | D13 v2 | 8개 vCPU, 56GB RAM, 400GB SSD |
| 작업자 | D14 v2 | 16개 vCPU, 112GB RAM, 800GB SSD |
| ZooKeeper | A4 v2 | 4개 vCPU, 8GB RAM, 40GB SSD |

## <a name="recommended-configurations"></a>권장 구성

권장 구성 값은 D14 v2 유형 작업자 노드 기준입니다.

| 키 | 값 | Description |
|---|---|---|
| yarn.nodemanager.resource.memory-mb | 102400(MB) | 노드의 모든 YARN 컨테이너에 제공되는 총 메모리 양(MB)입니다. |
| yarn.scheduler.maximum-allocation-mb | 102400(MB) | RM의 모든 컨테이너 요청에 제공되는 최대 할당량(MB)입니다. 이 값보다 높은 메모리 요청은 적용되지 않습니다. |
| yarn.scheduler.maximum-allocation-vcores | 12 |Resource Manager의 모든 컨테이너 요청에 제공되는 최대 CPU 코어 수입니다. 이 값보다 높은 요청은 적용되지 않습니다. |
| yarn.scheduler.capacity.root.llap.capacity | 90% | LLAP 큐에 제공되는 YARN 용량 할당량입니다.  |
| hive.server2.tez.sessions.per.default.queue | number_of_worker_nodes |hive.server2.tez.default.queues에 명명된 각 큐의 세션 수입니다. 이 수는 쿼리 코디네이터 수(Tez AM)에 해당합니다. |
| tez.am.resource.memory.mb | 4096(MB) | tez AppMaster에서 사용하는 메모리 양(MB)입니다. |
| hive.tez.container.size | 4096(MB) | 지정된 Tez 컨테이너 크기(MB)입니다. |
| hive.llap.daemon.num.executors | 12 | LLAP 디먼당 실행기 수입니다. |
| hive.llap.io.threadpool.size | 12 | 실행기의 스레드 풀 크기입니다. |
| hive.llap.daemon.yarn.container.mb | 86016(MB) | 개별 LLAP 디먼에서 사용하는 총 메모리(MB)입니다(디먼당 메모리).|
| hive.llap.io.memory.size | 409600(MB) | SSD 캐시를 사용하는 경우 LLAP 디먼당 캐시 크기(MB)입니다. |
| hive.auto.convert.join.noconditionaltask.size | 2048(MB) | 맵 조인을 수행할 메모리 크기(MB)입니다. |

## <a name="llap-daemon-size-estimations"></a>LLAP 디먼 크기 예측  

### <a name="yarnnodemanagerresourcememory-mb"></a>yarn.nodemanager.resource.memory-mb

이 값은 각 노드의 YARN 컨테이너에서 사용하는 메모리의 최대 합계(MB)를 나타냅니다. 이 값은 YARN이 이 노드에서 사용할 수 있는 메모리 양을 지정하므로 해당 노드의 전체 메모리보다 작아야 합니다.

이 값을 = [노드의 총 실제 메모리] – [OS + 기타 서비스에 사용할 메모리]로 설정하세요.

이 값을 가용 RAM의 90% 이하로 설정하는 것이 좋습니다. D14 v2의 경우 권장 값은 **102400MB**입니다.

### <a name="yarnschedulermaximum-allocation-mb"></a>yarn.scheduler.maximum-allocation-mb

이 값은 Resource Manager의 모든 컨테이너 요청에 제공되는 최대 할당량(MB)을 나타냅니다. 지정된 값보다 높은 메모리 요청은 적용되지 않습니다. Resource Manager는 `yarn.scheduler.minimum-allocation-mb` 단위로만 컨테이너에 메모리를 제공할 수 있으며, `yarn.scheduler.maximum-allocation-mb`에 지정된 크기를 초과할 수 없습니다. 이 값은 노드에 제공되는 총 메모리를 초과할 수 없으며, 총 메모리는 `yarn.nodemanager.resource.memory-mb`에서 지정합니다.

D14 v2 작업자 노드의 경우 권장 값은 **102400MB**입니다.

### <a name="yarnschedulermaximum-allocation-vcores"></a>yarn.scheduler.maximum-allocation-vcores

이 구성은 Resource Manager의 모든 컨테이너 요청에 제공되는 최대 가상 CPU 코어 수입니다. 이 구성보다 높은 값을 요청할 수 없습니다. 이 구성은 YARN 스케줄러의 글로벌 속성입니다. LLAP 디먼 컨테이너의 경우 이 값을 가용 가상 코어(vCORE)의 75%로 설정할 수 있습니다. 나머지 25%는 NodeManager, DataNode 및 작업자 노드에서 실행되는 기타 서비스를 위해 남겨 두어야 합니다.  

D14 v2 작업자 노드의 경우 vCORE 16개가 있으며 vCORE 16개의 75%를 제공할 수 있습니다. 따라서 LLAP 디먼 컨테이너의 권장 값은 **12**입니다.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>hive.server2.tez.sessions.per.default.queue

이 구성 값은 `hive.server2.tez.default.queues`에 지정된 큐마다 병렬로 실행해야 하는 Tez 세션 수를 결정합니다. 이 값은 Tez AM(쿼리 코디네이터) 수에 해당합니다. 작업자 노드 수와 동일하게 노드당 Tez AM 하나가 좋습니다. Tez AM 수는 LLAP 디먼 노드 수보다 많아도 됩니다. 주요 역할은 쿼리 실행을 조정하고 쿼리 계획 조각을 해당 LLAP 디먼에 할당하여 실행하는 것입니다. 더 높은 처리량을 얻을 수 있도록 LLAP 디먼 노드의 배수로 유지하는 것이 좋습니다.  

기본 HDInsight 클러스터는 4개의 작업자 노드에서 4개의 LLAP 디먼이 실행되므로 권장 값은 **4**입니다.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>tez.am.resource.memory.mb, hive.tez.container.size

`tez.am.resource.memory.mb`는 Tez 애플리케이션 마스터 크기를 정의합니다.  
권장 값은 **4096MB**입니다.

`hive.tez.container.size`는 Tez 컨테이너에 제공되는 메모리 양을 정의합니다. 이 값은 YARN 최소 컨테이너 크기(`yarn.scheduler.minimum-allocation-mb`)와 YARN 최대 컨테이너 크기 (`yarn.scheduler.maximum-allocation-mb`) 사이에서 설정해야 합니다.  
**4096MB**로 설정하는 것이 좋습니다.  

컨테이너당 프로세서 하나를 고려하여 프로세서당 메모리 양보다 작게 유지하는 것이 일반적입니다. LLAP 디먼에 사용할 메모리를 제공하기 전에 노드의 Tez AM 수에 제공하는 `Rreserve` 메모리입니다. 예를 들어 노드당 Tez AM 2개(각각 4GB)를 사용하는 경우 90GB 중 82GB를 LLAP 디먼에 제공하고 8GB는 Tez AM 2개를 위해 남겨 둡니다.

### <a name="yarnschedulercapacityrootllapcapacity"></a>yarn.scheduler.capacity.root.llap.capacity

이 값은 LLAP 큐에 제공되는 용량의 백분율을 나타냅니다. HDInsights 대화형 쿼리 클러스터는 총 용량의 90%를 LLAP 큐에 제공하며, 나머지 10%는 다른 컨테이너 할당의 기본 큐로 설정됩니다.  
D14 v2 작업자 노드의 경우 LLAP 큐에 권장하는 값은 **90**입니다.

### <a name="hivellapdaemonyarncontainermb"></a>hive.llap.daemon.yarn.container.mb

LLAP 디먼의 총 메모리 크기는 다음 구성 요소에 따라 다릅니다.

* YARN 컨테이너 크기 구성(`yarn.scheduler.maximum-allocation-mb`, `yarn.scheduler.maximum-allocation-mb`, `yarn.nodemanager.resource.memory-mb`)

* 실행기에서 사용하는 힙 메모리(Xmx)

    헤드룸을 뺀 후 사용할 수 있는 RAM 양입니다.  
    D14 v2 HDI 4.0의 경우 이 값은 (86GB - 6GB) = 80GB  
    D14 v2 HDI 3.6의 경우 이 값은 (84GB - 6GB) = 78GB

* 디먼당 오프힙 메모리 내 캐시(hive.llap.io.memory.size)

* 헤드룸

    Java VM 오버헤드(메타스페이스, 스레드 스택, gc 데이터 구조 등)에 사용되는 오프힙 메모리의 일부입니다. 이 부분은 힙 크기(Xmx)의 약 6%를 차지하는 것으로 관찰됩니다. 총 LLAP 디먼 메모리 크기의 6%로 계산하는 것이 안전합니다. SSD 캐시를 사용할 경우 LLAP 디먼이 모든 가용 메모리 내 공간을 힙에만 사용할 수 있기 때문입니다.  
    D14 v2의 경우 권장 값은 ceil(86GB x 0.06) ~= **6GB**입니다.  

디먼당 메모리 = [메모리 내 캐시 크기] + [힙 크기] + [헤드룸]입니다.

다음과 같이 계산할 수 있습니다.

노드당 Tez AM 메모리 = [(Tez AM 수/LLAP 디먼 노드 수) * Tez AM 크기].
LLAP 디먼 컨테이너 크기 = [YARN 최대 컨테이너 메모리의 90%] – [노드당 Tez AM 메모리].

D14 v2 작업자 노드 HDI 4.0의 경우 권장 값은 (90 - (1/1 * 4GB)) = **86GB**입니다.
(HDI 3.6의 경우 슬라이더 AM 용량으로 ~2GB를 남겨 두어야 하므로 권장 값은 **84GB**입니다.)  

### <a name="hivellapiomemorysize"></a>hive.llap.io.memory.size

이 구성은 LLAP 디먼의 캐시로 사용 가능한 메모리 양입니다. LLAP 디먼은 SSD를 캐시로 사용할 수 있습니다. `hive.llap.io.allocator.mmap`을 true로 설정하면 SSD 캐싱이 사용됩니다. D14 v2는 SSD ~800GB를 기본 제공하며, 대화형 쿼리 클러스터(LLAP)에 SSD 캐싱이 기본적으로 사용됩니다. 오프힙 캐시에 SSD 공간의 50%를 사용하도록 구성되어 있습니다.

D14 v2의 경우 권장 값은 **409600MB**입니다.  

SSD 캐싱을 사용하지 않는 다른 VM의 경우 보다 나은 성능을 얻을 수 있도록 LLAP 캐싱에 가용 RAM의 일부를 제공하는 것이 좋습니다. LLAP 디먼의 총 메모리 크기를 다음과 같이 조정하세요.  

총 LLAP 디먼 메모리 = [LLAP 캐시 크기] + [힙 크기] + [헤드룸].

워크로드에 가장 적합한 캐시 크기와 힙 크기로 조정하는 것이 좋습니다.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

이 구성은 LLAP 디먼마다 병렬로 작업을 실행할 수 있는 실행기 수를 제어합니다. 이 값은 사용 가능한 vCORE 수, 실행기마다 제공되는 메모리 양, LLAP 디먼마다 사용 가능한 총 메모리의 균형점입니다. 이 값을 코어 수와 최대한 가깝게 설정하는 것이 일반적입니다.

D14 v2의 경우 vCORE 16개가 있지만 모든 vCORE를 제공할 수 있는 것은 아닙니다. 작업자 노드는 NodeManager, DataNode, Metrics Monitor 등의 다른 서비스도 실행하며 이러한 서비스에도 사용 가능한 vCORE의 일부가 필요합니다. 이 값은 해당 노드에서 사용할 수 있는 총 vCORE의 75%까지 구성할 수 있습니다.

D14 v2의 경우 권장 값은 (.75 X 16) = **12**입니다.

실행기 하나마다 힙 공간 ~6GB를 남겨 두는 것이 좋습니다. 사용 가능한 LLAP 디먼 크기와 노드당 사용 가능한 vCORE 수에 따라 실행기 수를 조정합니다.  

### <a name="hivellapiothreadpoolsize"></a>hive.llap.io.threadpool.size

이 값은 실행기의 스레드 풀 크기를 지정합니다. 실행기는 지정된 값으로 고정되므로 LLAP 디먼당 실행기 수와 동일합니다.

D14 v2의 경우 이 값을 **12**로 설정하는 것이 좋습니다.

이 구성은 `yarn.nodemanager.resource.cpu-vcores` 값을 초과할 수 없습니다.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>hive.auto.convert.join.noconditionaltask.size

이 매개 변수를 적용하려면 `hive.auto.convert.join.noconditionaltask`를 사용하도록 설정해야 합니다. 이렇게 구성하면 사용자는 맵 조인을 수행하는 데 필요한 메모리에 적합하도록 테이블 크기를 지정할 수 있습니다. n방향 조인에 사용할 `tables/partitions`의 n-1 크기 합계가 구성된 값보다 작으면 맵 조인이 선택됩니다. 맵 조인으로 자동 변환하는 임계값을 계산할 때는 LLAP 실행기 메모리 크기를 사용해야 합니다.

D14 v2의 경우 이 값을 **2048MB**로 설정하는 것이 좋습니다.

이 값을 너무 낮게 설정하면 자동 변환 기능을 사용할 수 없으므로 워크로드에 적당한 값으로 조정하는 것이 좋습니다. 이 값을 너무 높게 설정하면 GC가 일시 중지될 수 있으며, 이로 인해 쿼리 성능에 부정적인 영향을 줄 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [게이트웨이 지침](gateway-best-practices.md)
* [Apache Tez 메모리 튜닝 - 단계별 과정 설명](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [LLAP의 맵 조인 메모리 크기 조정](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP - 단일 페이지 아키텍처 개요](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [Hive LLAP 자세히 살펴보기](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
