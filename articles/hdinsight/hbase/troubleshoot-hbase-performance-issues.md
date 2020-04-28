---
title: Azure HDInsight에서 Apache HBase 성능 문제 해결
description: Azure HDInsight에서 최적의 성능을 얻는 데 도움이 되는 다양 한 Apache HBase 성능 조정 지침 및 팁입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75887158"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight에서 Apache HBase 성능 문제 해결

이 문서에서는 Azure HDInsight에서 최적의 성능을 얻기 위한 다양 한 Apache HBase 성능 조정 지침과 팁을 설명 합니다. 이러한 팁 중 상당수는 특정 워크 로드 및 읽기/쓰기/검사 패턴에 따라 달라 집니다. 프로덕션 환경에서 구성 변경 내용을 적용 하기 전에 철저 하 게 테스트 합니다.

## <a name="hbase-performance-insights"></a>HBase 성능 정보

대부분의 HBase 작업에서 발생 하는 가장 큰 병목은 WAL (쓰기 미리 로그)입니다. 쓰기 성능에 심각한 영향을 줍니다. HDInsight HBase에는 구분 된 저장소 계산 모델이 있습니다. 가상 컴퓨터에서 지역 서버를 호스팅하는 경우에도 데이터는 Azure Storage에 원격으로 저장 됩니다. 최근 까지는 WAL도 Azure Storage에 기록 되었습니다. HDInsight에서이 동작은 이러한 병목 상태를 증폭 합니다. [가속화 된 쓰기](./apache-hbase-accelerated-writes.md) 기능은이 문제를 해결 하도록 설계 되었습니다. Azure 프리미엄 SSD 관리 디스크에 WAL을 기록 합니다. 이 크게는 쓰기 성능을 향상 시켜 주며, 몇 가지 쓰기 집약적인 작업에서 직면 하는 많은 문제를 해결 합니다.

읽기 작업의 성능을 크게 향상 시키려면 [Premium Block Blob Storage 계정을](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) 원격 저장소로 사용 합니다. 이 옵션은 WAL 기능이 사용 하도록 설정 된 경우에만 가능 합니다.

## <a name="compaction"></a>압축

압축은 커뮤니티에서 근본적으로 합의 되는 또 다른 잠재적인 병목 상태입니다. 기본적으로 HDInsight HBase 클러스터에서 주 압축은 사용 하지 않도록 설정 됩니다. 압축은 리소스를 많이 사용 하는 프로세스 이지만 고객의 작업에 따라 일정을 예약 하는 데는 유연성이 있습니다. 예를 들어, 사용량이 적은 시간에 예약할 수 있습니다. 또한 저장소는 로컬 Hadoop 분산 파일 시스템 (HDFS) 대신 원격 (Azure Storage에 의해 지원 됨) 이므로 데이터 집약성은 문제가 되지 않습니다.

고객은 편의를 위해 주요 압축을 예약 해야 합니다. 이러한 유지 관리를 수행 하지 않으면 압축은 장기 실행에서 읽기 성능에 부정적인 영향을 줍니다.

검색 작업의 경우 100 밀리초 보다 훨씬 높은 평균 대기 시간을 고려해 야 합니다. 주 압축을 정확 하 게 예약 했는지 확인 합니다.

## <a name="apache-phoenix-workload"></a>작업 Apache Phoenix

다음 질문에 대 한 답변을 통해 Apache Phoenix 워크 로드를 더 잘 이해할 수 있습니다.

* 모든 "읽기"는 검색으로 변환 됩니까?
    * 그렇다면 이러한 검색의 특징은 무엇 인가요?
    * 적절 한 인덱싱을 포함 하 여 이러한 검색에 대 한 Phoenix 테이블 스키마를 최적화 했습니까?
* `EXPLAIN` 문을 사용 하 여 "읽기"가 생성 하는 쿼리 계획을 이해 했습니까?
* 작성 시 "upsert"가 선택 되어 있나요?
    * 이 경우 검색도 수행 됩니다. HBase에서 point를 가져오는 데 10 밀리초와 비교 했을 때 100 밀리초 정도의 평균 검색에 대 한 예상 대기 시간입니다.  

## <a name="test-methodology-and-metrics-monitoring"></a>테스트 방법 및 메트릭 모니터링

Yahoo!와 같은 벤치 마크를 사용 하는 경우 클라우드를 제공 하는 벤치 마크, JMeter 또는 Pherf 성능을 테스트 하 고 조정 하려면 다음을 확인 하세요.

- 클라이언트 컴퓨터에서 병목 상태가 발생 하지 않습니다. 이렇게 하려면 클라이언트 컴퓨터의 CPU 사용량을 확인 합니다.

- 스레드 수와 같은 클라이언트 쪽 구성은 포화 클라이언트 대역폭으로 적절 하 게 조정 됩니다.

- 테스트 결과는 정확 하 게 기록 되며 체계적으로 기록 됩니다.

쿼리가 갑자기 이전 보다 훨씬 더 나쁜 작업을 시작한 경우 응용 프로그램 코드에서 잠재적 버그를 확인 합니다. 갑자기 많은 양의 잘못 된 데이터를 생성 하 고 있습니까? 인 경우 읽기 대기 시간을 늘릴 수 있습니다.

## <a name="migration-issues"></a>마이그레이션 문제

