---
title: HDInsight Interactive Query 클러스터(LLAP) 크기 조정 가이드
description: LLAP 크기 조정 가이드
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: ca3ba61de13e0e451b43dc9c8ea40db33fed859a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869670"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight Interactive Query 클러스터(Hive LLAP) 크기 조정 가이드

이 문서에서는 일반적인 워크로드에서 적절한 성능에 도달하기 위해 HDInsight Interactive Query 클러스터(Hive LLAP 클러스터)의 크기를 조정하는 방법을 알아봅니다. 이 문서에 제공된 권장 사항은 일반적인 지침이며 워크로드에 따라 특별한 조정이 필요할 수 있습니다.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**HDInsight Interactive Query 클러스터(LLAP)용 Azure 기본 VM 유형**

| 노드 유형      | 인스턴스 | 크기     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | vCPU 8개, 56GB RAM, 400GB SSD   |
| 작업자   | **D14 v2**        | **vCPU 16개, 112GB RAM, 800GB SSD**       |
| ZooKeeper   | A4 v2        | vCPU 4개, 8GB RAM, 40GB SSD       |

***참고: 모든 권장 구성 값은 D14 v2 유형 작업자 노드 기준입니다.***  

### <a name="configuration"></a>**구성:**    
| 구성 키      | 권장되는 값  | Description |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400(MB) | 노드의 모든 YARN 컨테이너에 제공되는 총 메모리 양(MB) | 
| yarn.scheduler.maximum-allocation-mb | 102400(MB) | RM의 모든 컨테이너 요청에 제공되는 최대 할당량(MB)입니다. 이 값보다 높은 메모리 요청은 적용되지 않습니다. |
| yarn.scheduler.maximum-allocation-vcores | 12 |Resource Manager의 모든 컨테이너 요청에 제공되는 최대 CPU 코어 수입니다. 이 값보다 높은 요청은 적용되지 않습니다. |
| yarn.nodemanager.resource.cpu-vcores | 12 | 컨테이너에 할당할 수 있는 NodeManager당 CPU 코어 수입니다. |
| yarn.scheduler.capacity.root.llap.capacity | 85 (%) | LLAP 큐에 제공되는 YARN 용량 할당  |
| tez.am.resource.memory.mb | 4096(MB) | tez AppMaster에 사용될 메모리 양(MB) |
| hive.server2.tez.sessions.per.default.queue | <number_of_worker_nodes> |hive.server2.tez.default.queues에 명명된 각 큐의 세션 수입니다. 이 수는 쿼리 코디네이터 수(Tez AM)에 해당합니다. |
| hive.tez.container.size | 4096(MB) | 지정된 Tez 컨테이너 크기(MB) |
| hive.llap.daemon.num.executors | 19 | LLAP 디먼당 실행기 수 | 
| hive.llap.io.threadpool.size | 19 | 실행기의 스레드 풀 크기 |
| hive.llap.daemon.yarn.container.mb | 81920 (MB) | 개별 LLAP 디먼에서 사용하는 총 메모리(MB)(디먼당 메모리)
| hive.llap.io.memory.size | 242688 (MB) | SSD 캐시를 사용하는 경우 LLAP 디먼당 캐시 크기(MB) |
| hive.auto.convert.join.noconditionaltask.size | 2048(MB) | 맵 조인을 수행할 메모리 크기(MB) |

### <a name="llap-architecturecomponents"></a>**LLAP 아키텍처/구성 요소:**  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png " alt-text="' LLAP 아키텍처/구성 요소 '" border="true":::

### <a name="llap-daemon-size-estimations"></a>**LLAP 디먼 크기 예측:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. 노드의 모든 컨테이너에 대한 총 YARN 메모리 할당 결정**    
구성: ***yarn.nodemanager.resource.memory-mb***  

