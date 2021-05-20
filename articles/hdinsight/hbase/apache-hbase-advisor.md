---
title: 클러스터 관리자 권장 사항에 맞게 최적화
titleSuffix: Azure HDInsight
description: Azure HDInsight의 클러스터 관리자 권장 사항에 맞게 Apache HBase를 최적화합니다.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943007"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Azure HDInsight의 Apache HBase 권고

이 문서에서는 Azure HDInsight에서 Apache HBase 성능을 최적화하는 데 도움이 되는 몇 가지 권고를 설명합니다. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>가장 최근에 쓴 데이터를 읽도록 HBase 최적화

이 권고는 HBase에서 가장 최근에 작성된 데이터를 읽는 사용 사례에 도움이 될 수 있습니다. 고성능을 위해 원격 스토리지 대신 memstore에서 HBase 읽기가 처리되는 것이 가장 좋습니다.

이 쿼리 권고는 특정 테이블의 지정된 열 패밀리에서 75%가 넘는 읽기가 memstore에서 처리된다고 나와 있습니다. 이 표시기는 memstore에서 플러시가 발생하더라도 최근 파일에 액세스해야 하며 해당 파일이 캐시에 있어야 함을 나타냅니다. 데이터는 먼저 memstore에 기록되며, 시스템은 여기서 최근 데이터에 액세스합니다. 내부 HBase 플러셔 스레드는 지정된 지역이 128M(기본값) 크기에 도달했음을 감지하고 플러시를 트리거할 수 있습니다. 이 시나리오는 memstore의 크기가 약 128M일 때 기록된 가장 최근의 데이터에도 발생합니다. 따라서 나중에 이러한 최근 레코드를 읽으려면 memstore가 아닌 파일을 읽어야 할 수 있습니다. 그러므로 최근에 플러시된 최신 데이터도 캐시에 상주할 수 있도록 최적화하는 것이 가장 좋습니다.

캐시의 최근 데이터를 최적화하려면 다음 구성 설정을 고려하세요.

1. `hbase.rs.cacheblocksonwrite`를 `true`로 설정합니다. HDInsight HBase의 기본 구성은 `true`이므로 `false`로 다시 설정되지 않았는지 확인합니다.

2. 압축이 발생하지 않도록 `hbase.hstore.compactionThreshold` 값을 늘립니다. 이 값은 기본적으로 `3`입니다. `10`처럼 더 높은 값으로 늘릴 수 있습니다.

3. 2단계를 수행하고 compactionThreshold를 설정하는 경우 `hbase.hstore.compaction.max`를 더 큰 값(예: `100`)으로 변경하고, `hbase.hstore.blockingStoreFiles` 구성 값도 더 큰 값(예: `300`)으로 늘립니다.

