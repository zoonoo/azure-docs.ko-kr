---
title: Azure HDInsight에서 Apache HBase 성능 문제 해결
description: Azure HDInsight에서 최적의 성능을 얻는 데 도움이 되는 다양한 Apache HBase 성능 튜닝 지침 및 팁입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942902"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight에서 Apache HBase 성능 문제 해결

이 문서에서는 Azure HDInsight에서 최적의 성능을 얻는 데 도움이 되는 다양한 Apache HBase 성능 튜닝 지침 및 팁을 설명합니다. 이러한 팁 중 상당수는 특정 워크로드 및 읽기/쓰기/검색 패턴에 따라 달라집니다. 프로덕션 환경에서 구성 변경 내용을 적용하기 전에 철저히 테스트하세요.

## <a name="hbase-performance-insights"></a>HBase 성능 인사이트

대부분의 HBase 작업에서 발생하는 가장 큰 병목 상태는 WAL(미리 쓰기 로그)입니다. 쓰기 성능에 심각한 영향을 줍니다. HDInsight HBase에는 구분된 스토리지-컴퓨팅 모델이 있습니다. 가상 머신에서 지역 서버를 호스팅하는 경우에도 데이터는 Azure Storage에 원격으로 저장됩니다. 최근까지는 WAL도 Azure Storage에 기록되었습니다. HDInsight에서 이 동작은 이러한 병목 상태를 증폭시켰습니다. [가속화된 쓰기](./apache-hbase-accelerated-writes.md) 기능은 이 문제를 해결하도록 설계되었습니다. Azure 프리미엄 SSD 관리 디스크에 WAL을 기록합니다. 이는 쓰기 성능에 큰 이점을 제공하며, 일부 쓰기 집약적 워크로드에서 발생하는 많은 문제를 해결하는 데 도움이 됩니다.

읽기 작업을 크게 개선하려면 [프리미엄 블록 Blob Storage 계정](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)을 원격 스토리지로 사용합니다. 이 옵션은 WAL 기능이 사용하도록 설정된 경우에만 가능합니다.

## <a name="compaction"></a>압축

압축은 커뮤니티에서 완전히 합의한 또 다른 잠재적인 병목 상태입니다. 기본적으로 HDInsight HBase 클러스터에서 주 압축은 사용하지 않도록 설정됩니다. 압축은 리소스 집약적 프로세스이지만 고객이 워크로드에 따라 완전히 자유롭게 예약할 수 있기 때문에 사용하지 않도록 설정됩니다. 예를 들어, 사용량이 적은 시간에 예약할 수 있습니다. 또한 스토리지가 로컬 Hadoop 분산 파일 시스템(HDFS)이 아닌 Azure Storage에서 지원하는 원격 스토리지이므로 데이터 지역성이 문제가 되지 않습니다.

고객은 편의를 위해 주 압축을 예약해야 합니다. 이러한 유지 관리를 수행하지 않으면 압축이 결국에는 읽기 성능에 부정적인 영향을 줍니다.

검색 작업의 경우 100밀리초보다 훨씬 더 긴 평균 대기 시간이 염려가 될 것입니다. 주 압축을 정확하게 예약했는지 확인하세요.

## <a name="apache-phoenix-workload"></a>Apache Phoenix 워크로드

다음 질문에 대한 답변을 통해 Apache Phoenix 워크로드를 더 잘 이해할 수 있습니다.

* 모든 "읽기"는 검색으로 변환됩니까?
    * 그렇다면 이러한 검색의 특징은 무엇입니까?
    * 적절한 인덱싱을 포함하여 이러한 검색에 대한 Phoenix 테이블 스키마 최적화를 완료했습니까?
* `EXPLAIN` 문을 사용하여 "읽기"가 생성하는 쿼리 계획을 이해했습니까?
* 쓰기가 "upsert-selects"입니까?
    * 그렇다면 역시 검색을 수행합니다. 검색의 예상 대기 시간은 평균적으로 약 100밀리초이며, HBase에서 포인트를 가져오는 경우 10밀리초입니다.  

## <a name="test-methodology-and-metrics-monitoring"></a>테스트 방법 및 메트릭 모니터링

Yahoo! Cloud Serving Benchmark, JMeter 또는 Pherf 와 같은 벤치마크를 사용하여 성능을 테스트하고 튜닝하려면 다음을 확인하세요.

- 클라이언트 머신에서 병목 상태가 발생하지 않습니다. 이를 확인하려면 클라이언트 머신의 CPU 사용량을 확인합니다.

- 스레드 수와 같은 클라이언트 쪽 구성은 클라이언트 대역폭을 모두 사용할 수 있게 적절히 튜닝됩니다.

- 테스트 결과는 정확하게, 체계적으로 기록됩니다.

쿼리가 갑자기 전보다 훨씬 더 악화되기 시작한 경우 애플리케이션 코드에서 잠재적 버그를 확인합니다. 갑자기 잘못된 데이터를 대량으로 생성합니까? 그렇다면 읽기 대기 시간이 늘어날 수 있습니다.

## <a name="migration-issues"></a>마이그레이션 문제

