---
title: 비용 예측
titleSuffix: Azure Cognitive Search
description: 청구 가능한 이벤트, 가격 책정 모델 및 Cognitive Search 서비스 실행 비용을 관리 하기 위한 팁에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539545"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스의 비용을 예측 하 고 관리 하는 방법

이 문서에서는 Azure Cognitive Search 서비스를 실행 하는 비용을 관리 하기 위한 가격 책정 모델, 청구 가능한 이벤트 및 팁에 대해 알아봅니다.

## <a name="pricing-model"></a>가격 책정 모델

Azure Cognitive Search의 확장성 아키텍처는 다양 한 쿼리 또는 인덱싱 성능이 필요한 지 여부에 따라 용량을 변경 하 고 필요한 만큼만 지불 하도록 복제본과 파티션의 유연한 조합을 기반으로 합니다.

서비스 계층에 의해 설정 된 청구 요금을 곱하여 검색 서비스에서 사용 하는 리소스의 양은 서비스 실행 비용을 결정 합니다. 비용과 용량은 긴밀 하 게 바인딩되어 있습니다. 비용을 예측할 때 인덱싱 및 쿼리 워크 로드를 실행 하는 데 필요한 용량을 이해 하면 예상 되는 비용을 파악할 수 있습니다.

요금 청구를 위해 다음 두 가지 간단한 수식이 인식 됩니다.

| 수식 | 설명 |
|---------|-------------|
| **R x P = SU** | 사용 된 파티션 수를 곱하여 사용 된 복제본 수는 서비스에서 사용 하는 SU ( *검색 단위* )의 수량과 같습니다. SU는 리소스의 단위 이며, 파티션 또는 복제본이 될 수 있습니다. |
| **SU * 청구 요금 = 월별 지출** | 서비스를 프로 비전 한 계층의 청구 요금에 대 한 SUs의 수는 전체 월간 청구의 기본 결정입니다. 일부 기능이 나 워크 로드는 다른 Azure 서비스에 대 한 종속성을 가지 며,이를 통해 구독 수준에서 솔루션의 비용을 높일 수 있습니다. 아래 청구 가능한 이벤트 섹션은 청구서에 추가할 수 있는 기능을 식별 합니다. |

