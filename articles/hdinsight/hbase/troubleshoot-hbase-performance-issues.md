---
title: Azure HDInsight의 Apache HBase 성능 문제 해결
description: Azure HDInsight에서 최적의 성능을 얻는 데 도움이 되는 다양 한 Apache HBase 성능 조정 지침 및 팁입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266654"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight의 Apache HBase 성능 문제 해결

이 문서에서는 Azure HDInsight에서 최적의 성능을 얻기 위한 다양 한 Apache HBase 성능 조정 지침과 팁을 설명 합니다. 이러한 팁 중 상당수는 특정 워크 로드 및 읽기/쓰기/검사 패턴에 따라 달라 집니다. 프로덕션 환경에 적용 하기 전에 철저 하 게 구성 변경을 테스트 합니다.

## <a name="hdinsight-hbase-performance-insights"></a>HDInsight HBase 성능 통찰력

대부분의 HBase 작업에서 발생 하는 가장 큰 병목은 WAL (쓰기 미리 로그)입니다. 쓰기 성능에 심각한 영향을 줍니다. HDInsight HBase에는 구분 된 저장소 계산 모델이 있습니다. 즉, 데이터는 Azure Storage에 원격으로 저장 되지만 지역 서버는 Vm에서 호스팅됩니다. 최근 까지는 쓰기 미리 로그가 Azure Storage에도 기록 되어 HDInsight의 경우 이러한 병목 현상이 암시. [가속화](./apache-hbase-accelerated-writes.md) 된 쓰기 기능은 AZURE premium SSD 관리 디스크에 미리 쓰기 로그를 작성 하 여이 문제를 해결 하도록 설계 되었습니다. 이를 통해 성능 크게을 작성할 수 있으며, 일부 쓰기 집약적인 작업에서 직면 하는 많은 문제를 방지할 수 있습니다.

[프리미엄 블록 Blob Storage 계정을](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) 원격 저장소로 사용 하 여 읽기 작업을 크게 향상 시킬 수 있습니다. 이 옵션은 미리 쓰기 로그 기능을 사용 하도록 설정한 경우에만 사용할 수 있습니다.

## <a name="compaction"></a>압축

압축은 기본적으로 커뮤니티에서 동의한 또 다른 잠재적인 병목 상태입니다.  기본적으로 HDInsight HBase 클러스터에서 주요 압축은 사용 하지 않도록 설정 됩니다. 이는 리소스를 많이 사용 하는 프로세스 이기 때문에 고객의 작업 특성을 기준으로 (즉, 사용량이 적은 시간에) 고객에 게 전체 유연성을 제공할 수 있도록 하는 것입니다. 또한 대부분의 온-프레미스 인스턴스에 공통적으로 제공 되는 로컬 HDFS와는 달리, 저장소는 원격 (Azure storage에서 지원 됨)이 아니라, 주요 압축의 기본 목표 중 하나입니다.

고객은 편의를 위해 주요 압축을 예약 해야 한다고 가정 합니다. 이 유지 관리가 수행 되지 않으면 압축은 장기 실행의 읽기 성능에 상당한 영향을 줍니다.

특히 검색 작업의 경우 100 밀리초 보다 훨씬 큰 평균 대기 시간이 문제가 발생 해야 합니다. 주 압축을 정확 하 게 예약 했는지 확인 합니다.

## <a name="know-your-apache-phoenix-workload"></a>Apache Phoenix 워크 로드 파악

다음 질문에 대 한 답변을 통해 Apache Phoenix 워크 로드를 더 잘 이해할 수 있습니다.

* 모든 "읽기"는 검색으로 변환 됩니까?
    * 그렇다면 이러한 검색의 특징은 무엇 인가요?
    * 적절 한 인덱싱을 포함 하 여 이러한 검색에 대 한 phoenix 테이블 스키마를 최적화 했습니까?
* `EXPLAIN` 문을 사용 하 여 "읽기"가 생성 하는 쿼리 계획을 이해 하 고 있어야 합니다.
* 작성 시 "upsert"가 선택 되어 있나요?
    * 이 경우 검색도 수행 됩니다. 예상 되는 검색 대기 시간은 HBase의 10 밀리초와는 달리 평균 100 밀리초의 순서입니다.  

## <a name="test-methodology-and-metrics-monitoring"></a>테스트 방법 및 메트릭 모니터링

YCSB, JMeter 또는 Pherf와 같은 벤치 마크를 사용 하 여 성능 테스트 및 조정 하는 경우 다음을 확인 합니다.

1. 클라이언트 컴퓨터는 병목 상태가 되지 않습니다 (클라이언트 컴퓨터의 CPU 사용량 확인).

1. 클라이언트 쪽 configs (예: 스레드 수)는 포화 클라이언트 대역폭으로 적절 하 게 조정 됩니다.

1. 테스트 결과는 정확 하 게 기록 되며 체계적으로 기록 됩니다.

쿼리가 갑자기 이전 보다 훨씬 더 나쁜 작업을 시작한 경우 – 응용 프로그램 코드에서 잠재적 버그를 확인 하는 것은 갑자기 많은 양의 잘못 된 데이터를 생성 하는 것 이므로 읽기 대기 시간이 자연스럽 게 늘어나고 있나요?

## <a name="migration-issues"></a>마이그레이션 문제

Azure HDInsight로 마이그레이션하는 경우에는 자동화를 통해 매우 효율적이 고 정확 하 게 마이그레이션을 수행 해야 합니다. 수동 마이그레이션을 피합니다. 다음을 확인 합니다.

