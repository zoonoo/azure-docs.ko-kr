---
title: 클러스터 관리자 권장 사항 최적화
titleSuffix: Azure HDInsight
description: Azure HDInsight의 cluster advisor 권장 사항에 대해 Apache HBase를 최적화 합니다.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943007"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Azure HDInsight의 Apache HBase 권고

이 문서에서는 Azure HDInsight에서 Apache HBase 성능을 최적화 하는 데 도움이 되는 몇 가지 권고를 설명 합니다. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>가장 최근에 쓴 데이터를 읽도록 HBase 최적화

사용 사례가 HBase에서 가장 최근에 작성 된 데이터를 읽는 경우이 권고는 도움이 될 수 있습니다. 고성능을 위해 HBase 읽기가 원격 저장소 대신 memstore에서 제공 되는 것이 가장 좋습니다.

쿼리 권고는 테이블의 지정 된 열 패밀리에 대해 memstore에서 제공 되는 75% 읽기 > 함을 나타냅니다. 이 지표는 memstore에서 플러시가 발생 하더라도 최근 파일에 액세스 해야 하며 캐시에 있어야 함을 나타냅니다. 데이터는 먼저 memstore에 기록 되며, 시스템은 해당 데이터에 최근 데이터에 액세스 합니다. 내부 HBase 플 러 셔 스레드에서는 지정 된 지역이 128m (기본값) 크기에 도달 했음을 감지 하 고 플러시를 트리거할 수 있습니다. 이 시나리오는 memstore의 크기가 128M 일 때 작성 된 최신 데이터에도 발생 합니다. 따라서 나중에 이러한 최근 레코드를 읽으면 memstore가 아닌 파일을 읽어야 할 수 있습니다. 따라서 최근에 플러시된 최근 데이터도 캐시에 상주할 수 있도록 최적화 하는 것이 가장 좋습니다.

캐시의 최근 데이터를 최적화 하려면 다음 구성 설정을 고려 하십시오.

1. `hbase.rs.cacheblocksonwrite`를 `true`로 설정합니다. HDInsight HBase의이 기본 구성은 이므로 `true` 로 다시 설정 되지 않았는지 확인 `false` 합니다.

2. `hbase.hstore.compactionThreshold`에서 시작의 압축을 방지할 수 있도록 값을 늘립니다. 이 값은 기본적으로 `3`입니다. 보다 높은 값으로 늘릴 수 있습니다 `10` .

3. 2 단계를 수행 하 고 compactionThreshold를 설정 하는 경우 예를 들어 `hbase.hstore.compaction.max` 더 높은 값으로 변경 하 `100` 고, 예를 들어 구성 값을 `hbase.hstore.blockingStoreFiles` 더 큰 값으로 늘립니다 `300` .