이 값은 각 노드의 YARN 컨테이너에서 사용할 수 있는 메모리의 최대 합계(MB)를 나타냅니다. 지정된 값은 해당 노드의 실제 메모리 총량보다 작아야 합니다.   
노드의 모든 YARN 컨테이너에 대 한 총 메모리 = (전체 실제 메모리 – OS + 기타 서비스의 메모리)  
이 값을 가용 RAM 크기의 90% 이하로 설정합니다.  
D14 v2의 경우 권장 값은 **102400MB** 입니다. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. YARN 컨테이너 요청당 최대 메모리 양 결정**  
구성: ***yarn.scheduler.maximum-allocation-mb***

이 값은 Resource Manager의 모든 컨테이너 요청에 제공되는 최대 할당량(MB)을 나타냅니다. 지정된 값보다 높은 메모리 요청은 적용되지 않습니다. Resource Manager는 *yarn.scheduler.minimum-allocation-mb* 단위로 컨테이너에 메모리를 제공할 수 있으며, *yarn.scheduler.maximum-allocation-mb* 에 지정된 크기를 초과할 수 없습니다. 지정된 값은 *yarn.nodemanager.resource.memory-mb* 에 지정된 노드의 모든 컨테이너에 대해 주어진 총 메모리를 초과하지 않아야 합니다.    
D14 v2 작업자 노드의 경우 권장 값은 **102400MB** 입니다.

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. YARN 컨테이너 요청당 최대 vCore 양 결정**  
구성: ***yarn.scheduler.maximum-allocation-vcores***  

이 값은 Resource Manager의 모든 컨테이너 요청에 제공되는 최대 가상 CPU 코어 수입니다. 이 값보다 많은 수의 vCore를 요청하면 적용되지 않습니다. YARN 스케줄러의 글로벌 속성입니다. LLAP 디먼 컨테이너의 경우 이 값을 사용 가능한 총 vCore의 75%로 설정할 수 있습니다. 나머지 25%는 NodeManager, DataNode 및 작업자 노드에서 실행되는 기타 서비스를 위해 남겨 두어야 합니다.  
D14 v2 VM에는 vCore가 16개 있으며 LLAP 디먼 컨테이너에서 vCore 총 16개 중 75%를 사용할 수 있습니다.  
D14 v2의 경우 권장되는 값은 **12** 입니다.  

#### <a name="4-number-of-concurrent-queries"></a>**4. 동시 쿼리 수**  
구성: ***hive.server2.tez.sessions.per.default.queue***

이 구성 값은 병렬로 시작할 수 있는 Tez 세션 수를 결정합니다. 이 Tez 세션은 "hive.server2.tez.default.queues"에 지정된 각 큐에 대해 시작됩니다. Tez AM(쿼리 코디네이터) 수에 해당합니다. 작업자 노드 수와 동일한 것이 좋습니다. Tez AM 수는 LLAP 디먼 노드 수보다 많아도 됩니다. Tez AM의 주요 역할은 쿼리 실행을 조정하고 쿼리 계획 조각을 해당 LLAP 디먼에 할당하여 실행하는 것입니다. 처리량을 높이려면 이 값을 LLAP 데몬 노드의 배수로 유지하십시오.  

기본 HDInsight 클러스터는 4개의 작업자 노드에서 4개의 LLAP 디먼이 실행되므로 권장 값은 **4** 입니다.  

**Hive 구성 변수에 대 한 Ambari UI 슬라이더 `hive.server2.tez.sessions.per.default.queue` :**

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png " alt-text="' LLAP 최대 동시 쿼리 '" border="true":::

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Tez 컨테이너 및 Tez 애플리케이션 마스터 크기**    
구성: ***tez.am.resource.memory.mb, hive.tez.container.size***  

*tez.am.resource.memory.mb* - Tez 애플리케이션 마스터 크기를 정의합니다.  
권장 값은 **4096MB** 입니다.
   
