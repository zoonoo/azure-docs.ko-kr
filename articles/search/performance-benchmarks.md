---
title: 성능 벤치마크
titleSuffix: Azure Cognitive Search
description: 다양한 성능 벤치마크를 통해 Azure Cognitive Search의 성능에 대해 알아보기
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 2da5a39710e7bbce2e610ef7850b9284bc5c4309
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111441472"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Azure Cognitive Search 성능 벤치마크

Azure Cognitive Search의 성능은 검색 서비스의 크기 및 전송 중인 쿼리 유형을 비롯한 [다양한 요인](search-performance-tips.md)에 따라 달라집니다. 워크로드에 필요한 검색 서비스의 크기를 예측하는 데 도움이 되도록 여러 벤치마크를 실행하여 다양한 검색 서비스 및 구성에 대한 성능을 문서화했습니다. 이러한 벤치마크는 서비스에서 특정 수준의 성능을 보장하지는 않지만, 기대할 수 있는 성능에 대해 알 수 있습니다.

다양한 사용 사례의 범위를 다루기 위해 두 가지 주요 시나리오에 대해 벤치마크를 실행했습니다.

* **전자상거래 검색** - 이 벤치마크는 실제 전자상거래 시나리오를 에뮬레이트하며 북유럽 전자상거래 회사 [CDON](https://cdon.com)에 기반하고 있습니다.
* **문서 검색** - 이 시나리오는 [Semantic Scholar](https://www.aclweb.org/anthology/2020.acl-main.447/)의 전체 텍스트 문서에 대한 키워드 검색으로 구성되어 있습니다. 이는 일반적인 문서 검색 솔루션을 에뮬레이트합니다.

이러한 시나리오는 다양한 사용 사례를 반영하지만 모든 시나리오는 다르므로 항상 개별 워크로드의 성능 테스트를 권장합니다. 사용자의 서비스에 대해 유사한 테스트를 실행할 수 있도록 [JMeter를 사용하는 성능 테스트 솔루션](https://github.com/Azure-Samples/azure-search-performance-testing)을 게시했습니다.

## <a name="testing-methodology"></a>테스트 방법

Azure Cognitive Search의 성능을 벤치마크하기 위해 서로 다른 계층 및 복제본/파티션 조합에서 서로 다른 두 시나리오에 대해 테스트를 실행했습니다.

이러한 벤치마크를 만들기 위해 다음 방법을 사용했습니다.

1. 테스트는 `X`QPS(초당 쿼리)에서 시작해서 180초 동안 진행됩니다. 이는 일반적으로 5 또는 10QPS였습니다.
2. 그런 다음 QPS가 `X`만큼 늘어나고 또 180초 동안 실행되었습니다.
3. 180초마다, 테스트는 평균 대기 시간이 늘어나 1000ms를 넘거나 쿼리의 99% 미만이 성공할 때까지 `X`QPS만큼 늘어났습니다.

아래 그래프는 테스트의 쿼리 로드가 어떻게 보이는지에 대한 시각적 예시를 제공합니다.

![예 테스트](./media/performance-benchmarks/example-test.png)

각 시나리오는 적어도 10,000개의 고유 쿼리를 사용하여 캐싱에 의해 테스트가 지나치게 왜곡되는 것을 방지합니다.

> [!IMPORTANT]
> 이러한 테스트에는 쿼리 워크로드만 포함됩니다. 많은 양의 인덱싱 작업이 예상되는 경우 이를 예측 및 성능 테스트에 포함해야 합니다. 인덱싱 시뮬레이션에 대한 샘플 코드는 이 [자습서](tutorial-optimize-indexing-push-api.md)에서 찾을 수 있습니다.

### <a name="definitions"></a>정의

- **최대 QPS** - 아래의 최대 QPS 숫자는 1000ms 아래로 유지되는 평균 대기 시간 및 제한 없이 99%의 쿼리가 성공적으로 완료된 테스트에서 달성한 가장 높은 QPS를 기준으로 합니다.

- **최대 QPS 백분율** - 특정 테스트에 대해 달성한 최대 QPS의 백분율입니다. 예를 들어, 지정된 테스트가 최대 100QPS에 도달한 경우 최대 QPS의 20%는 20QPS입니다.

- **대기 시간** - 쿼리에 대한 서버의 대기 시간이며 이러한 숫자는 [RTT(왕복 연기)](https://en.wikipedia.org/wiki/Round-trip_delay)를 포함하지 않습니다. 아래의 값은 ms(밀리초) 단위입니다.

### <a name="disclaimer"></a>고지 사항

이러한 벤치마크를 실행하는 데 사용한 코드는 [여기](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools)에서 확인할 수 있습니다. 아래 벤치마크보다 [JMeter 성능 테스트 솔루션](https://github.com/Azure-Samples/azure-search-performance-testing)을 사용했을 때 QPS 수준이 약간 더 낮다는 점에 주목할 필요가 있습니다. 차이는 테스트 스타일의 차이에 기인할 수 있습니다. 이는 가능한 한 프로덕션 워크로드와 유사한 성능 테스트를 만드는 것의 중요성을 나타냅니다.

이러한 벤치 마크는 서비스의 특정 수준의 성능을 보장하지 않지만 시나리오에 따라 기대할 수 있는 성능에 대한 아이디어를 제공할 수 있습니다.

질문이나 문제가 있으면 azuresearch_contact@microsoft.com으로 연락하세요.

## <a name="benchmark-1-e-commerce-search"></a>벤치마크 1: 전자상거래 검색

:::row:::
   :::column span="1":::
      ![CDON 로고](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      이 벤치마크는 스웨덴, 핀란드, 노르웨이 및 덴마크에서 운영되는 북유럽 지역 최대 온라인 마켓플레이스인 전자상거래 회사인 [CDON](https://cdon.com)과 협력하여 작성되었습니다. 1,500개의 가맹점을 통해 CDON은 800만 개가 넘는 제품을 포함하는 다양한 제품을 제공합니다. 2020 년 CDON은 1억 2천만 명 이상의 방문자와 2백만 명의 활성 고객을 보유했습니다. [이 문서](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/)에서 CDON의 Azure Cognitive Search 사용에 대해 자세히 알아볼 수 있습니다.
   :::column-end:::
:::row-end:::

이러한 테스트를 실행하기 위해 CDON의 프로덕션 검색 인덱스 스냅샷과 해당 [웹 사이트](https://cdon.com)에서 수천 개의 고유 쿼리를 사용했습니다.

### <a name="scenario-details"></a>시나리오 세부 정보

- **문서 수**: 6,000,000개 
- **인덱스 크기**: 20GB
- **인덱스 스키마**: 총 250개 필드(검색 가능한 필드 25개 및 패싯 가능/필터링 가능 필드 200개)가 있는 넓은 인덱스
- **쿼리 유형**: 패싯, 필터, 순서 지정 및 점수 매기기 프로필을 포함하는 전체 텍스트 검색 쿼리

### <a name="s1-performance"></a>S1 성능

#### <a name="queries-per-second"></a>초당 쿼리 수

아래 차트에서는 서비스가 연장된 시간 동안 처리할 수 있었던 가장 높은 쿼리 로드를 QPS(초당 쿼리 수)로 보여 줍니다.

![유지 관리 가능한 가장 높은 QPS 전자상거래 s1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>쿼리 대기 시간

쿼리 대기 시간은 서비스 부하에 따라 다르며 스트레스가 높은 서비스는 평균 쿼리 대기 시간이 더 높습니다. 아래 표에서는 세 가지 다른 사용 수준에 대해 쿼리 대기 시간의 25번째, 50번째, 75번째, 90번째, 95번째 및 99번째 백분위수를 보여 줍니다.

| 최대 QPS 백분율  | 평균 대기 시간 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104ms  | 35ms  | 115ms   | 177ms | 257ms | 738ms |
| 50%  | 140ms  | 47ms  | 144ms   | 241ms | 400ms | 1175ms |
| 80%  | 239ms  | 77ms  | 248ms   | 466ms | 763ms | 1752ms | 


### <a name="s2-performance"></a>S2 성능

#### <a name="queries-per-second"></a>초당 쿼리 수

아래 차트에서는 서비스가 연장된 시간 동안 처리할 수 있었던 가장 높은 쿼리 로드를 QPS(초당 쿼리 수)로 보여 줍니다.

![유지 관리 가능한 가장 높은 QPS 전자상거래 s2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>쿼리 대기 시간

쿼리 대기 시간은 서비스 부하에 따라 다르며 스트레스가 높은 서비스는 평균 쿼리 대기 시간이 더 높습니다. 아래 표에서는 세 가지 다른 사용 수준에 대해 쿼리 대기 시간의 25번째, 50번째, 75번째, 90번째, 95번째 및 99번째 백분위수를 보여 줍니다.

| 최대 QPS 백분율  | 평균 대기 시간 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56ms | 21ms | 68ms  | 106ms  | 132ms | 210ms | 
| 50%  | 71ms  | 26ms  | 83ms   | 132ms | 177ms | 329ms |
| 80%  | 140ms  | 47ms  | 153ms   | 293ms | 452ms | 924ms | 

### <a name="s3-performance"></a>S3 성능

#### <a name="queries-per-second"></a>초당 쿼리 수

아래 차트에서는 서비스가 연장된 시간 동안 처리할 수 있었던 가장 높은 쿼리 로드를 QPS(초당 쿼리 수)로 보여 줍니다.

![유지 관리 가능한 가장 높은 QPS 전자상거래 s3](./media/performance-benchmarks/s3-ecom-qps.png)

이 경우 두 번째 파티션을 추가하면 최대 QPS가 크게 증가하지만 세 번째 파티션을 추가하면 한계 수익이 감소한다는 것을 알 수 있습니다. 겨우 두 개의 파티션만 있는 S3의 활성 메모리로 이미 모든 데이터를 끌어오는 중이기 때문에 개선이 더 작을 수 있습니다.

#### <a name="query-latency"></a>쿼리 대기 시간

쿼리 대기 시간은 서비스 부하에 따라 다르며 스트레스가 높은 서비스는 평균 쿼리 대기 시간이 더 높습니다. 아래 표에서는 세 가지 다른 사용 수준에 대해 쿼리 대기 시간의 25번째, 50번째, 75번째, 90번째, 95번째 및 99번째 백분위수를 보여 줍니다.

| 최대 QPS 백분율  | 평균 대기 시간 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 50ms  | 20밀리초  | 64ms   | 83ms | 98ms | 160ms |
| 50%  | 62ms  | 24ms  | 80ms   | 107ms | 130ms | 253ms |
| 80%  | 115ms  | 38ms  | 121ms   | 218ms | 352ms | 828ms |

## <a name="benchmark-2-document-search"></a>벤치마크 2: 문서 검색

### <a name="scenario-details"></a>시나리오 세부 정보

- **문서 수**: 750만 개
- **인덱스 크기**: 22GB
- **인덱스 스키마**: 23개 필드(검색 가능 8개, 필터링 가능/패싯 가능 10개)
- **쿼리 유형**: 패싯으로 키워드 검색 및 적중 강조 표시

### <a name="s1-performance"></a>S1 성능

#### <a name="queries-per-second"></a>초당 쿼리 수

아래 차트에서는 서비스가 연장된 시간 동안 처리할 수 있었던 가장 높은 쿼리 로드를 QPS(초당 쿼리 수)로 보여 줍니다.

![유지 관리 가능한 가장 높은 QPS 문서 검색 s1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>쿼리 대기 시간

쿼리 대기 시간은 서비스 부하에 따라 다르며 스트레스가 높은 서비스는 평균 쿼리 대기 시간이 더 높습니다. 아래 표에서는 세 가지 다른 사용 수준에 대해 쿼리 대기 시간의 25번째, 50번째, 75번째, 90번째, 95번째 및 99번째 백분위수를 보여 줍니다.

| 최대 QPS 백분율  | 평균 대기 시간 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 67ms  | 44ms  | 77ms   | 103ms | 126ms | 216밀리초 |
| 50%  | 93ms  | 59ms  | 110ms   | 150ms | 184ms | 304ms |
| 80%  | 150ms  | 96ms  | 184ms   | 248ms | 297ms | 424ms |

### <a name="s2-performance"></a>S2 성능

#### <a name="queries-per-second"></a>초당 쿼리 수

아래 차트에서는 서비스가 연장된 시간 동안 처리할 수 있었던 가장 높은 쿼리 로드를 QPS(초당 쿼리 수)로 보여 줍니다.

![유지 관리 가능한 가장 높은 QPS 문서 검색 s2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>쿼리 대기 시간

쿼리 대기 시간은 서비스 부하에 따라 다르며 스트레스가 높은 서비스는 평균 쿼리 대기 시간이 더 높습니다. 아래 표에서는 세 가지 다른 사용 수준에 대해 쿼리 대기 시간의 25번째, 50번째, 75번째, 90번째, 95번째 및 99번째 백분위수를 보여 줍니다.

| 최대 QPS 백분율  | 평균 대기 시간 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 45ms  | 31ms  | 55ms   | 73ms | 84ms | 109ms |
| 50%  | 63ms  | 39ms  | 81ms   | 106ms | 123ms | 163ms |
| 80%  | 115ms  | 73ms  | 145ms   | 191ms | 224ms | 291ms |

### <a name="s3-performance"></a>S3 성능

#### <a name="queries-per-second"></a>초당 쿼리 수

아래 차트에서는 서비스가 연장된 시간 동안 처리할 수 있었던 가장 높은 쿼리 로드를 QPS(초당 쿼리 수)로 보여 줍니다.

![유지 관리 가능한 가장 높은 QPS 문서 검색 s3](./media/performance-benchmarks/s3-docsearch-qps.png)

#### <a name="query-latency"></a>쿼리 대기 시간

쿼리 대기 시간은 서비스 부하에 따라 다르며 스트레스가 높은 서비스는 평균 쿼리 대기 시간이 더 높습니다. 아래 표에서는 세 가지 다른 사용 수준에 대해 쿼리 대기 시간의 25번째, 50번째, 75번째, 90번째, 95번째 및 99번째 백분위수를 보여 줍니다.

| 최대 QPS 백분율  | 평균 대기 시간 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 43ms  | 29ms  | 53ms   | 74ms | 86ms | 111ms |
| 50%  | 65ms  | 37ms  | 85ms   | 111ms | 128ms | 164ms |
| 80%  | 126ms  | 83ms  | 162ms   | 205ms | 233ms | 281ms |

## <a name="takeaways"></a>핵심 내용

이러한 벤치마크를 통해 Azure Cognitive Search가 제공하는 성능에 대해 알 수 있습니다. 서로 다른 계층에 있는 서비스 간의 차이를 확인할 수도 있습니다.

이러한 벤치마크의 몇 가지 주요 핵심 내용은 다음과 같습니다.

* S2는 일반적으로 S1에 비해 쿼리 볼륨의 4배 이상을 처리할 수 있습니다.
* S2는 일반적으로 비슷한 쿼리 볼륨에서 S1보다 대기 시간이 짧습니다.
* 복제본을 추가할 때 서비스가 처리할 수 있는 QPS는 일반적으로 선형 확장됩니다(예: 한 복제본이 10QPS를 처리할 수 있으면 5개의 복제본은 보통 50QPS 처리 가능).
* 서비스의 부하가 높을수록 평균 대기 시간이 더 길어집니다.

또한 시나리오마다 성능이 크게 다를 수 있음을 알 수 있습니다. 기대하는 성능을 얻지 못하고 있는 경우 [더 나은 성능을 위한 팁](search-performance-tips.md)을 확인합니다.

## <a name="next-steps"></a>다음 단계

이제 성능 벤치마크를 살펴보았으므로 성능에 영향을 주는 Cognitive Search의 성능 및 주요 요인을 분석하는 방법에 대해 자세히 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [성능 분석](search-performance-analysis.md)
> [더 나은 성능을 위한 팁](search-performance-tips.md)