4. 최근 데이터만 읽어야 하는 경우 `hbase.rs.cachecompactedblocksonwrite` 구성을 **켜기** 로 설정 합니다. 이 구성은 압축이 발생 하더라도 데이터가 캐시에 남아 있음을 시스템에 알립니다. 구성은 패밀리 수준 에서도 설정할 수 있습니다. 

   HBase 셸에서 다음 명령을 실행 하 여 config를 설정 합니다 `hbase.rs.cachecompactedblocksonwrite` .
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. 테이블의 지정 된 패밀리에 대해 블록 캐시를 해제할 수 있습니다. 가장 최근의 데이터 읽기를 포함 하는 **제품군에 대해 설정 되어** 있는지 확인 합니다. 기본적으로 블록 캐시는 테이블의 모든 패밀리에 대해 설정 됩니다. 패밀리의 블록 캐시를 사용 하지 않도록 설정 하 고 사용 하도록 설정 해야 하는 경우 hbase 셸에서 alter 명령을 사용 합니다.

   이러한 구성은 데이터를 캐시에서 사용할 수 있도록 하 고 최근 데이터가 압축 되지 않도록 하는 데 도움이 됩니다. 시나리오에서 TTL을 사용할 수 있는 경우에는 날짜 계층 압축을 사용 하는 것이 좋습니다. 자세한 내용은 [Apache HBase 참조 가이드: 날짜 계층화 된 압축](https://hbase.apache.org/book.html#ops.date.tiered) 을 참조 하세요.  

## <a name="optimize-the-flush-queue"></a>플러시 큐 최적화

이 권고는 HBase 플러시에 조정이 필요할 수 있음을 나타냅니다. 플러시 핸들러에 대 한 현재 구성이 쓰기 트래픽을 처리할 수 있을 만큼 충분히 크지 않을 수 있으며이로 인해 플러시 속도가 느려질 수 있습니다.

지역 서버 UI에서 플러시 큐가 100 이상 증가 하는지 확인 합니다. 이 임계값은 플러시 속도가 느리고 구성을 조정 해야 할 수 있음을 나타냅니다   `hbase.hstore.flusher.count` . 기본적으로이 값은 2입니다. 최대 플 러 셔 스레드가 6을 초과 하지 않도록 합니다.

또한 지역 개수 튜닝에 대 한 권장 사항이 있는지 확인 합니다. 예를 사용 하는 경우이를 통해 더 빠르게 플러시할 수 있는지 확인 하기 위해 지역 튜닝을 시도 하는 것이 좋습니다. 그러지 않으면 플 러 셔 스레드를 조정 하는 데 도움이 될 수 있습니다.

## <a name="region-count-tuning"></a>지역 수 튜닝

지역 수 튜닝 권고는 HBase에서 업데이트를 차단 했으며 지역 수가 최적으로 지원 되는 힙 크기 보다 많을 수 있음을 나타냅니다. 힙 크기, memstore 크기 및 지역 수를 튜닝할 수 있습니다.

예제 시나리오는 다음과 같습니다.

- 지역 서버의 힙 크기가 10gb로 가정 합니다. 기본적으로는 `hbase.hregion.memstore.flush.size` `128M` 입니다. `hbase.regionserver.global.memstore.size`의 기본값은 `0.4`입니다. 즉, 10gb (전역적으로)에 4gb가 할당 됩니다.

- 모든 지역에 대 한 쓰기 부하를 균등 하 게 분산 하 고 모든 지역이 128 MB 까지만 증가 한다고 가정 하 고이 설치의 최대 지역 수는 지역입니다 `32` . 지정 된 지역 서버가 32 지역을 갖도록 구성 된 경우 시스템은 업데이트 차단을 방지 합니다.

- 이러한 설정이 적용 되 면 지역 수가 100입니다. 이제 4gb 전역 memstore가 100 지역에 걸쳐 분할 됩니다. 따라서 사실상 각 지역은 memstore에 대해 40 MB만을 얻습니다. 쓰기가 균일 하면 시스템은 가장 자주 플러시를 수행 하 고 크기는 40 < 합니다. 여러 플 러 셔 스레드가 있으면 플러시 속도가 늘어날 수 있습니다 `hbase.hstore.flusher.count` .

이 권고는 업데이트 차단 방지를 위해 플러시 스레드 조정과 함께 서버당 지역 수, 힙 크기 및 전역 memstore 크기 구성을 다시 고려할 수 있음을 의미 합니다.

## <a name="compaction-queue-tuning"></a>압축 큐 튜닝

HBase 압축 큐가 2000 보다 크게 증가 하 여 주기적으로 발생 하는 경우에는 압축 스레드를 큰 값으로 늘릴 수 있습니다.

압축에 사용할 파일 수가 너무 많으면 파일이 Azure 파일 시스템과 상호 작용 하는 방법과 관련 된 더 많은 힙 사용이 발생할 수 있습니다. 따라서 가능한 한 빨리 압축을 완료 하는 것이 좋습니다. 이전 클러스터에서 제한과 관련 된 압축 구성은 느린 압축 속도로 이어질 수 있습니다.

구성 및를 확인 합니다 `hbase.hstore.compaction.throughput.lower.bound` `hbase.hstore.compaction.throughput.higher.bound` . 이미 50M 및 100M로 설정 되어 있으면 그대로 둡니다. 그러나 이러한 설정을 더 낮은 값 (이전 클러스터의 경우)으로 구성한 경우에는 각각 50M 및 100M로 제한을 변경 합니다.

구성은 `hbase.regionserver.thread.compaction.small` 및 이며 `hbase.regionserver.thread.compaction.large` 기본값은 각각 1입니다.
이 구성에 대 한 max 값을 3 보다 작게 설정 합니다.

## <a name="full-table-scan"></a>전체 테이블 검색

전체 테이블 검색 권고는 실행 된 검색 중 75% 이상이 전체 테이블/지역 검색 임을 나타냅니다. 코드에서 검색을 호출 하 여 쿼리 성능을 향상 시키는 방법에 다시 방문할 수 있습니다. 다음 방법을 고려 하십시오.

* 각 검색에 대해 적절 한 시작 및 중지 행을 설정 합니다.

* 하나의 스캔 호출에서 서로 다른 범위를 쿼리할 수 있도록 **Multirow범위 필터** API를 사용 합니다. 자세한 내용은 [Multirowrange FILTER API 설명서](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html)를 참조 하세요.

* 전체 테이블 또는 지역 검색이 필요한 경우 캐시를 사용 하는 다른 쿼리에서 핫의 블록을 제거 하지 않을 수 있도록 해당 쿼리에 대해 캐시 사용을 방지할 가능성이 있는지 확인 합니다. 검색에서 캐시를 사용 하지 않도록 하려면 코드에서 **setcaching (false)** 옵션과 함께 **검색** API를 사용 합니다. 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>다음 단계

[Ambari를 사용 하 여 Apache HBase 최적화](../optimize-hbase-ambari.md)