*hive.tez.container.size* -Tez 컨테이너에 제공되는 메모리 양을 정의합니다. 이 값은 YARN 최소 컨테이너 크기(*yarn.scheduler.minimum-allocation-mb*)와 YARN 최대 컨테이너 크기(*yarn.scheduler.maximum-allocation-mb*) 사이에서 설정해야 합니다. LLAP 디먼 실행기는 이 값을 사용하여 실행기당 메모리 사용량을 제한합니다.  
권장 값은 **4096MB** 입니다.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. LLAP 큐 용량 할당**   
구성: ***yarn.scheduler.capacity.root.llap.capacity***  

이 값은 LLAP 큐에 제공되는 용량의 백분율을 나타냅니다. 용량 할당은 YARN 큐 구성 방식에 따라 워크로드마다 다른 값을 가질 수 있습니다. 워크로드가 읽기 전용 작업인 경우 용량의 90%로 설정하면 작동합니다. 그러나 워크 로드가 관리 되는 테이블을 사용 하 여 업데이트/삭제/병합 작업과 혼합 되어 있는 경우 llap 큐에 대해 85%의 용량을 제공 하는 것이 좋습니다. 나머지 15% 용량은 압축 등의 다른 작업에서 사용 하 여 기본 큐에서 컨테이너를 할당할 수 있습니다. 이렇게 하면 기본 큐의 작업이 YARN 리소스를 박탈하지 않습니다.    

