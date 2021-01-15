---
title: 가격 책정 계층 선택
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search는 무료, 기본 및 표준 계층으로 프로 비전 할 수 있으며, Standard는 다양 한 리소스 구성 및 용량 수준에서 사용할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4c58968cb6a38a10433915ec8fa00336ccad301e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216413"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 가격 책정 계층 선택

[검색 서비스를 만들](search-create-service-portal.md)때 서비스 수명 동안 고정 된 가격 책정 계층을 선택 합니다. 선택한 계층에 따라 다음이 결정 됩니다.

+ 인덱스 및 만들 수 있는 기타 개체의 수량 (최대 제한)
+ 파티션(실제 스토리지)의 크기와 속도
+ 청구 가능 요금, 고정 월별 비용, 파티션 또는 복제본을 추가 하는 경우의 증분 비용

또한 몇 가지 [프리미엄 기능](#premium-features) 을 계층 요구 사항과 함께 제공 합니다.

## <a name="tier-descriptions"></a>계층 설명

계층에는 **무료**, **기본**, **표준** 및 **저장소에 최적화** 됨이 포함 됩니다. 표준 및 저장소 최적화는 여러 구성 및 용량에서 사용할 수 있습니다.

Azure Portal의 다음 스크린샷은 사용 가능한 계층, 가격 책정 (포털 및 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에서 찾을 수 있음)을 보여 줍니다. 

![Azure Cognitive Search의 가격 책정 계층](media/search-sku-tier/tiers.png "Azure Cognitive Search의 가격 책정 계층")

**Free** 는 자습서 및 코드 샘플과 같이 작은 프로젝트에 대해 제한 된 검색 서비스를 만듭니다. 내부적으로 복제본과 파티션은 여러 구독자 간에 공유 됩니다. 무료 서비스를 확장 하거나 중요 한 작업을 실행할 수 없습니다.

**기본** 및 **표준은** 가장 일반적으로 사용 되는 청구 가능 계층으로, **표준** 을 기본값으로 사용 합니다. 사용자의 제어에 따라 전용 리소스를 사용 하 여 대규모 프로젝트를 배포 하 고 성능을 최적화 하 고 용량을 늘릴 수 있습니다.

일부 계층은 특정 작업 유형에 맞게 최적화 되어 있습니다. 예를 들어 **표준 3 고밀도 (S3 HD)** 는 s 3의 *호스팅 모드* 입니다. 여기서 기본 하드웨어는 많은 수의 작은 인덱스에 대해 최적화 되 고 배포할지에 시나리오를 위한 것입니다. S3 HD는 S3와 동일한 단가를 갖지만 하드웨어는 많은 수의 작은 인덱스에서 빠른 파일 읽기에 최적화 되어 있습니다.

**저장소에 최적화** 된 계층은 표준 계층 보다 TB 당 저렴 한 가격으로 더 큰 저장소 용량을 제공 합니다. 기본 단점은 특정 응용 프로그램 요구 사항에 대 한 유효성을 검사 해야 하는 더 높은 쿼리 대기 시간입니다. 이 계층의 성능 고려 사항에 대해 자세히 알아보려면 [성능 및 최적화 고려 사항](search-performance-optimization.md)을 참조 하세요.

[가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)의 [Azure Cognitive Search의 서비스 제한](search-limits-quotas-capacity.md) 문서 및 서비스를 프로 비전 할 때 포털 페이지에서 다양 한 계층에 대 한 자세한 내용을 확인할 수 있습니다.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>계층 별 기능 가용성

무료 계층을 포함 하 여 모든 계층에서 대부분의 기능을 사용할 수 있습니다. 경우에 따라 선택 하는 계층은 기능을 구현 하는 기능에 영향을 줍니다. 다음 표에서는 서비스 계층과 관련 된 기능 제약 조건을 설명 합니다.

| 기능 | 제한 사항 |
|---------|-------------|
| [인덱서에](search-indexer-overview.md) | S3 HD에서는 인덱서를 사용할 수 없습니다.  |
| [AI 보강](search-security-manage-encryption-keys.md) | 무료 계층에서 실행 되지만 권장 되지 않습니다. |
| [고객 관리형 암호화 키](search-security-manage-encryption-keys.md) | 무료 계층에서는 사용할 수 없습니다. |
| [IP 방화벽 액세스](service-configure-firewall.md) | 무료 계층에서는 사용할 수 없습니다. |
| [개인 끝점 (Azure 개인 링크와 통합)](service-create-private-endpoint.md) | 검색 서비스에 대 한 인바운드 연결의 경우 무료 계층에서는 사용할 수 없습니다. 인덱서를 사용 하 여 다른 Azure 리소스에 대 한 아웃 바운드 연결의 경우 무료 또는 S3 HD에서 사용할 수 없습니다. 기술력과를 사용 하는 인덱서의 경우 무료, 기본, S1 또는 S3 HD에서 사용할 수 없습니다.|

리소스를 많이 사용 하는 기능은 충분 한 용량을 제공 하지 않으면 제대로 작동 하지 않을 수 있습니다. 예를 들어 [AI 보강](cognitive-search-concept-intro.md) 에는 데이터 집합이 작은 경우를 제외 하 고 무료 서비스에서 시간이 오래 걸리는 장기 실행 기술이 있습니다.

## <a name="billable-events"></a>청구 가능한 이벤트

Azure Cognitive Search을 기반으로 하는 솔루션은 다음과 같은 방법으로 비용을 발생 시킬 수 있습니다.

+ 최소 구성 (단일 파티션 및 복제본)에서 기본 요금으로 연중 무휴 실행 되는 [서비스 자체의 비용](#service-costs) 입니다. 이를 서비스 실행의 고정 비용으로 간주할 수 있습니다.

+ 요금이 청구 될 때 비용이 증가 하는 용량 (복제본 또는 파티션) 추가

+ 대역폭 요금 (아웃 바운드 데이터 전송)

+ 특정 기능 또는 기능에 필요한 추가 기능 서비스:

  + AI 보강 ( [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)필요)
  + 기술 자료 저장소 ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)필요)
  + 증분 보강 ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)필요, AI 보강에 적용 됨)
  + 고객 관리 키 및 이중 암호화 ( [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)필요)
  + 인터넷이 아닌 액세스 모델의 개인 끝점 ( [Azure 개인 링크](https://azure.microsoft.com/pricing/details/private-link/)필요)

### <a name="service-costs"></a>서비스 비용

요금을 방지 하기 위해 "일시 중지" 될 수 있는 가상 머신 또는 다른 리소스와 달리 Azure Cognitive Search 서비스는 항상 독점적으로 사용 되는 하드웨어에서 사용할 수 있습니다. 따라서 서비스를 만드는 것은 서비스를 만들 때 시작 되 고 서비스를 삭제할 때 종료 되는 청구 가능 이벤트입니다. 

최소 요금은 청구 가능 요금에 첫 번째 검색 단위 (하나의 복제본 x 파티션)입니다. 이 구성 보다 작은 항목에서 서비스를 실행할 수 없기 때문에이 최소값은 서비스의 수명 동안 고정 됩니다. 최소값을 초과 하 여 복제본과 파티션을 서로 독립적으로 추가할 수 있습니다. 복제본 및 파티션을 통해 용량이 증가 하는 경우 다음 수식에 따라 청구서가 증가 합니다. [(복제본 x 파티션 x rate)](#search-units). 여기서 청구 되는 등급은 선택한 가격 책정 계층에 따라 달라 집니다.

검색 솔루션의 비용을 예측 하는 경우 가격 책정 및 용량이 선형이 아닌 것을 염두에 두어야 합니다. (두 배의 용량으로 비용을 늘립니다.) 수식의 작동 방법에 대 한 예는 [복제본 및 파티션을 할당 하는 방법](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)을 참조 하세요.

### <a name="bandwidth-charges"></a>대역폭 요금

Azure 데이터 원본이 Azure Cognitive Search의 다른 지역에 있는 경우 [인덱서](search-indexer-overview.md) 를 사용 하면 청구에 영향을 줄 수 있습니다. 이 시나리오에서는 Azure 데이터 원본에서 Azure Cognitive Search로 아웃 바운드 데이터를 이동 하는 비용이 발생할 수 있습니다. 자세한 내용은 해당 하는 Azure 데이터 플랫폼의 가격 책정 페이지를 참조 하세요.

데이터와 동일한 지역에서 Azure Cognitive Search 서비스를 만드는 경우 데이터 송신 요금을 완전히 제거할 수 있습니다. [대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)의 일부 정보는 다음과 같습니다.

+ 인바운드 데이터: Microsoft는 Azure의 모든 서비스에 대 한 인바운드 데이터를 청구 하지 않습니다. 

+ 아웃 바운드 데이터: 아웃 바운드 데이터는 쿼리 결과를 나타냅니다. Cognitive Search는 아웃 바운드 데이터에 대해 요금이 부과 되지 않지만, 서비스가 서로 다른 지역에 있는 경우 Azure의 아웃 바운드 요금은 가능 합니다. 이러한 요금은 실제로 Azure Cognitive Search 청구서에 포함 되지 않습니다. 다른 지역 또는 비 Azure 앱에 결과를 전송 하는 경우 전체 청구서에 반영 된 비용을 볼 수 있기 때문에 여기에 설명 되어 있습니다.

### <a name="ai-enrichment-with-cognitive-services"></a>Cognitive Services AI 보강

[AI 보강](cognitive-search-concept-intro.md)의 경우 azure Cognitive Search와 동일한 지역에 종 량 제 처리를 위한 S0 가격 책정 계층에서 [청구 가능한 azure Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)하도록 계획 해야 합니다. 연결 Cognitive Services와 관련 된 고정 비용은 없습니다. 필요한 처리에 대해서만 비용을 지불 하면 됩니다.

| 작업 | 청구 영향 |
|-----------|----------------|
| 문서 크랙, 텍스트 추출 | 무료 |
| 문서 크랙, 이미지 추출 | 문서에서 추출 된 이미지의 수에 따라 요금이 청구 됩니다. [인덱서 구성](/rest/api/searchservice/create-indexer#indexer-parameters)에서 **imageaction** 은 이미지 추출을 트리거하는 매개 변수입니다. **Imageaction** 이 "none" (기본값)으로 설정 된 경우 이미지 추출에 대 한 요금이 청구 되지 않습니다. 이미지 추출 율은 Azure Cognitive Search에 대 한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/search/) 페이지에 설명 되어 있습니다.|
| [기본 제공 인지 기술](cognitive-search-predefined-skills.md) | Cognitive Services를 직접 사용 하 여 작업을 수행한 것과 동일한 요금으로 청구 됩니다. |
| 사용자 지정 기술 | 사용자 지정 기술은 사용자가 제공 하는 기능입니다. 사용자 지정 기술 사용 비용은 사용자 지정 코드가 다른 요금제 서비스를 호출 하는지 여부에 따라 전적으로 달라 집니다. |

[증분 보강 (미리 보기)](cognitive-search-incremental-indexing-conceptual.md) 기능을 사용 하면 나중에 기술를 수정 하 여 시간과 비용을 절감 하는 데 필요한 인식 기술을 실행 하는 데 인덱서가 더 효율적일 수 있는 캐시를 제공할 수 있습니다.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>청구 수식 (R x P = SU)

Azure Cognitive Search 작업을 이해 하는 가장 중요 한 청구 개념은 *검색 단위* (SU)입니다. Azure Cognitive Search는 인덱싱 및 쿼리에 대 한 복제본과 파티션 모두에 종속 되기 때문에 하나 또는 다른 방법으로 청구 하는 것은 의미가 없습니다. 대신, 청구는 둘 다의 복합을 기준으로 합니다.

SU는 서비스에서 사용 하는 *복제본* 및 *파티션* **(R x P = SU)** 의 곱입니다.

모든 서비스는 최소값으로 1SU(복제본 1개 x 파티션 1개)에서 시작합니다. 모든 서비스에 대 한 최대값은 36입니다. 이 최대 크기는 여러 가지 방법으로 연결할 수 있습니다. 예를 들어 6 개 파티션 x 복제본 6 개 또는 3 개의 파티션 x 12 복제본입니다. 총 용량을 사용 하는 것이 일반적입니다 (예: 3 개 복제본, 3-파티션 서비스는 9 개 SUs로 청구 됨). 올바른 조합은 [파티션 및 복제본 조합](search-capacity-planning.md#chart) 차트를 참조 하세요.

요금 청구 요금은 SU 당 매시간입니다. 각 계층의 속도가 점차 높아집니다. 상위 계층에는 더 큰 및 speedier 파티션이 제공 되며,이는 해당 계층에 대 한 전반적인 시간당 요금에 기여 합니다. [가격 정보](https://azure.microsoft.com/pricing/details/search/) 페이지에서 각 계층에 대 한 요금을 볼 수 있습니다.

대부분의 고객은 총 용량의 일부만 온라인 상태로 유지하고 나머지는 보류 상태로 둡니다. 요금 청구를 위해 SU 수식으로 계산 되는 온라인 상태로 전환 되는 파티션 및 복제본의 수는 시간별로 청구 되는 항목을 결정 합니다.

## <a name="next-steps"></a>다음 단계

Cost management는 용량 계획의 필수적인 부분입니다. 다음 단계로 용량을 예측 하 고 비용을 관리 하는 방법에 대 한 지침을 보려면 다음 문서를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에서 비용을 관리 하 고 용량을 계산 하는 방법](search-sku-manage-costs.md)