1. 압축, 블 룸 필터 등의 테이블 특성은 정확 하 게 마이그레이션해야 합니다.

1. Phoenix 테이블의 경우 솔트 설정은 새 클러스터 크기에 맞게 적절히 매핑되어야 합니다. 예를 들어, 솔트 버킷 수는 클러스터의 작업자 노드 수를 여러 개 지정 하는 것이 좋습니다. 특정 배수는 관찰 된 핫 발견의 양입니다.  

## <a name="server-side-config-tunings"></a>서버 쪽 구성 방법

HDInsight HBase에서 HFiles는 원격 저장소에 저장 됩니다. 따라서 캐시에 오류가 발생 하는 경우에는 관련 된 네트워크 대기 시간으로 인해 로컬 HDFS에서 데이터를 지 원하는 데이터를 포함 하는 온-프레미스 시스템 보다는 읽기 비용이 매우 높아집니다. 대부분의 시나리오에서는이 문제를 피하기 위해 HBase 캐시 (블록 캐시 및 버킷 캐시)를 지능적으로 사용 합니다. 그러나이 경우에는 고객에 게 문제가 될 수 있는 경우가 종종 있습니다. 프리미엄 블록 blob 계정을 사용 하는 것이 다소 도움이 되었습니다. 그러나 WASB (Windows Azure Storage Driver) blob을 사용 하면 읽기 모드로 결정 된 항목 `fs.azure.read.request.size` (순차 및 임의)에 따라 블록에서 데이터를 인출 하는 등의 특정 속성을 사용 하는 것이 좋습니다. 읽기 요청 블록 크기 (`fs.azure.read.request.size`)를 512 KB로 설정 하 고 HBase 테이블의 블록 크기와 일치 하는 경험적 실험을 통해 최상의 결과를 얻을 수 있습니다.

가장 큰 크기의 노드 클러스터에 대 한 HDInsight HBase `bucketcache` 는 `regionservers`를 실행 하는 VM에 연결 된 로컬 SSD의 파일로를 제공 합니다. 때때로 힙 캐시를 대신 사용 하는 것이 약간 개선 될 수 있습니다. 이는 사용 가능한 메모리를 사용 하는 제한 사항이 며 파일 기반 캐시 보다 크기가 작을 가능성이 있으므로 항상 적절 한 선택이 아닐 수 있습니다.

조정 된 다른 특정 매개 변수 중 일부는 다음과 같이 몇 가지 근거를 사용 하 여 다양 한 수준에 도움이 되었습니다.

1. 기본 `memstore` 128 mb에서 256 mb로 크기 늘리기 –이 설정은 일반적으로 많은 쓰기 시나리오에 권장 됩니다.

1. 기본값인 1에서 4로 압축 전용 스레드 수를 늘립니다. 이 설정은 사소한 작업을 자주 관찰 하는 경우에 적합 합니다.

1. 저장소 제한 `memstore` 으로 인해 플러시를 차단 하지 않습니다. `Hbase.hstore.blockingStoreFiles`이 버퍼를 제공 하기 위해 100으로 늘릴 수 있습니다.

1. 플러시를 제어 하기 위해 기본값은 아래와 같이 주소를 지정할 수 있습니다.

    1. `Hbase.regionserver.maxlogs`32에서 140로 upped 수 있습니다 (WAL 제한으로 인해 플러시 방지).

    1. `Hbase.regionserver.global.memstore.lowerLimit`= 0.55.

    1. `Hbase.regionserver.global.memstore.upperLimit`= 0.60.

1. 스레드 풀 조정을 위한 Phoenix 별 configs:

    1. `Phoenix.query.queuesize`1만로 늘릴 수 있습니다.

    1. `Phoenix.query.threadpoolsize`512로 늘릴 수 있습니다.

1. 기타 phoenix 관련 configs:

    1. `Phoenix.rpc.index.handler.count`크거나 많은 인덱스 조회가 있는 경우 50로 설정할 수 있습니다.

    1. `Phoenix.stats.updateFrequency`– 기본값 15 분에서 1 시간으로 upped 수 있습니다.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems`– 30 분에서 1 시간으로 upped 수 있습니다.

    1. `Phoenix.coprocessor.maxmetadatacachesize`– 20mb에서 50 upped 수 있습니다.

1. RPC 시간 제한 – HBase rpc 시간 제한, HBase 클라이언트 스캐너 시간 제한 및 Phoenix 쿼리 제한 시간을 3 분으로 늘릴 수 있습니다. 여기서 `hbase.client.scanner.caching` 매개 변수는 서버 end와 클라이언트 끝에서 일치 하는 값으로 설정 된다는 점에 유의 해야 합니다. 그렇지 않으면이 설정은 클라이언트 끝 `OutOfOrderScannerException` 에서와 관련 된 오류를 발생 시킵니다. 이 설정은 대량 검색에 대해 낮은 값으로 설정 해야 합니다. 이 값은 100로 설정 됩니다.

## <a name="other-considerations"></a>기타 고려 사항

튜닝에 고려할 다른 매개 변수는 다음과 같습니다.

1. `Hbase.rs.cacheblocksonwrite`–이 설정은 기본적으로 HDI에 대해 true로 설정 됩니다.

1. 나중에 대 한 보조 압축을 연기할 수 있는 설정입니다.

1. 읽기 및 쓰기 요청에 대해 예약 된 큐의 백분율 조정 등의 실험적 설정

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

- 을 사용 [@AzureSupport](https://twitter.com/azuresupport) 하 여 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

- 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