D14v2 worker 노드의 경우 llap 큐에 권장 되는 값은 **85** 입니다.     
(읽기 전용 워크로드의 경우 적절하게 90까지 늘릴 수 있습니다.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP 디먼 컨테이너 크기**    
구성: ***hive.llap.daemon.yarn.container.mb***  
   
LLAP 디먼은 각 작업자 노드에서 YARN 컨테이너로 실행됩니다. LLAP 디먼 컨테이너의 총 메모리 크기는 다음 요소에 따라 다릅니다.    
*  YARN 컨테이너 크기의 구성(yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  노드의 Tez AM 수
*  노드의 모든 컨테이너 및 LLAP 큐 용량에 대해 구성된 총 메모리  

Tez AM(Tez 애플리케이션 마스터)에 필요한 메모리는 다음과 같이 계산할 수 있습니다.  
Tez AM은 쿼리 코디네이터 역할을 하며, Tez AMs의 수는 처리할 동시 쿼리 수에 따라 구성 해야 합니다. 이론적으로는 작업자 노드당 하나의 Tez AM을 고려할 수 있습니다. 그러나 작업자 노드에는 Tez AM이 둘 이상 표시 될 수 있습니다. 계산을 위해 모든 LLAP 데몬 노드/작업자 노드에 대해 Tez AMs의 균일 한 분포를 가정 합니다.
Tez AM당 4GB의 메모리를 사용하는 것이 좋습니다.  

Hive 구성 ***hive***. c s. t e r. n a t e.  
Ambari UI에서 env 변수 ***num_llap_nodes_for_llap_daemons*** 로 지정 된 llap 디먼 노드 수입니다.  
Tez AM 컨테이너 크기 = Tez 구성 ***tez.***. p. m.  

노드 당 tez AM 메모리 = **(** ceil **(** llap 데몬 노드 수의 tez AMs **/** 수 **)** **x** tez AM 컨테이너 크기 **)**  
D14 v2의 경우 기본 구성에는 Tez AM 4개와 LLAP 디먼 노드 4개가 있습니다.  
노드당 Tez AM 메모리 = (ceil(4/4) x 4 GB) = 4GB

작업자 노드당 LLAP 큐에 사용할 수 있는 총 메모리는 다음과 같이 계산 될 수 있습니다.  
이 값은 노드 (*YARN*)의 모든 YARN 컨테이너에 사용할 수 있는 총 메모리 양과 llap 큐에 대해 구성 된 용량의 백분율 (*YARN*)에 따라 달라 지 게 결정 됩니다.  
작업자 노드의 LLAP 큐에 대한 총 메모리 = 노드의 모든 YARN 컨테이너에 사용 가능한 총 메모리 x LLAP 큐에 대한 용량 백분율.  
D14 v 2의 경우이 값은 (100 GB x 0.85) = 85 GB입니다.

LLAP 디먼 컨테이너 크기는 다음과 같이 계산됩니다.

**LLAP 디먼 컨테이너 크기 = (영역 노드의 LLAP 큐에 대 한 총 메모리)-(노드당 Tez AM memory)-(서비스 마스터 컨테이너 크기)**  
작업자 노드 중 하나에서 생성 된 클러스터에는 서비스 마스터 (LLAP 서비스용 응용 프로그램 마스터)가 하나 뿐입니다. 계산 용도로는 작업자 노드당 하나의 서비스 마스터를 고려 합니다.  
D14 v2 worker 노드의 경우 HDI 4.0-권장 되는 값은 (85 GB-4gb-1gb)) = **80 GB**   
HDI 3.6의 경우 슬라이더 AM에 대 한 추가 ~ 2gb를 예약 해야 하므로 권장 값은 **79 GB** 입니다.  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. LLAP 디먼 당 실행기 수 결정**  
구성: ***hive.llap.daemon.num.executors** _, _ *_hive_* . a. 풀.*

***hive.llap.daemon.num.executors***:   
이 구성은 LLAP 디먼당 병렬로 작업을 실행할 수 있는 실행기 수를 제어합니다. 이 값은 vcores 수, 실행자 당 사용 되는 메모리 양 및 LLAP 디먼 컨테이너에 사용할 수 있는 총 메모리 양에 따라 달라 집니다.    실행자 수는 작업자 노드당 사용 가능한 vcore의 120% 초과 구독 수 있습니다. 그러나 실행 기 당 필요한 메모리와 LLAP 디먼 컨테이너 크기를 기준으로 메모리 요구 사항을 충족 하지 않는 경우에는 조정 해야 합니다.

각 실행자는 Tez 컨테이너와 같으며 4GB (Tez 컨테이너 크기)의 메모리를 사용할 수 있습니다. LLAP 디먼의 모든 실행자는 동일한 힙 메모리를 공유 합니다. 모든 실행 기에서 메모리 집약적 작업을 동시에 실행 하는 것은 아니므로 실행 기 당 75%의 Tez 컨테이너 크기 (4gb)를 고려할 수 있습니다. 이러한 방식으로 증가 하는 병렬 처리를 위해 각 실행자에 더 많은 메모리 (예: 3gb)를 부여 하 여 실행 기 수를 늘릴 수 있습니다. 그러나 대상 작업에 대해이 설정을 조정 하는 것이 좋습니다.

D14 v2 VM에는 vCore가 16개 있습니다.
D14 v 2의 경우 실행 기 수에 권장 되는 값은 (실행 기 당 3gb를 고려 하는 각 작업자 노드의 16 vcore x 120% ~ = **19** )입니다.

***hive.llap.io.threadpool.size***:   
이 값은 실행기의 스레드 풀 크기를 지정합니다. 실행기는 지정된 값으로 고정되므로 LLAP 디먼당 실행기 수와 동일합니다.    
D14 v 2의 경우 권장 되는 값은 **19** 입니다.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. LLAP 디먼 캐시 크기 결정**  
구성: ***hive.llap.io.memory.size***

LLAP 디먼 컨테이너 메모리는 다음 구성 요소로 구성됩니다.
*  헤드룸
*  실행기에서 사용하는 힙 메모리(Xmx)
*  디먼당 메모리 내 캐시(오프힙 메모리 크기, SSD 캐시가 활성화된 경우 적용할 수 없음)
*  메모리 내 캐시 메타데이터 크기(SSD 캐시가 활성화된 경우에만 적용 가능)

**헤드룸 크기**: 이 크기는 Java VM 오버헤드(메타스페이스, 스레드 스택, gc 데이터 구조 등)에 사용되는 오프힙 메모리의 일부를 나타냅니다. 일반적으로 이 오버헤드는 힙 크기(Xmx)의 약 6%입니다. 안전하게 사용할 수 있도록, 이 값을 총 LLAP 디먼 메모리 크기의 6%로 계산할 수 있습니다.  
D14 v 2의 경우 권장 되는 값은 ceil (80gb x 0.06) ~ = **4gb** 입니다.  

**힙 크기(Xmx)** : 모든 실행기에 사용할 수 있는 힙 메모리의 양입니다.
총 힙 크기 = 실행 기 x 3gb의 수  
D14 v 2의 경우이 값은 19 x 3gb = **57 GB** 입니다.  

`Ambari environment variable for LLAP heap size:`

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png " alt-text="' LLAP 힙 크기 '" border="true":::

SSD 캐시를 사용 하지 않도록 설정 하는 경우 메모리 내 캐시는 LLAP 디먼 컨테이너 크기에서 여유 크기와 힙 크기를 가져온 후 남은 메모리의 양입니다.

SSD 캐시가 활성화된 경우 캐시 크기 계산이 다릅니다.
*hive.llap.io.allocator.mmap* = true로 설정하면 SSD 캐싱이 활성화됩니다.
SSD 캐시가 활성화되면 메모리의 일부는 SSD 캐시의 메타데이터를 저장하는 데 사용됩니다. 메타 데이터는 메모리에 저장 되며 SSD 캐시 크기의 ~ 8% 여야 합니다.   
SSD 캐시 메모리 내 메타 데이터 크기 = LLAP 데몬 컨테이너 크기-(헤드 공간 + 힙 크기)  
D14 v2의 경우 HDI 4.0, SSD 캐시 메모리 내 메타 데이터 크기 = 80 GB-(4gb + 57 g b) = **19 g** b  
D14 v2의 경우 HDI 3.6, SSD 캐시 메모리 내 메타 데이터 크기 = 79 GB-(4gb + 57 g b) = **18GB**

SSD 캐시 메타데이터를 저장하는 데 사용 가능한 메모리 크기가 있으면 지원할 수 있는 SSD 캐시 크기를 계산할 수 있습니다.  
SSD 캐시의 메모리 내 메타 데이터 크기 = LLAP 디먼 컨테이너 크기-(헤드 실 + 힙 크기) = 19 g b     
SSD 캐시 크기 = SSD 캐시의 메모리 내 메타 데이터 크기 (19 g b)/0.08 (8%)  

D14 v2 및 HDI 4.0의 경우 권장 되는 SSD 캐시 크기 = 19 g b/0.08 ~ = **237 GB**  
D14 v2 및 HDI 3.6의 경우 권장 되는 SSD 캐시 크기 = 18gb/0.08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. 맵 조인 메모리 조정**   
구성: ***hive.auto.convert.join.noconditionaltask.size***

이 매개 변수를 적용하려면 *hive.auto.convert.join.noconditionaltask* 를 활성화해야 합니다.
이 구성은 다른 실행자에서 메모리의 초과 구독을 고려 하 여 더 많은 맵 조인 변환을 허용 하도록 Hive 최적화 프로그램의 MapJoin 선택 임계값을 결정 합니다. 실행 기 당 3GB를 고려 하 여이 크기를 3GB로 초과 구독 수 있지만 일부 힙 메모리는 다른 작업에 의해 정렬 버퍼, 순서 섞기 등에 사용 될 수도 있습니다.   
따라서 D14 v2의 경우 실행 기 당 3gb 메모리를 사용 하는 경우이 값을 **2048** 로 설정 하는 것이 좋습니다.  

(참고:이 값에는 워크 로드에 적합 한 조정이 필요할 수 있습니다. 이 값을 너무 낮게 설정하면 autoconvert 기능이 사용되지 않을 수 있습니다. 또한 너무 높게 설정 하면 메모리 예외 또는 GC 일시 중지로 인해 성능이 저하 될 수 있습니다.  

#### <a name="11-number-of-llap-daemons"></a>**11. LLAP 디먼 수**
Ambari 환경 변수: ***num_llap_nodes, num_llap_nodes_for_llap_daemons***  

**num_llap_nodes** -HIVE llap 서비스에서 사용 하는 노드 수를 지정 합니다. 여기에는 llap 디먼, Llap 서비스 마스터 및 TEZ AM (Application master)을 실행 하는 노드가 포함 됩니다.  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png " alt-text="' LLAP 서비스에 대 한 노드 수 '" border="true":::  

**num_llap_nodes_for_llap_daemons** 지정 된 노드 수는 llap 디먼에만 사용 됩니다. LLAP 디먼 컨테이너 크기는 최대 맞춤 노드로 설정 되어 있으므로 각 노드에 하나의 LLAP 데몬이 생성 됩니다.

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png " alt-text="' LLAP 디먼에 대 한 노드 수 '" border="true":::

두 값을 모두 대화형 쿼리 클러스터의 작업자 노드 수와 동일 하 게 유지 하는 것이 좋습니다.

### <a name="considerations-for-workload-management"></a>**워크 로드 관리에 대 한 고려 사항**  
LLAP에 대 한 워크 로드 관리를 사용 하도록 설정 하려는 경우 워크 로드 관리가 예상 대로 작동할 수 있도록 충분 한 용량을 예약 해야 합니다. 워크 로드 관리에는 큐 외에도 사용자 지정 YARN 큐가 구성 되어 있어야 합니다 `llap` . 작업 요구 사항에 따라 llap 큐와 워크 로드 관리 큐 간에 총 클러스터 리소스 용량을 나누어야 합니다.
리소스 계획이 활성화 되 면 작업 관리에서 tez AMs (응용 프로그램 마스터)를 생성 합니다.
참고:

* 리소스 계획을 활성화 하 여 생성 된 tez AMs는에 지정 된 대로 작업 관리 큐의 리소스를 사용 `hive.server2.tez.interactive.queue` 합니다.  
* Tez AMs의 수는 `QUERY_PARALLELISM` 리소스 계획에 지정 된의 값에 따라 달라 집니다.  
* 워크 로드 관리가 활성화 되 면 llap 큐의 Tez AMs는 사용 되지 않습니다. 작업 관리 큐의 Tez AMs만 쿼리 조정에 사용 됩니다. `llap`작업 관리를 사용 하지 않도록 설정한 경우 큐의 tez AMs를 사용 합니다.
 
예를 들어 총 클러스터 용량 = 100 g b 메모리, LLAP, 워크 로드 관리 및 기본 큐 간에 다음과 같이 구분 됩니다.
 - llap 큐 용량 = 70
 - 워크 로드 관리 큐 용량 = 20gb
 - 기본 큐 용량 = 10gb

워크 로드 관리 큐 용량의 20gb를 사용 하면 리소스 계획에서 `QUERY_PARALLELISM` 5로 값을 지정할 수 있습니다. 즉, 워크 로드 관리에서 각각 4gb 컨테이너 크기를 가진 5 개의 Tez AMs를 시작할 수 있습니다. `QUERY_PARALLELISM`가 용량 보다 큰 경우 상태에서 Tez AMs가 응답 하지 않는 것을 볼 수 있습니다 `ACCEPTED` . Hiveserver2 Interactive는 상태가 아닌 Tez AMs에 쿼리 조각을 제출할 수 없습니다 `RUNNING` .


#### <a name="next-steps"></a>**다음 단계**
이 값을 설정해도 문제가 해결되지 않으면 다음 중 하나를 수행하세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.  

* ##### <a name="other-references"></a>**기타 참조:**
  * [기타 LLAP 속성 구성](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Hive 서버 힙 크기 구성](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [LLAP의 맵 조인 메모리 크기 조정](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez 실행 엔진 속성](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [Hive LLAP 자세히 살펴보기](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)