Azure HDInsight로 마이그레이션하는 경우에는 자동화(권장)를 통해 체계적이며 정확하게 마이그레이션을 수행해야 합니다. 수동 마이그레이션은 피하세요. 확인할 사항은 다음과 같습니다.

- 테이블 특성이 정확하게 마이그레이션되었는지 여부. 특성에는 압축, 블룸 필터 등이 포함될 수 있습니다.

- Phoenix 테이블의 솔트 설정은 새 클러스터 크기에 적절하게 매핑됩니다. 예를 들어 솔트 버킷 수는 클러스터의 작업자 노드 수의 배수여야 합니다. 그리고 핫 스폿 양의 계수인 배수를 사용해야 합니다.

## <a name="server-side-configuration-tunings"></a>서버 쪽 구성 튜닝

HDInsight HBase에서 HFile은 원격 스토리지에 저장됩니다. 캐시 누락이 있는 경우 온-프레미스 시스템의 데이터를 로컬 HDFS에서 지원하기 때문에 읽기 비용이 온-프레미스 시스템보다 많이 듭니다. 대부분의 시나리오에서는 이 문제를 피하기 위해 HBase 캐시(블록 캐시 및 버킷 캐시)를 지능적으로 사용할 수 있습니다. 문제를 피하지 못한 경우 프리미엄 블록 Blob 계정을 사용하면 이 문제를 해결할 수 있습니다. Windows Azure Storage Blob 드라이버는 `fs.azure.read.request.size`와 같은 특정 속성을 사용하여 확인된 읽기 모드(순차 및 임의)에 따라 블록의 데이터를 페치하므로 읽기 대기 시간이 더 긴 인스턴스가 계속 있을 수 있습니다. 경험적 실험을 통해 읽기 요청 블록 크기(`fs.azure.read.request.size`)를 512KB로 설정하고 HBase 테이블의 블록 크기를 동일한 크기로 맞추면 최상의 결과를 얻을 수 있다는 사실이 확인되었습니다.

대부분의 대규모 노드 클러스터에서 HDInsight HBase는 `regionservers`를 실행하는 가상 머신에 연결된 로컬 프리미엄 SSD에 `bucketcache`를 파일로 제공합니다. 오프-힙 캐시를 사용하면 대신 어느 정도 개선될 수 있습니다. 이 해결 방법은 가용 메모리 사용에 제한이 있으며, 파일 기반 캐시보다 작을 수 있으므로 항상 최선의 선택은 아닐 수 있습니다.

튜닝된 다른 몇 가지 특정 매개 변수는 다음과 같습니다. 이는 다양한 수준에서 도움이 되는 것으로 보입니다.

- `memstore` 크기를 기본값 128MB에서 256MB로 늘립니다. 일반적으로 이 설정은 대용량 쓰기 시나리오에 권장됩니다.

- 압축 전용 스레드 수를 기본 설정인 **1** 에서 **4** 로 늘립니다. 이 설정은 부 압축이 자주 목격되는 경우에 적합합니다.

- 저장소 제한으로 인한 `memstore` 플러시 차단을 방지합니다. 이 버퍼를 제공하려면 `Hbase.hstore.blockingStoreFiles` 설정을 **100** 으로 늘립니다.

- 플러시를 제어하려면 다음 설정을 사용합니다.

    - `Hbase.regionserver.maxlogs`: **140**(WAL 제한으로 인한 플러시 방지)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0.55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0.60**

- 스레드 풀 튜닝을 위한 Phoenix 관련 구성:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- 기타 Phoenix 관련 구성:

    - `Phoenix.rpc.index.handler.count`: **50**(인덱스 조회의 규모가 크거나 개수가 많은 경우)

    - `Phoenix.stats.updateFrequency`: **1시간**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1시간**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50MB**

- RPC 시간 제한: **3분**

   - RPC 시간 제한에는 HBase RPC 시간 제한, HBase 클라이언트 스캐너 시간 제한 및 Phoenix 쿼리 제한 시간이 포함됩니다. 
   - `hbase.client.scanner.caching` 매개 변수가 서버 끝과 클라이언트 끝에 동일한 값으로 설정되어 있는지 확인합니다. 동일하지 않은 경우 이 설정을 사용하면 `OutOfOrderScannerException`과 관련된 클라이언트 끝 오류가 발생합니다. 대규모 검색의 경우 이 설정을 낮은 값으로 설정해야 합니다. 여기서는 이 값을 **100** 으로 설정합니다.

## <a name="other-considerations"></a>기타 고려 사항

튜닝을 고려해야 할 추가 매개 변수는 다음과 같습니다.

- `Hbase.rs.cacheblocksonwrite` – HDI에서는 기본적으로 이 설정이 **true** 로 설정됩니다.

- 부 압축을 나중으로 연기할 수 있는 설정

- 읽기 및 쓰기 요청을 위해 예약된 큐의 백분율 조정 같은 실험적 설정

## <a name="next-steps"></a>다음 단계

문제가 해결되지 않을 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

- [@AzureSupport](https://twitter.com/azuresupport)에 연결합니다. 이는 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

- 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.