Azure HDInsight로 마이그레이션하는 경우에는 자동화를 통해 매우 효율적이 고 정확 하 게 마이그레이션을 수행 해야 합니다. 수동 마이그레이션을 피합니다. 확인할 사항은 다음과 같습니다.

- 테이블 특성은 정확 하 게 마이그레이션됩니다. 특성에는 압축, 블 룸 필터 등이 포함 될 수 있습니다.

- Phoenix 테이블의 솔트 설정은 새 클러스터 크기로 적절 하 게 매핑됩니다. 예를 들어 솔트 버킷 수는 클러스터의 작업자 노드 수의 배수 여야 합니다. 그리고 핫 발견 크기의 요소인 배수를 사용 해야 합니다.

## <a name="server-side-configuration-tunings"></a>서버 쪽 구성 방법

HDInsight HBase에서 HFiles는 원격 저장소에 저장 됩니다. 캐시 누락이 있는 경우 온-프레미스 시스템의 데이터는 로컬 HDFS에서 지원 되기 때문에 읽기 비용은 온-프레미스 시스템 보다 높습니다. 대부분의 시나리오에서는이 문제를 피하기 위해 HBase 캐시 (블록 캐시 및 버킷 캐시)를 지능적으로 사용 합니다. 문제가 우회할 되지 않는 경우 프리미엄 블록 blob 계정을 사용 하면이 문제를 해결할 수 있습니다. Windows Azure Storage Blob 드라이버는와 `fs.azure.read.request.size` 같은 특정 속성을 사용 하 여 읽기 모드 (순차 및 임의)로 확인 된 항목을 기반으로 블록의 데이터를 인출 하므로 읽기와의 대기 시간이 더 길어질 수 있습니다. 경험적 실험을 통해 읽기 요청 블록 크기 (`fs.azure.read.request.size`)를 512 KB로 설정 하 고 HBase 테이블의 블록 크기를 동일한 크기로 설정 하 여 최상의 결과를 얻을 수 있습니다.

대부분의 큰 노드 클러스터에서 HDInsight HBase는를 실행 `bucketcache` `regionservers`하는 가상 컴퓨터에 연결 된 로컬 프리미엄 SSD에 파일로 제공 합니다. 대신 off-힙 캐시를 사용 하면 몇 가지 개선 사항이 제공 될 수 있습니다. 이 해결 방법에는 사용 가능한 메모리 사용과 파일 기반 캐시 보다 작은 잠재적 크기의 제한이 있으므로 항상 가장 적합 한 선택이 아닐 수 있습니다.

다음은 몇 가지 다른 특정 매개 변수를 조정 하 여 다양 한 수준에서 사용할 수 있도록 하는 것입니다.

- 크기 `memstore` 를 기본 128 mb에서 256 mb로 늘립니다. 일반적으로이 설정은 많은 쓰기 시나리오에 권장 됩니다.

- 기본 설정인 **1** 에서 **4**로 압축 전용 스레드 수를 늘립니다. 이 설정은 사소한 작업을 자주 관찰 하는 경우에 적합 합니다.

- 저장소 제한 `memstore` 으로 인해 플러시를 차단 하지 않습니다. 이 버퍼를 제공 하려면 `Hbase.hstore.blockingStoreFiles` 설정을 **100**로 늘립니다.

- 플러시를 제어 하려면 다음 설정을 사용 합니다.

    - `Hbase.regionserver.maxlogs`: **140** (WAL 제한으로 인해 플러시 방지)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0.60**

- 스레드 풀 튜닝에 대 한 Phoenix 관련 구성:

    - `Phoenix.query.queuesize`: **1만**

    - `Phoenix.query.threadpoolsize`: **512**

- 기타 Phoenix 관련 구성:

    - `Phoenix.rpc.index.handler.count`: **50** (크거나 많은 인덱스 조회)

    - `Phoenix.stats.updateFrequency`: **1 시간**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 시간**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 m b**

- RPC 시간 제한: **3 분**

   - RPC 시간 제한에는 HBase RPC 시간 제한, HBase 클라이언트 스캐너 시간 제한 및 Phoenix 쿼리 제한 시간이 포함 됩니다. 
   - `hbase.client.scanner.caching` 매개 변수가 서버 end와 클라이언트 끝에서 같은 값으로 설정 되어 있는지 확인 합니다. 동일 하지 않은 경우이 설정에 `OutOfOrderScannerException`는와 관련 된 클라이언트 끝 오류가 발생 합니다. 이 설정은 대량 검색에 대해 낮은 값으로 설정 해야 합니다. 이 값은 **100**로 설정 됩니다.

## <a name="other-considerations"></a>기타 고려 사항

다음은 튜닝을 고려 하는 추가 매개 변수입니다.

- `Hbase.rs.cacheblocksonwrite`– HDI에서는 기본적으로이 설정이 **true**로 설정 됩니다.

- 나중에 대 한 보조 압축을 연기할 수 있는 설정입니다.

- 읽기 및 쓰기 요청을 위해 예약 된 큐의 백분율 조정 등의 실험적 설정

## <a name="next-steps"></a>다음 단계

문제가 해결 되지 않은 상태로 남아 있으면 다음 채널 중 하나를 방문 하 여 더 많은 지원을 받을 수 있습니다.

- Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

- 을 사용 [@AzureSupport](https://twitter.com/azuresupport)하 여 연결 합니다. 사용자 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

- 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. Microsoft Azure 구독에는 구독 관리 및 청구 지원에 대 한 액세스 권한이 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
