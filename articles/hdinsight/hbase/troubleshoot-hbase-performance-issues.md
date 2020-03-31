---
title: Azure HDInsight에서 Apache HBase 성능 문제 해결
description: Azure HDInsight에서 최적의 성능을 얻기 위한 다양한 아파치 HBase 성능 튜닝 지침 및 팁
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887158"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight에서 Apache HBase 성능 문제 해결

이 문서에서는 Azure HDInsight에서 최적의 성능을 얻기 위한 다양한 아파치 HBase 성능 튜닝 지침 및 팁에 대해 설명합니다. 이러한 팁의 대부분은 특정 워크로드 및 읽기/쓰기/스캔 패턴에 따라 달라집니다. 프로덕션 환경에서 구성 변경 내용을 적용하기 전에 철저하게 테스트합니다.

## <a name="hbase-performance-insights"></a>HBase 성능 인사이트

대부분의 HBase 워크로드에서 가장 큰 병목 현상은 WAL(미리 쓰기 로그)입니다. 쓰기 성능에 심각한 영향을 미칩니다. HDInsight HBase에는 분리된 스토리지 컴퓨팅 모델이 있습니다. 데이터는 가상 시스템이 지역 서버를 호스트하는 경우에도 Azure Storage에 원격으로 저장됩니다. 최근까지 WAL은 Azure 저장소에도 기록되었습니다. HDInsight에서 이 동작은 이 병목 현상을 증폭시켰습니다. [가속 쓰기](./apache-hbase-accelerated-writes.md) 기능은 이 문제를 해결하도록 설계되었습니다. AZURE 프리미엄 SSD 관리 디스크에 WAL을 씁니다. 이렇게 하면 쓰기 성능이 크게 향상되며 쓰기 집약적인 일부 워크로드에서 직면한 많은 문제에 도움이 됩니다.

읽기 작업을 크게 개선하려면 [프리미엄 블록 Blob 저장소 계정을](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) 원격 저장소로 사용합니다. 이 옵션은 WAL 기능이 활성화된 경우에만 가능합니다.

## <a name="compaction"></a>압축

다짐은 지역 사회에서 근본적으로 합의된 또 다른 잠재적인 병목 현상입니다. 기본적으로 HDInsight HBase 클러스터에서는 주요 압축이 비활성화됩니다. 리소스집약적인 프로세스임에도 불구하고 고객은 워크로드에 따라 일정을 잡을 수 있는 완전한 유연성을 가지므로 압축이 비활성화됩니다. 예를 들어 사용량이 많은 시간대에 예약할 수 있습니다. 또한 저장소가 로컬 Hadoop 분산 파일 시스템(HDFS)이 아닌 원격(Azure Storage에서 백업)이기 때문에 데이터 지역성은 문제가 되지 않습니다.

고객은 편의를 위해 주요 다짐을 예약해야 합니다. 이 유지 관리를 하지 않으면 압축은 장기적으로 읽기 성능에 부정적인 영향을 미칩니다.

검사 작업의 경우 100밀리초보다 훨씬 높은 평균 대기 시간을 우려의 원인이 되어야 합니다. 주요 다짐이 정확하게 예약되었는지 확인합니다.

## <a name="apache-phoenix-workload"></a>아파치 피닉스 워크로드

다음 질문에 답하면 아파치 피닉스의 워크로드를 더 잘 이해하는 데 도움이 됩니다.

* 모든 "읽기"가 스캔으로 변환되고 있습니까?
    * 그렇다면 이러한 검사의 특징은 무엇입니까?
    * 적절한 인덱싱을 포함하여 이러한 검사에 대해 Phoenix 테이블 스키마를 최적화했습니까?
* "읽기"가 `EXPLAIN` 생성하는 쿼리 계획을 이해하기 위해 문을 사용했습니까?
* 당신의 글은 "upsert-선택"입니까?
    * 그렇다면, 그들은 또한 스캔을하고있을 것입니다. 검사에 대한 예상 대기 시간은 HBase에서 얻는 포인트의 경우 10밀리초와 비교하여 약 100밀리초입니다.  

## <a name="test-methodology-and-metrics-monitoring"></a>테스트 방법론 및 메트릭 모니터링

당신은 야후와 같은 벤치 마크를 사용하는 경우! 성능을 테스트하고 조정하기 위해 클라우드 제공 벤치마크, JMeter 또는 Pherf는 다음을 수행해야 합니다.

- 클라이언트 컴퓨터는 병목 현상이 되지 않습니다. 이렇게 하려면 클라이언트 컴퓨터에서 CPU 사용량을 확인합니다.

- 스레드 수와 같은 클라이언트 측 구성은 클라이언트 대역폭을 포화하도록 적절하게 조정됩니다.

- 테스트 결과는 정확하고 체계적으로 기록됩니다.

쿼리가 갑자기 이전보다 훨씬 더 나빠지기 시작한 경우 응용 프로그램 코드에서 잠재적인 버그를 확인합니다. 갑자기 많은 양의 잘못된 데이터가 생성되고 있습니까? 이 경우 읽기 대기 시간을 늘릴 수 있습니다.

## <a name="migration-issues"></a>마이그레이션 문제

