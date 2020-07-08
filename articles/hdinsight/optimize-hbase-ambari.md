---
title: Azure HDInsight에서 apache Ambari를 사용 하 여 Apache HBase 최적화
description: Apache Ambari 웹 UI를 사용 하 여 Apache HBase를 구성 하 고 최적화 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a7da6bc23d797e0e89b2338f446fc850b0fd0577
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82797163"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 apache Ambari를 사용 하 여 Apache HBase 최적화

Apache Ambari는 HDInsight 클러스터를 관리 하 고 모니터링 하는 웹 인터페이스입니다. Ambari 웹 UI에 대 한 소개는 [Apache Ambari 웹 ui를 사용 하 여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조 하세요.

Apache HBase 구성은 **HBase Configs** 탭에서 수정 됩니다. 다음 섹션에서는 HBase 성능에 영향을 주는 몇 가지 중요 한 구성 설정에 대해 설명 합니다.

## <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE 설정

HBase 힙 크기는 *영역* 및 *마스터* 서버에서 사용할 최대 힙 크기를 메가 바이트 단위로 지정합니다. 기본값은 1,000MB입니다. 클러스터 작업에 대해이 값을 조정 해야 합니다.

1. 수정하려면 HBase **Configs**(구성) 탭의 **Advanced HBase-env**(고급 HBase-env) 창으로 이동한 다음 `HBASE_HEAPSIZE` 설정을 찾습니다.

1. 기본값을 5,000MB로 변경합니다.

    ![' Apache Ambari HBase memory heapsize '](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>읽기 작업이 많은 워크로드 최적화

다음 구성은 읽기 작업이 많은 워크로드의 성능을 향상시키는 데 중요합니다.

### <a name="block-cache-size"></a>블록 캐시 크기

블록 캐시는 읽기 캐시입니다. 이 크기는 `hfile.block.cache.size` 매개 변수로 제어됩니다. 기본값은 0.4이며 이것은 전체 영역 서버 메모리의 40퍼센트입니다. 블록 캐시 크기가 클수록 임의 읽기가 빨라집니다.

1. 이 매개 변수를 수정하려면 HBase **Configs**(구성) 탭의 **Settings**(설정) 탭으로 이동한 다음 **% of RegionServer Allocated to Read Buffers**(버퍼를 읽기 위해 할당된 RegionServer의 %)를 찾습니다.

    ![Apache HBase 메모리 블록 캐시 크기](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. 값을 변경하려면 **편집** 아이콘을 선택합니다.

### <a name="memstore-size"></a>Memstore 크기

모든 편집은 *Memstore*라는 메모리 버퍼에 저장됩니다. 이 버퍼는 단일 작업으로 디스크에 쓸 수 있는 총 데이터 양을 늘립니다. 또한 최근 편집에 대 한 액세스를 가속화 합니다. Memstore 크기는 다음 두 가지 매개 변수로 정의됩니다.

* `hbase.regionserver.global.memstore.UpperLimit`: Memstore가 함께 사용할 수 있는 영역 서버의 최대 백분율을 정의합니다.

* `hbase.regionserver.global.memstore.LowerLimit`: Memstore가 함께 사용할 수 있는 영역 서버의 최소 백분율을 정의합니다.

임의 읽기를 최적화하려면 Memstore 상한 및 하한을 줄입니다.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>디스크에서 스캔할 때 페치하는 행 수

`hbase.client.scanner.caching` 설정은 스캐너에서 `next` 메서드가 호출될 때 디스크에서 읽어오는 행 수를 정의합니다.  기본값은 100입니다. 숫자가 높을수록 클라이언트에서 영역 서버로의 원격 호출이 줄어들어 스캔 속도가 빨라집니다. 그러나이 설정은 클라이언트의 메모리 압력도 증가 시킵니다.

![인출 된 Apache HBase 행 수](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> 스캐너에서 다음 메서드 호출 사이의 간격이 스캐너 시간 제한보다 크도록 값을 설정하지 않습니다. 스캐너 시간 제한 기간은 `hbase.regionserver.lease.period` 속성에 의해 정의됩니다.

## <a name="optimize-write-heavy-workloads"></a>쓰기 작업이 많은 워크로드 최적화

다음 구성은 쓰기 작업이 많은 워크로드의 성능을 향상시키는 데 중요합니다.

### <a name="maximum-region-file-size"></a>최대 영역 파일 크기

HBase는 *HFile*이라는 내부 파일 형식으로 데이터를 저장합니다. `hbase.hregion.max.filesize` 속성은 영역에 대한 단일 HFile의 크기를 정의합니다.  영역 내 모든 HFile의 합계가 이 설정보다 큰 경우 하나의 영역이 두 영역으로 분할됩니다.

![' Apache HBase HRegion max filesize '](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

영역 파일 크기가 클수록 분할 수가 줄어듭니다. 파일 크기를 늘려서 쓰기 성능이 최대화되는 값을 결정할 수 있습니다.

### <a name="avoid-update-blocking"></a>업데이트 차단 방지

* `hbase.hregion.memstore.flush.size` 속성은 Memstore가 디스크로 플러시되는 크기를 정의합니다. 기본 크기는128MB입니다.

* HBase 영역 블록 승수는에 의해 정의 됩니다 `hbase.hregion.memstore.block.multiplier` . 기본값은 4입니다. 허용되는 최대값은 8입니다.

* Memstore가 (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`)바이트이면 HBase가 업데이트를 차단합니다.

    플러시 크기 및 블록 승수의 기본값을 사용하면 Memstore 크기가 128 * 4 = 512MB인 경우 업데이트가 차단됩니다. 업데이트 차단 횟수를 줄이려면 `hbase.hregion.memstore.block.multiplier` 값을 늘립니다.

![Apache HBase 영역 블록 승수](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Memstore 크기 정의

Memstore 크기는 `hbase.regionserver.global.memstore.UpperLimit` 및 `hbase.regionserver.global.memstore.LowerLimit` 매개 변수로 정의됩니다. 이 값을 서로 같게 설정하면 쓰기 중에 일시 중지가 줄어들어서(또한 플러시 빈도가 높아져서) 쓰기 성능이 향상됩니다.

## <a name="set-memstore-local-allocation-buffer"></a>Memstore 로컬 할당 버퍼 설정

Memstore 로컬 할당 버퍼 사용은 `hbase.hregion.memstore.mslab.enabled` 속성으로 결정됩니다. 이 설정을 사용 하도록 설정 하면 (true) 사용량이 많은 쓰기 작업 중에 힙 조각화가 방지 됩니다. 기본값은 true입니다.

![hbase.hregion.memstore.mslab.enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI로 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [클러스터 최적화](./hdinsight-changing-configs-via-ambari.md)
* [Apache Hive 최적화](./optimize-hive-ambari.md)
* [Apache Pig 최적화](./optimize-pig-ambari.md)