모든 서비스는 최소값으로 1SU(복제본 1개 x 파티션 1개)에서 시작합니다. 모든 서비스에 대 한 최대값은 36입니다. 이 최대 크기는 여러 가지 방법으로 연결할 수 있습니다. 예를 들어 6 개 파티션 x 복제본 6 개 또는 3 개의 파티션 x 12 복제본입니다. 총 용량을 사용 하는 것이 일반적입니다 (예: 3 개 복제본, 3-파티션 서비스는 9 개 SUs로 청구 됨). 올바른 조합은 [파티션 및 복제본 조합](search-capacity-planning.md#chart) 차트를 참조 하세요.

요금 청구 요금은 SU 당 매시간입니다. 각 계층의 속도가 점차 높아집니다. 상위 계층에는 더 큰 및 speedier 파티션이 제공 되며,이는 해당 계층에 대 한 전반적인 시간당 요금에 기여 합니다. [가격 정보](https://azure.microsoft.com/pricing/details/search/) 페이지에서 각 계층에 대 한 요금을 볼 수 있습니다.

대부분의 고객은 총 용량의 일부만 온라인 상태로 유지하고 나머지는 보류 상태로 둡니다. 요금 청구를 위해 SU 수식으로 계산 되는 온라인 상태로 전환 되는 파티션 및 복제본의 수는 시간별로 청구 되는 항목을 결정 합니다. 

## <a name="billable-events"></a>청구 가능한 이벤트

Azure Cognitive Search을 기반으로 하는 솔루션은 다음과 같은 방법으로 비용을 발생 시킬 수 있습니다.

+ 최소 구성 (단일 파티션 및 복제본)에서 기본 요금으로 연중 무휴 실행 되는 [서비스 자체의 비용](#service-costs) 입니다. 이를 서비스 실행의 고정 비용으로 간주할 수 있습니다.

+ 요금이 청구 될 때 비용이 증가 하는 용량 (복제본 또는 파티션)을 추가 합니다. 고가용성이 비즈니스 요구 사항인 경우 3 개의 복제본이 필요 합니다.

+ 대역폭 요금 (아웃 바운드 데이터 전송)

+ 특정 기능 또는 기능에 필요한 추가 기능 서비스:

  + AI 보강 ( [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)필요)
  + 기술 자료 저장소 ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)필요)
  + 증분 보강 ( [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)필요, AI 보강에 적용 됨)
  + 고객 관리 키 및 이중 암호화 ( [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)필요)
  + 인터넷이 아닌 액세스 모델의 개인 끝점 ( [Azure 개인 링크](https://azure.microsoft.com/pricing/details/private-link/)필요)

### <a name="service-costs"></a>서비스 비용

요금을 방지 하기 위해 "일시 중지" 될 수 있는 가상 머신 또는 다른 리소스와 달리 Azure Cognitive Search 서비스는 항상 독점적으로 사용 되는 하드웨어에서 사용할 수 있습니다. 따라서 서비스를 만드는 것은 서비스를 만들 때 시작 되 고 서비스를 삭제할 때 종료 되는 청구 가능 이벤트입니다. 

최소 요금은 청구 가능 요금에 첫 번째 검색 단위 (하나의 복제본 x 파티션)입니다. 이 구성 보다 작은 항목에서 서비스를 실행할 수 없기 때문에이 최소값은 서비스의 수명 동안 고정 됩니다. 

최소값을 초과 하 여 복제본과 파티션을 서로 독립적으로 추가할 수 있습니다. 복제본 및 파티션을 통해 용량이 증가 하면 다음 수식에 따라 청구서가 증가 합니다. **(복제본 x 파티션 x 청구 요금)**, 청구 되는 등급은 선택한 가격 책정 계층에 따라 달라 집니다.

검색 솔루션의 비용을 예측 하는 경우 가격 책정 및 용량이 선형이 아니라는 것을 염두에 두어야 합니다 (두 배의 용량은 비용 2 배 이상). 수식의 작동 방법에 대 한 예는 [복제본 및 파티션을 할당 하는 방법](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)을 참조 하세요.

### <a name="bandwidth-charges"></a>대역폭 요금

Azure 데이터 원본이 Azure Cognitive Search의 다른 지역에 있는 경우 [인덱서](search-indexer-overview.md) 를 사용 하면 청구에 영향을 줄 수 있습니다. 이 시나리오에서는 Azure 데이터 원본에서 Azure Cognitive Search로 아웃 바운드 데이터를 이동 하는 비용이 발생할 수 있습니다. 자세한 내용은 해당 하는 Azure 데이터 플랫폼의 가격 책정 페이지를 참조 하세요.

데이터와 동일한 지역에서 Azure Cognitive Search 서비스를 만드는 경우 데이터 송신 요금을 완전히 제거할 수 있습니다. [대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)의 일부 정보는 다음과 같습니다.

+ 인바운드 데이터: Microsoft는 Azure의 모든 서비스에 대 한 인바운드 데이터를 청구 하지 않습니다. 

+ 아웃 바운드 데이터: 아웃 바운드 데이터는 쿼리 결과를 나타냅니다. Cognitive Search는 아웃 바운드 데이터에 대해 요금이 부과 되지 않지만, 서비스가 서로 다른 지역에 있는 경우 Azure의 아웃 바운드 요금은 가능 합니다.

  이러한 요금은 실제로 Azure Cognitive Search 청구서에 포함 되지 않습니다. 다른 지역 또는 비 Azure 앱에 결과를 전송 하는 경우 전체 청구서에 반영 된 비용을 볼 수 있기 때문에 여기에 설명 되어 있습니다.

### <a name="ai-enrichment-with-cognitive-services"></a>Cognitive Services AI 보강

[AI 보강](cognitive-search-concept-intro.md)의 경우 azure Cognitive Search와 동일한 지역에 종 량 제 처리를 위한 S0 가격 책정 계층에서 [청구 가능한 azure Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)하도록 계획 해야 합니다. 연결 Cognitive Services와 관련 된 고정 비용은 없습니다. 필요한 처리에 대해서만 비용을 지불 하면 됩니다.

| 작업 | 청구 영향 |
|-----------|----------------|
| 문서 크랙, 텍스트 추출 | 무료 |
| 문서 크랙, 이미지 추출 | 문서에서 추출 된 이미지의 수에 따라 요금이 청구 됩니다. [인덱서 구성](/rest/api/searchservice/create-indexer#indexer-parameters)에서 **imageaction** 은 이미지 추출을 트리거하는 매개 변수입니다. **Imageaction** 이 "none" (기본값)으로 설정 된 경우 이미지 추출에 대 한 요금이 청구 되지 않습니다. 이미지 추출 율은 Azure Cognitive Search에 대 한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/search/) 페이지에 설명 되어 있습니다.|
| [기본 제공 인지 기술](cognitive-search-predefined-skills.md) | Cognitive Services를 직접 사용 하 여 작업을 수행한 것과 동일한 요금으로 청구 됩니다. |
| 사용자 지정 기술 | 사용자 지정 기술은 사용자가 제공 하는 기능입니다. 사용자 지정 기술 사용 비용은 사용자 지정 코드가 다른 요금제 서비스를 호출 하는지 여부에 따라 전적으로 달라 집니다. |

[증분 보강 (미리 보기)](cognitive-search-incremental-indexing-conceptual.md) 기능을 사용 하면 나중에 기술를 수정 하 여 시간과 비용을 절감 하는 데 필요한 인식 기술을 실행 하는 데 인덱서가 더 효율적일 수 있는 캐시를 제공할 수 있습니다.

## <a name="tips-for-managing-costs"></a>비용 관리를 위한 팁

다음 지침을 참조 하 여 비용을 절감 하거나 비용을 보다 효율적으로 관리할 수 있습니다.

+ 대역폭 요금을 최소화 하거나 제거 하기 위해 동일한 지역에 있거나 가능한 적은 수의 지역에 모든 리소스를 만듭니다.

+ 모든 서비스를 Azure Cognitive Search, Cognitive Services 및 솔루션에 사용 되는 다른 Azure 서비스와 같은 하나의 리소스 그룹으로 통합 합니다. Azure Portal에서 리소스 그룹을 찾고 **Cost Management** 명령을 사용 하 여 실제 및 예상 지출 정보를 파악 합니다.

+ 요청 및 응답이 데이터 센터 경계 내에 유지 되도록 프런트 엔드 응용 프로그램에 대해 Azure 웹 앱을 고려 합니다.

+ 인덱싱 등의 리소스를 많이 사용 하는 작업을 확장 한 다음 일반 쿼리 워크 로드에 대해 다시 조정 합니다. Azure Cognitive Search에 대 한 최소 구성 (하나의 파티션과 하나의 복제본으로 구성 된 SU)부터 시작 하 여 더 많은 용량이 필요한 경우를 나타내는 사용 패턴을 식별 하는 사용자 활동을 모니터링 합니다. 예측 가능한 패턴이 있는 경우 크기 조정을 활동과 동기화 할 수 있습니다 .이를 자동화 하는 코드를 작성 해야 합니다.

+ Cost management는 Azure 인프라에 기본 제공 됩니다. 비용, 도구 및 Api를 추적 하는 방법에 대 한 자세한 내용은 [청구 및 비용 관리](../cost-management-billing/cost-management-billing-overview.md) 를 검토 하세요.

임시 방식으로 검색 서비스를 종료 하는 것은 불가능 합니다. 전용 리소스는 항상 작동 하며 서비스 수명 동안 독점적으로 사용 하도록 할당 됩니다. 서비스 삭제는 영구적 이며 연결 된 데이터도 삭제 합니다.

서비스 자체를 기준으로 청구서를 낮추는 유일한 방법은 복제본과 파티션을 여전히 허용 되는 성능 및 [SLA 준수](https://azure.microsoft.com/support/legal/sla/search/v1_0/)를 제공 하는 수준으로 축소 하거나 더 낮은 계층에서 서비스를 만드는 것입니다 (S1 시간당 요금은 S2 또는 S3 요금 보다 낮음). 부하 예측의 하단에서 서비스를 프로 비전 한다고 가정 하 고, 서비스를 증가 시키는 경우 두 번째 대규모 계층화 된 서비스를 만들고 두 번째 서비스에서 인덱스를 다시 작성 한 다음 첫 번째 서비스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 구독에서 비용을 모니터링 하 고 관리 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Cost Management 및 청구 설명서](../cost-management-billing/cost-management-billing-overview.md)