4. 최근 데이터만 읽어야 하는 경우 `hbase.rs.cachecompactedblocksonwrite` 구성을 **ON** 으로 설정합니다. 이 구성은 압축이 발생하더라도 데이터가 캐시에 상주함을 시스템에 알립니다. 이 구성은 패밀리 수준에서도 설정할 수 있습니다. 

   HBase 셸에서 다음 명령을 실행하여 `hbase.rs.cachecompactedblocksonwrite` 구성을 설정합니다.
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. 테이블의 지정된 패밀리에 대해 블록 캐시를 해제할 수 있습니다. 가장 최근의 데이터 읽기를 포함하는 패밀리에 대해서는 **ON** 으로 설정해야 합니다. 기본적으로 블록 캐시는 테이블의 모든 패밀리에 대해 ON으로 설정됩니다. 패밀리의 블록 캐시를 사용하지 않도록 설정했는데 ON으로 설정해야 하는 경우 hbase 셸에서 alter 명령을 사용합니다.

   이러한 구성은 데이터가 캐시에 상주하도록 하 고 최근 데이터가 압축되지 않도록 하는 데 도움이 됩니다. 시나리오에서 TTL을 사용할 수 있는 경우에는 날짜로 계층화된 압축을 사용하는 것이 좋습니다. 자세한 내용은 [Apache HBase 참조 가이드: 날짜로 계층화된 압축](https://hbase.apache.org/book.html#ops.date.tiered)을 참조하세요.  

## <a name="optimize-the-flush-queue"></a>플러시 큐 최적화

이 권고는 HBase 플러시에 튜닝이 필요할 수 있음을 나타냅니다. 플러시 처리기에 대한 현재 구성이 쓰기 트래픽을 처리할 만큼 충분히 크지 않을 수 있으며, 이로 인해 플러시 속도가 느려질 수 있습니다.

지역 서버 UI에서 플러시 큐가 100 이상 증가 하는지 확인합니다. 이 임계값은 플러시 속도가 느리다는 의미이며 `hbase.hstore.flusher.count` 구성을 튜닝해야 할 수 있습니다. 기본적으로 이 값은 2입니다. 최대 플러셔 스레드가 6을 초과하지 않아야 합니다.

또한 지역 개수 튜닝에 대한 권장 사항이 있는지 확인합니다. 그렇다면 지역 튜닝을 시도하여 더 빠르게 플러시하는 데 도움이 되는지 확인하는 것이 좋습니다. 그러지 않으면 플러셔 스레드를 튜닝하는 것이 도움이 될 수 있습니다.

## <a name="region-count-tuning"></a>지역 개수 튜닝

지역 개수 튜닝 권고는 HBase에서 업데이트를 차단했으며 지역 개수가 최적으로 지원되는 힙 크기보다 많을 수 있음을 나타냅니다. 힙 크기, memstore 크기 및 지역 개수를 튜닝할 수 있습니다.

예제 시나리오:

- 지역 서버의 힙 크기가 10GB라고 가정합니다. 기본적으로 `hbase.hregion.memstore.flush.size`는 `128M`입니다. `hbase.regionserver.global.memstore.size`의 기본값은 `0.4`입니다. 즉, 10GB 중 4GB가 memstore에 전역적으로 할당됩니다.

- 모든 지역에 쓰기 부하가 균등하게 분산되었고 모든 지역이 128MB까지만 증가한다고 가정할 경우 이 설정의 최대 지역 수는 `32`개 지역입니다. 특정 지역 서버가 32개 지역을 갖도록 구성된 경우 시스템이 업데이트 차단을 더 잘 방지합니다.

- 이러한 설정이 적용되면 지역 수가 100개입니다. 이제 4GB의 글로벌 memstore가 100개 지역에 걸쳐 분할됩니다. 따라서 각 지역의 memstore에는 사실상 40MB만 할당됩니다. 쓰기가 균일하면 시스템은 자주 플러시를 수행하고 40MB보다 작은 크기의 명령을 수행합니다. 플러셔 스레드가 많으면 플러시 속도 `hbase.hstore.flusher.count`가 늘어날 수 있습니다.

이 권고는 업데이트 차단 방지를 위해 플러시 스레드 튜닝과 함께 서버당 지역 수, 힙 크기 및 전역 memstore 크기 구성을 재고하는 것이 좋음을 의미합니다.

## <a name="compaction-queue-tuning"></a>압축 큐 튜닝

HBase 압축 큐가 2000개 이상으로 증가하고 주기적으로 발생하는 경우에는 압축 스레드를 더 큰 값으로 늘릴 수 있습니다.

압축할 파일 수가 너무 많으면 파일이 Azure 파일 시스템과 상호 작용하는 방법과 관련하여 힙 사용량이 늘어날 수 있습니다. 따라서 가능한 한 빨리 압축을 완료하는 것이 더 좋습니다. 이전 클러스터에서는 제한과 관련된 압축 구성으로 인해 압축 속도가 느려질 수 있습니다.

`hbase.hstore.compaction.throughput.lower.bound` 및 `hbase.hstore.compaction.throughput.higher.bound` 구성을 확인합니다. 이미 50M 및 100M으로 설정되어 있으면 그대로 둡니다. 그러나 이러한 설정을 더 작은 값(이전 클러스터의 경우)으로 구성한 경우에는 각각 50M 및 100M으로 제한을 변경합니다.

구성은 `hbase.regionserver.thread.compaction.small` 및 `hbase.regionserver.thread.compaction.large`입니다(기본값은 각각 1).
이 구성의 최댓값을 3보다 작게 설정합니다.

## <a name="full-table-scan"></a>전체 테이블 검색

전체 테이블 검색 권고는 실행된 검색 중 75% 이상이 전체 테이블/지역 검색임을 나타냅니다. 코드가 검색을 호출하여 쿼리 성능을 개선하는 방법을 다시 살펴볼 수 있습니다. 다음 방법을 고려해 보세요.

* 각 검색에 적절한 시작 및 중지 행을 설정합니다.

* 한 번의 검색 호출에서 서로 다른 범위를 쿼리할 수 있도록 **MultiRowRangeFilter** API를 사용합니다. 자세한 내용은 [MultiRowRangeFilter API 설명서](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html)를 참조하세요.

* 전체 테이블 또는 지역 검색이 필요한 경우 캐시를 사용하는 다른 쿼리에서 핫 블록을 제거하지 않도록 해당 쿼리에 대해 캐시 사용을 방지할 수 있는지 확인합니다. 검색에서 캐시를 사용하지 않도록 하려면 코드에서 **setCaching(false)** 옵션과 함께 **검색** API를 사용합니다. 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>다음 단계

[Ambari를 사용하여 Apache HBase 최적화](../optimize-hbase-ambari.md)
