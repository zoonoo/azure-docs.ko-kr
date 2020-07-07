---
title: Azure HDInsight에서 apache Ambari를 사용 하 여 Apache Pig 최적화
description: Apache Ambari 웹 UI를 사용 하 여 Apache Pig를 구성 하 고 최적화 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 469019eb1e90654d1953156337593d5de99b46c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796682"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 apache Ambari를 사용 하 여 Apache Pig 최적화

Apache Ambari는 HDInsight 클러스터를 관리 하 고 모니터링 하는 웹 인터페이스입니다. Ambari 웹 UI에 대 한 소개는 [Apache Ambari 웹 ui를 사용 하 여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조 하세요.

Apache Pig 속성을 Ambari Web UI에서 수정하여 Pig 쿼리를 조정할 수 있습니다. Pig 속성을 Ambari에서 직접 수정하면 `/etc/pig/2.4.2.0-258.0/pig.properties` 파일의 Pig 속성이 수정됩니다.

1. Pig 속성을 수정하려면 Pig **Configs**(구성) 탭으로 이동한 다음 **Advanced pig-properties**(고급 Pig 속성) 창을 확장합니다.

1. 수정할 속성의 값을 찾아서 주석 처리를 제거하고 변경합니다.

1. 창의 오른쪽 위에 있는 **저장** 을 선택 하 여 새 값을 저장 합니다. 일부 속성은 서비스를 다시 시작해야 할 수도 있습니다.

    ![고급 Apache pig 속성](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> 모든 세션 수준 설정은 `pig.properties` 파일의 속성 값을 재정의합니다.

## <a name="tune-execution-engine"></a>실행 엔진 조정

Pig 스크립트를 실행하기 위해 두 개의 실행 엔진 즉, MapReduce 및 Tez를 사용할 수 있습니다. Tez는 최적화된 엔진이며 MapReduce보다 훨씬 빠릅니다.

1. 실행 엔진을 수정하려면 **Advanced pig-properties**(고급 Pig 속성) 창에서 `exectype` 속성을 찾습니다.

1. 기본값은 **MapReduce**입니다. 이것을 **Tez**로 변경합니다.

## <a name="enable-local-mode"></a>로컬 모드 사용

Hive와 마찬가지로 로컬 모드는 비교적 양이 적은 데이터의 작업 속도를 높이는 데 사용됩니다.

1. 로컬 모드를 사용하도록 설정하려면 `pig.auto.local.enabled`를 **true**로 설정합니다. 기본값은 False입니다.

1. 입력 데이터 크기가 `pig.auto.local.input.maxbytes` 속성 값보다 작은 작업은 작은 작업으로 간주됩니다. 기본값은 1GB입니다.

## <a name="copy-user-jar-cache"></a>사용자 jar 캐시 복사

Pig는 UDF에 필요한 JAR 파일을 태스크 노드에서 사용할 수 있도록 분산 캐시에 복사합니다. 이러한 jar 자주 변경 되지 않습니다. `pig.user.cache.enabled` 설정을 사용하도록 설정하면 jar를 캐시에 배치하여 동일한 사용자가 실행하는 작업에 다시 사용할 수 있습니다. 이 설정으로 인해 작업 성능이 조금씩 증가 합니다.

1. 사용하도록 설정하려면 `pig.user.cache.enabled`을 true로 설정합니다. 기본값은 false입니다.

1. 캐시된 jar의 기본 경로를 설정하려면 `pig.user.cache.location`을 기본 경로로 설정합니다. 기본값은 `/tmp`입니다.

## <a name="optimize-performance-with-memory-settings"></a>메모리 설정으로 성능 최적화

다음 메모리 설정은 Pig 스크립트 성능을 최적화하는 데 도움이 됩니다.

* `pig.cachedbag.memusage`: 모음에 지정 된 메모리의 양입니다. bag은 튜플의 컬렉션입니다. 튜플은 정렬된 필드 집합이며 필드는 데이터의 일부입니다. 모음의 데이터가 지정 된 메모리를 초과 하는 경우 디스크에 분산 됩니다. 기본값은 0.2이며, 이것은 사용 가능한 메모리의 20%를 나타냅니다. 이 메모리는 애플리케이션의 모든 bag에서 공유됩니다.

* `pig.spill.size.threshold`: 이 유출 크기 임계값(바이트 단위)보다 큰 bag은 디스크로 유출됩니다. 기본값은 5MB입니다.

## <a name="compress-temporary-files"></a>임시 파일 압축

Pig는 작업 실행 중에 임시 파일을 생성합니다. 임시 파일을 압축하면 디스크에 파일을 쓰거나 읽을 때 성능이 향상됩니다. 다음 설정을 사용하여 임시 파일을 압축할 수 있습니다.

* `pig.tmpfilecompression`: true이면 임시 파일 압축이 사용됩니다. 기본값은 False입니다.

* `pig.tmpfilecompression.codec`: 임시 파일을 압축하는 데 사용하는 압축 코덱입니다. 권장 되는 압축 코덱은 낮은 CPU 사용을 위한 LZO 및 Snappy입니다.

## <a name="enable-split-combining"></a>분할 결합 사용

사용하도록 설정하면 맵 작업 수가 줄어들도록 작은 파일이 결합됩니다. 이 설정은 작은 파일이 많은 작업의 효율성을 향상 시킵니다. 사용하도록 설정하려면 `pig.noSplitCombination`을 true로 설정합니다. 기본값은 False입니다.

## <a name="tune-mappers"></a>매퍼 조정

매퍼 수는 `pig.maxCombinedSplitSize` 속성을 수정하여 제어됩니다. 이 속성은 단일 맵 태스크에서 처리할 데이터의 크기를 지정 합니다. 기본값은 파일 시스템의 기본 블록 크기입니다. 이 값을 늘려도 매퍼 작업 수가 줄어듭니다.

## <a name="tune-reducers"></a>리듀서 조정

리듀서 수는 `pig.exec.reducers.bytes.per.reducer` 매개 변수를 기반으로 계산됩니다. 이 매개 변수는 리듀서당 처리되는 바이트 수를 지정하며 기본값은 1GB입니다. 최대 리 듀 서 수를 제한 하려면 `pig.exec.reducers.max` 속성을 기본적으로 999로 설정 합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI로 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [클러스터 최적화](./hdinsight-changing-configs-via-ambari.md)
* [Apache HBase 최적화](./optimize-hbase-ambari.md)
* [Apache Hive 최적화](./optimize-hive-ambari.md)