Azure HDInsight로 마이그레이션하는 경우 자동화를 통해 체계적이고 정확하게 마이그레이션이 수행되는지 확인합니다. 수동 마이그레이션을 피하십시오. 확인할 사항은 다음과 같습니다.

- 테이블 특성은 정확하게 마이그레이션됩니다. 특성은 압축, 블룸 필터 등으로 포함될 수 있습니다.

- Phoenix 테이블의 솔딩 설정은 새 클러스터 크기에 맞게 매핑됩니다. 예를 들어 솔트 버킷 수는 클러스터의 작업자 노드 수의 배수여야 합니다. 그리고 핫 스포팅 량의 한 요소인 배수를 사용해야 합니다.

## <a name="server-side-configuration-tunings"></a>서버 측 구성 튜닝

HDInsight HBase에서 HFiles는 원격 스토리지에 저장됩니다. 캐시 누락이 발생하면 온-프레미스 시스템의 데이터가 로컬 HDFS에 의해 뒷받침되므로 읽기 비용이 온-프레미스 시스템보다 높습니다. 대부분의 시나리오에서 HBase 캐시(블록 캐시 및 버킷 캐시)를 지능적으로 사용하면 이 문제를 피할 수 있습니다. 문제가 회피되지 않는 경우 프리미엄 블록 Blob 계정을 사용하면 이 문제가 도움이 될 수 있습니다. Windows Azure Storage Blob 드라이버는 읽기 모드(순차적 대 임의)로 결정되는 내용에 따라 블록에서 데이터를 가져오는 것과 같은 `fs.azure.read.request.size` 특정 속성에 의존하므로 읽기가 있는 더 높은 대기 시간의 인스턴스가 계속 있을 수 있습니다. 경험적 실험을 통해 읽기 요청 블록 크기()`fs.azure.read.request.size`를 512KB로 설정하고 HBase 테이블의 블록 크기를 동일한 크기로 일치하면 실제로 최상의 결과를 얻을 수 있습니다.

대부분의 대형 노드 클러스터의 경우 HDInsight HBase는 실행되는 `bucketcache` `regionservers`가상 시스템에 연결된 로컬 프리미엄 SSD의 파일로 제공합니다. 대신 힙 오프 캐시를 사용하면 몇 가지 개선이 제공 될 수 있습니다. 이 해결 방법에는 사용 가능한 메모리를 사용하고 파일 기반 캐시보다 작을 수 있으므로 항상 최선의 선택이 아닐 수 있습니다.

다음은 우리가 조정 한 다른 특정 매개 변수 중 일부이며, 다양한 각도에 도움이되는 것처럼 보였습니다.

- 크기를 `memstore` 기본 값 128MB에서 256MB로 늘립니다. 일반적으로 이 설정은 쓰기가 많은 시나리오에 권장됩니다.

- 압축 전용 스레드 수를 기본 설정 **1에서** **4로**늘립니다. 이 설정은 빈번한 사소한 압축을 관찰하는 경우에 관련이 있습니다.

- 저장소 `memstore` 제한으로 인해 플러시를 차단하지 마십시오. 이 버퍼를 제공하려면 `Hbase.hstore.blockingStoreFiles` 설정을 **100으로**늘립니다.

- 플러시를 제어하려면 다음 설정을 사용합니다.

    - `Hbase.regionserver.maxlogs`: **140** (WAL 제한으로 인해 플러시 방지)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0.60**

- 스레드 풀 튜닝을 위한 피닉스 별 구성:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- 기타 피닉스 관련 구성:

    - `Phoenix.rpc.index.handler.count`: **50** (인덱스 조회가 크거나 많은 경우)

    - `Phoenix.stats.updateFrequency`: **1시간**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1시간**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC 시간 초과: **3분**

   - RPC 시간 시간에는 HBase RPC 시간 시간, HBase 클라이언트 스캐너 시간 시간 및 Phoenix 쿼리 시간 시간이 포함됩니다. 
   - 매개 변수가 `hbase.client.scanner.caching` 서버 끝과 클라이언트 끝 모두에서 동일한 값으로 설정되어 있는지 확인합니다. 동일하지 않은 경우 이 설정은 와 관련된 클라이언트 끝 `OutOfOrderScannerException`오류로 연결됩니다. 이 설정은 큰 검사의 경우 낮은 값으로 설정해야 합니다. 이 값을 **100으로**설정합니다.

## <a name="other-considerations"></a>기타 고려 사항

다음은 튜닝을 고려할 추가 매개 변수입니다.

- `Hbase.rs.cacheblocksonwrite`– 기본적으로 HDI에서 이 설정은 **true로**설정됩니다.

- 나중에 사소한 압축을 연기할 수 있는 설정입니다.

- 읽기 및 쓰기 요청을 위해 예약된 큐의 백분율 조정과 같은 실험적 설정입니다.

## <a name="next-steps"></a>다음 단계

문제가 해결되지 않은 상태로 남아 있으면 다음 채널 중 하나를 방문하여 더 많은 지원을 확인하십시오.

- Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

- 에 [@AzureSupport](https://twitter.com/azuresupport)연결합니다. 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결합니다.

- 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 Azure [지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토합니다. Microsoft Azure 구독에는 구독 관리 및 청구 지원에 대한 액세스가 포함되며 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공됩니다.
