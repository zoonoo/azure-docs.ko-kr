---
title: "Azure Data Lake Store Storm 성능 조정 지침 | Microsoft Docs"
description: "Azure Data Lake Store Storm 성능 조정 지침"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: 518d586a921926874cd959587ad2730ad346df71
ms.openlocfilehash: cae3f0192d6b0e6204ede4c1855b731a2ffa4ae1


---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>HDInsight의 Storm 및 Azure Data Lake Store에 대한 성능 조정 지침

Storm 토폴로지의 성능을 조정할 때 고려해야 할 몇 가지 요소가 있습니다.  Spout 및 Bolt(작업이 I/O 또는 메모리 집약적인지 여부에 따름)에서 수행한 작업의 특징을 이해하는 것이 중요합니다.

## <a name="prerequisites"></a>필수 조건 

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
* **Azure 데이터 레이크 저장소 계정**. 만드는 방법에 대한 지침은 [Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md) 
* **Azure HDInsight 클러스터** 입니다. [Data Lake Store가 있는 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다. 
* **Azure Data Lake Store에서 실행 중인 Storm 클러스터**.  자세한 내용은 [HDInsight의 Storm](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview)을 참조하세요. 
* **ADLS에서 성능 조정 지침**.  일반적인 성능 개념은 [Data Lake Store 성능 조정 지침](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)을 참조하세요.  

**토폴로지의 병렬 처리 조정**

Azure Data Lake Store와의 I/O 동시성을 늘리면 더 나은 성능을 얻을 수 있습니다.  
Storm 토폴로지에는 병렬 처리를 결정하는 일련의 구성이 포함되어 있습니다.
* 작업자 프로세스 수: 작업자는 VM 간에 균일하게 분산됩니다.
* Spout 실행자 인스턴스 수
* Bolt 실행자 인스턴스 수
* Spout 작업 수
* Bolt 태스크 수

예를 들어 VM 4개와 작업자 프로세스 4개, Spout 실행기 32개, Spout 태스크 32개, Bolt 실행기 256개, Bolt 태스크 512개가 있는 클러스터에는 다음 내용이 적용됩니다.

작업자 노드인 각 감독자는 4개의 Spout 스레드와 64개의 Bolt 스레드를 관리하는 단일 작업자 JVM을 포함하게 됩니다. 각 스레드 내에서 태스크는 순차적으로 실행됩니다. 위의 구성에서 각 Spout 스레드는 태스크 1개를 포함하며 각 Bolt 스레드는 태스크 2개를 포함합니다.

Storm에는 다양한 구성 요소가 관련되어 있으며 여기서는 이 구성 요소가 사용자의 병렬 처리 수준에 어떻게 영향을 주는지 살펴봅니다.
* 작업을 제출 및 관리하는 데 헤드 노드(Storm에서 Nimbus라고 함)가 사용됩니다. 이러한 노드는 병렬 처리 수준에 영향을 주지 않습니다.
* 감독자 노드 - Azure HDInsight에서 작업자 노드 Azure VM에 해당합니다.
* 작업자 태스크는 VM에서 실행 중인 Storm 프로세스입니다. 각 작업자 태스크는 java JVM 인스턴스에 해당합니다. Storm은 사용자가 지정한 작업자 프로세스 수를 작업자 노드에 최대한 균등하게 배포합니다.
* Spout 및 Bolt 실행기 인스턴스: 각 실행기 인스턴스는 작업자(JVM) 내에서 실행 중인 스레드에 해당합니다.
* Storm 태스크: 이러한 각 스레드가 실행되는 논리적 태스크입니다. 병렬 처리 수준은 변경되지 않으므로 실행기당 여러 태스크가 필요한지 평가해야 합니다.

## <a name="guidance"></a>인도

Azure Data Lake를 사용할 때는 다음을 수행하는 경우 최상의 성능을 얻을 수 있습니다.
* 작은 크기의 추가 항목을 보다 큰 크기로 병합(이상적으로 4MB 크기)
* 가능한 동시 요청을 많이 수행. 각 Bolt 스레드는 읽기 차단 작업을 수행하므로 코어당 8-12개 스레드 범위 내에 공간을 포함하려고 하므로 NIC 및 CPU를 계속해서 잘 활용할 수 있습니다.  VM 크기가 크면 더 많은 동시 요청이 가능합니다.  

## <a name="example"></a>예

D13v2 Azure VM과 함께 8개의 작업자 노드 클러스터가 있다고 가정해 보겠습니다.  D13v2 VM에는 8개의 코어가 있으므로 8개 작업자 노드에는 총 64개의 코어가 있습니다.

코어당 8개의 Bolt 스레드를 수행한다고 가정해 보겠습니다. 64개의 코어가 제공되므로 총 512개의 Bolt 실행기 인스턴스(즉, 스레드)가 필요합니다. 이 경우 VM당 JVM&1;개로 시작하고 JVM 내에서 스레드 동시성을 주로 사용하여 동시성을 달성한다고 가정해 보겠습니다. 즉, 8개의 작업자 태스크(Azure VM당 하나)와 512개의 Bolt 실행기가 필요합니다. 이 구성이 정해지면 Storm이 작업자를 작업자 노드(즉, 감독자 노드) 간에 균등하게 배포하려고 시도합니다(각 작업자 노드에 JVM 하나 제공). 이제 감독자 내에서 Storm은 실행기를 감독자 간에 균등하게 배포하려고 시도하여 각 감독자(즉, JVM)에 8개의 스레드를 제공합니다.

## <a name="further-tuning"></a>추가 조정
기본 토폴로지를 만든 후에는 매개 변수를 수정할 것인지 고려할 수 있습니다.
* **작업자 노드당 JVM 수:** 데이터 구조(예: 조회 테이블)가 크고 메모리에서 호스트하는 경우 각 JVM에는 별도의 복사본이 필요한 반면, JVM 수가 적으면 여러 스레드에 대해 해당 구조를 사용할 수 있습니다. Bolt I/O의 경우, JVM 수는 해당 JVM 간에 추가된 스레드 수와 큰 차이가 없습니다. 간소화를 위해 작업자당&1;개의 JVM을 사용하는 것이 좋으나 Bolt에서 수행하는 작업과 사용자가 필요로 하는 응용 프로그램 처리에 따라 수를 수정할지 여부를 평가해야 할 수 있습니다.
* **Spout 실행기 수:** 여기에 있는 예에서는 Azure Data Lake로 쓰기를 위해 Bolt를 사용하므로 Spout 수는 Bolt 성능과 직접 관련이 없습니다. 하지만 Spout에서 발생하는 처리 또는 I/O 양에 따라 최적의 성능을 얻기 위해 Spout을 조정하려고 합니다. 적어도 Bolt를 계속 사용할 수 있을 만큼 충분한 Spout이 있어야 합니다. 즉, Spout의 출력 속도가 Bolt의 처리량과 일치해야 합니다. 실제 구성은 Spout에 따라 달라지며 이에 대한 설명은 본 문서에서는 다루지 않습니다.
* **태스크 수:** 각 Bolt는 단일 스레드로 실행됩니다. Bolt당 추가 태스크는 어떠한 동시성도 제공하지 않습니다. 이익이 되는 경우는 튜플을 승인하는 프로세스가 Bolt 실행 시간의 상당 부분을 차지하는 경우 뿐입니다. Bolt에서 승인을 보내기 전에 여러 튜플을 더 큰 추가로 그룹화하는 것이 좋습니다. 따라서 대부분의 경우 여러 태스크를 사용할 경우 이점이 없습니다.
* **로컬 또는 순서 섞기 그룹화:** 이 설정을 사용하면 동일한 작업자 프로세스 내에서 튜플이 Bolt로 전송됩니다. 그러면 프로세스 간 통신과 네트워크 호출이 줄어듭니다. 대부분의 토폴로지에 사용하는 것이 좋습니다.

시작 방법으로 기본 시나리오로 시작하여 사용자 자신의 데이터로 테스트한 후 위에 설명된 매개 변수를 수정하여 최적의 성능을 달성하는 것이 좋습니다.

## <a name="tuning-the-spout"></a>Spout 만들기

**튜플 시간 제한**.

topology.message.timeout.secs – 이 설정은 몇 시간 후에 완료 및 수신 확인할 메시지를 실패로 간주할지 결정합니다.

**작업자 프로세스당 최대 메모리**

Worker.childopts - 이 설정을 통해 java 작업자에 대해 추가 명령줄 매개 변수를 지정할 수 있습니다. 여기서 가장 일반적으로 사용된 설정은 XmX이며 JVM 힙에 할당된 최대 메모리를 결정합니다.

**보류 중인 최대 Spout**

Topology.max.spout.pending - 이 구성은 모든 지정 시간에 Spout 스레드당 처리할 수 있는(토폴로지의 모든 노드에서 아직 승인되지 않은) 튜플 수를 결정합니다.

수행할 수 있는 적절한 계산은 각 튜플의 크기에 대한 예상치를 구하는 것입니다. 그런 다음 한 개의 Spout 스레드에 지정된 메모리 양을 파악합니다. 스레드에 할당된 총 메모리를 이 값으로 나누어 보류 중인 최대 Spout 매개 변수에 대한 상한을 제공합니다.

## <a name="tuning-the-bolt"></a>Bolt 조정
ADLS에 기록할 때는 크기 동기화 정책(클라이언트 쪽에서 버퍼)을 4MiB로 설정하는 것이 좋습니다.  그러면 버퍼 크기가 위의 값일 때만 플러시 또는 hsync()가 수행됩니다.  작업자 VM의 ADLS 드라이버는 사용자가 hsync()를 명시적 수행하지 않는 한, 이 버퍼링을 자동으로 수행합니다.

기본 ADLS storm Bolt에는 이 매개 변수를 조정하는 데 사용할 수 있는 크기 동기화 정책 매개 변수(fileBufferSize)가 포함됩니다.

I/O 집약적인 토폴로지에서 각 Bolt 스레드는 자체 파일에 기록하고 파일 회전 정책(fileRotationSize)을 설정하는 것이 좋습니다.  파일이 특정 크기에 도달하면 스트림이 자동으로 플러시되며 새 파일이 기록됩니다.  회전에 권장되는 파일 크기는 1GB입니다.

**승인 시기:** Storm에서 Spout는 튜플이 Bolt에 의해 명시적으로 승인될 때까지 튜플을 계속 사용합니다.  튜플을 Bolt에서 읽어 왔지만 아직 승인되지 않은 경우 Bolt가 Azure Data Lake Store 백 엔드에 유지된다는 보장이 없습니다.  튜플이 승인되면 Spout에서 Bolt의 지속성을 보장할 수 있으므로 읽어 오는 원본이 무엇이든지 원본 데이터를 삭제할 수 있습니다.  

**ADLS에서 최상의 성능을 위해:** Bolt에서 튜플 데이터 중 4MB를 버퍼링한 후 4MB 쓰기 1번으로 ADLS 백 엔드에 기록하는 것이 좋습니다. hflush()를 호출하여 저장소에 데이터를 성공적으로 기록했으면 Bolt가 데이터를 Spout으로 다시 승인할 수 있습니다. 여기에 제공된 Bolt 예제에서 이 작업을 수행합니다. 또한 hflush() 호출을 수행하고 튜플을 승인하기 전에 많은 수의 튜플을 보유하는 것도 가능합니다. 하지만 이 경우 Spout이 보유해야 하는 진행 중인 튜플 수가 증가하므로 JVM당 필요한 메모리 양이 늘어납니다.

응용 프로그램에는 기타 성능 이외의 이유로 더욱 자주(4MB 미만의 데이터 크기) 승인을 요청해야 할 수 있습니다. 하지만 그러면 저장소 백 엔드에 대한 I/O 처리량에 영향을 줄 수 있으므로 고객은 Bolt의 I/O 성능에 대해 장단점을 신중하게 고려해야 합니다.

들어오는 튜플 비율이 충분히 높지 않으므로 4MB 버퍼를 채우는 데 시간이 오래 걸리고 다양한 방법으로 이를 완화하는 것을 고려할 수 있습니다.
* Bolt 수를 줄이면 채울 4MB 버퍼 수가 줄어듭니다.
* 시간 기반 또는 개수 기반 정책을 사용할 경우 x 플러시 또는 y 밀리초마다 hflush()가 트리거되고 지금까지 누적된 튜플이 다시 승인됩니다.

이 경우 처리량은 낮지만 이벤트 속도가 느리고 최대 처리량이 최대 목표는 아닙니다.  위의 이러한 완화 노력으로 튜플이 저장소로 흐르는 데 소요되는 종단 간 시간을 줄일 수 있으며 이것은 낮은 이벤트 속도에서도 실시간 파이프라인을 원하는 경우 문제가 될 수 있습니다.  또한 들어오는 튜플 비율이 낮은 경우 버퍼링 또는 처리 중에 튜플이 시간 초과되지 않도록 topology.message.timeout_secs 매개 변수를 조정할 수도 있습니다.

## <a name="interpreting-storm-ui"></a>Storm UI 해석  
토폴로지가 실행 중인 동안에는 storm UI에서 이를 모니터링할 수 있습니다. UI를 확인할 경우 살펴볼 주요 매개 변수입니다.

* **총 프로세스 실행 대기 시간** –&1;개의 튜플이 Spout에 의해 보내지는 데 소요되어 Bolt에 의해 처리되고 승인되는 평균 시간입니다.

* **총 Bolt 프로세스 대기 시간** – 승인을 받기까지 Bolt에서 튜플에 소요된 평균 시간입니다.

* **총 Bolt 프로세스 대기 시간** – Execute 메서드에서 Bolt에 소요된 평균 시간입니다.

* **오류 수** – 시간 초과되기 전에 완전히 처리되지 않은 튜플 수를 나타냅니다.

* **용량** – 시스템이 얼마나 사용 중인지를 측정한 값입니다. 1이면 Bolt가 가장 빠르게 작동 중입니다. 1 미만이면 병렬 처리를 늘립니다. 1보다 크면 병렬 처리를 줄입니다.

## <a name="common-troubleshooting-scenarios"></a>일반적인 문제 해결 시나리오
* **시간 초과된 튜플 수가 많음** – 토폴로지에서 각 노드를 확인하여 병목 현상이 발생한 위치를 확인합니다. 가장 일반적인 이유는 Bolt에서 Spout을 따라갈 수 없어서 처리 대기 중인 동안 내부 버퍼에서 튜플이 막히기 때문입니다. 시간 초과 값을 늘리거나 보류 중인 최대 Spout을 줄이세요.

* **총 프로세스 실행 대기 시간은 높으나 Bolt 프로세스 대기 시간은 낮음** – 이 경우 튜플이 충분히 빠르게 처리되지 않습니다. acker 수가 충분한지 확인하세요. Bolt가 처리를 시작하기 전에 큐에서 너무 오래 대기 중인 것도 원인이 될 수 있습니다. 보류 중인 최대 Spout을 줄이세요.

* **Bolt 실행 대기 시간 높음** – Bolt의 execute() 메서드에 시간이 너무 오래 소요되기 때문입니다. 코드를 최적화하고 쓰기 크기/플러시 동작을 확인하세요.

## <a name="limitation"></a>V12로 업그레이드하는 
ADLS 제한: ADLS에서 제공하는 대역폭 한계에 도달한 경우 태스크 오류가 표시됩니다. 이것은 태스크 로그에서 제한 오류를 확인하여 파악할 수 있습니다.  컨테이너 크기를 늘려 병렬 처리를 줄일 수 있습니다.  작업에 대한 동시성이 더 필요한 경우 문의하세요.   
제한 여부를 확인하려면 클라이언트 쪽에서 디버그 로깅을 사용하도록 설정해야 합니다. 그 방법은 다음과 같습니다.

1. Ambari > Storm > Config > Advanced storm-worker-log4j에서 다음을 변경합니다.  &lt;root level="info"&gt;을 &lt;root level=”debug”&gt;로 변경합니다.  구성을 적용하려면 노드/서비스를 다시 시작합니다.
2. 작업자 노드의 storm 토폴로지 로그에서 ADLS 제한 예외를 모니터링합니다(/var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;port&gt;/worker.log 아래).

## <a name="additional-tuning"></a>추가 조정
Storm의 추가 성능 조정은 이 [블로그](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)를 참조하세요.

## <a name="examples-to-run"></a>실행 예
[github](https://github.com/hdinsight/storm-performance-automation)에 있는 다음 예제를 확인하세요.



<!--HONumber=Feb17_HO2-->


