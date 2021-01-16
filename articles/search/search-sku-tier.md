---
title: 가격 책정 계층 선택
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대 한 가격 책정 계층 (또는 Sku)에 대해 알아봅니다. 검색 서비스는 무료, 기본 및 표준 계층으로 프로 비전 할 수 있습니다. Standard는 다양 한 리소스 구성 및 용량 수준에서 사용할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 38ddfc2d3940bb9267edd6c5c683918c1fb5dc58
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251706"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 가격 책정 계층 선택

[검색 서비스를 만들](search-create-service-portal.md)때 서비스 수명 동안 고정 된 가격 책정 계층 (또는 SKU)을 선택 합니다. 예상 월별 비용은 포털의 **가격 책정 계층 선택** 페이지에 표시 됩니다. PowerShell을 통해 또는 Azure CLI를 대신 하 여 서비스를 만드는 경우 계층은 매개 변수를 통해 지정 됩니다 **`-Sku`** .

선택한 계층에 따라 다음이 결정 됩니다.

+ 서비스에서 만들 수 있는 인덱스 및 기타 개체의 최대 수
+ 파티션(실제 스토리지)의 크기와 속도
+ 고정 된 월별 비용으로 청구 가능 하 고 용량을 추가 하는 경우에는 증분 비용이 청구 됩니다.

일부 인스턴스에서 선택 하는 계층에 따라 [프리미엄 기능의](#premium-features)가용성이 결정 됩니다.

## <a name="tier-descriptions"></a>계층 설명

계층에는 **무료**, **기본**, **표준** 및 **저장소에 최적화** 됨이 포함 됩니다. 표준 및 저장소 최적화는 여러 구성 및 용량에서 사용할 수 있습니다. Azure Portal의 다음 스크린샷은 사용 가능한 계층, 가격 책정 (포털 및 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에서 찾을 수 있음)을 보여 줍니다. 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="가격 책정 계층 차트" border="true":::

**Free** 는 자습서 및 코드 샘플과 같이 작은 프로젝트에 대해 제한 된 검색 서비스를 만듭니다. 내부적으로 시스템 리소스는 여러 구독자 간에 공유 됩니다. 무료 서비스를 확장 하거나 중요 한 작업을 실행할 수 없습니다.

**기본** 및 **표준은** 가장 일반적으로 사용 되는 청구 가능 계층으로, 워크 로드를 보다 유연 하 게 확장할 수 있으므로 **표준이** 기본값입니다. 사용자의 제어에 따라 전용 리소스를 사용 하 여 대규모 프로젝트를 배포 하 고 성능을 최적화 하 고 용량을 늘릴 수 있습니다.

일부 계층은 특정 유형의 작업을 위해 설계 되었습니다. 예를 들어 **표준 3 고밀도 (S3 HD)** 는 s 3의 *호스팅 모드* 입니다. 여기서 기본 하드웨어는 많은 수의 작은 인덱스에 대해 최적화 되 고 배포할지에 시나리오를 위한 것입니다. S3 HD는 S3와 동일한 단가를 갖지만 하드웨어는 많은 수의 작은 인덱스에서 빠른 파일 읽기에 최적화 되어 있습니다.

**저장소에 최적화** 된 계층은 표준 계층 보다 TB 당 저렴 한 가격으로 더 큰 저장소 용량을 제공 합니다. 기본 단점은 특정 응용 프로그램 요구 사항에 대 한 유효성을 검사 해야 하는 더 높은 쿼리 대기 시간입니다. 이 계층의 성능 고려 사항에 대해 자세히 알아보려면 [성능 및 최적화 고려 사항](search-performance-optimization.md)을 참조 하세요.

[가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)의 [Azure Cognitive Search의 서비스 제한](search-limits-quotas-capacity.md) 문서 및 서비스를 프로 비전 할 때 포털 페이지에서 다양 한 계층에 대 한 자세한 내용을 확인할 수 있습니다.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>계층 별 기능 가용성

무료 계층을 포함 하 여 모든 계층에서 대부분의 기능을 사용할 수 있습니다. 경우에 따라 선택 하는 계층은 기능을 구현 하는 기능에 영향을 줍니다. 다음 표에서는 서비스 계층과 관련 된 기능 제약 조건을 설명 합니다.

| 기능 | 제한 사항 |
|---------|-------------|
| [인덱서에](search-indexer-overview.md) | S3 HD에서는 인덱서를 사용할 수 없습니다.  |
| [AI 보강](search-security-manage-encryption-keys.md) | 무료 계층에서 실행 되지만 권장 되지 않습니다. |
| [아웃 바운드 (인덱서) 액세스에 대 한 관리 또는 신뢰할 수 있는 id](search-howto-managed-identities-data-sources.md) | 무료 계층에서는 사용할 수 없습니다.|
| [고객 관리형 암호화 키](search-security-manage-encryption-keys.md) | 무료 계층에서는 사용할 수 없습니다. |
| [IP 방화벽 액세스](service-configure-firewall.md) | 무료 계층에서는 사용할 수 없습니다. |
| [개인 끝점 (Azure 개인 링크와 통합)](service-create-private-endpoint.md) | 검색 서비스에 대 한 인바운드 연결의 경우 무료 계층에서는 사용할 수 없습니다. 인덱서를 사용 하 여 다른 Azure 리소스에 대 한 아웃 바운드 연결의 경우 무료 또는 S3 HD에서 사용할 수 없습니다. 기술력과를 사용 하는 인덱서의 경우 무료, 기본, S1 또는 S3 HD에서 사용할 수 없습니다.|

리소스를 많이 사용 하는 기능은 충분 한 용량을 제공 하지 않으면 제대로 작동 하지 않을 수 있습니다. 예를 들어 [AI 보강](cognitive-search-concept-intro.md) 에는 데이터 집합이 작은 경우를 제외 하 고 무료 서비스에서 시간이 오래 걸리는 장기 실행 기술이 있습니다.

## <a name="upper-limits"></a>상한

계층은 서비스 자체의 최대 저장소 뿐만 아니라 만들 수 있는 인덱스, 인덱서, 데이터 원본, 기술력과 및 동의어 맵의 최대 수를 결정 합니다. 모든 제한을 완전히 차단 하려면 [Azure Cognitive Search의 서비스 제한](search-limits-quotas-capacity.md)을 참조 하세요. 

## <a name="partition-size-and-speed"></a>파티션 크기 및 속도

계층 가격 책정에는 기본 2gb부터 저장소에 최적화 된 (L2) 계층의 경우 최대 2tb의 범위에 해당 하는 파티션 별 저장소에 대 한 세부 정보가 포함 됩니다. 작업 속도, 대기 시간 및 전송 속도와 같은 기타 하드웨어 특성은 게시 되지 않지만 특정 솔루션 아키텍처에 맞게 설계 된 계층은 이러한 시나리오를 지원 하기 위한 기능이 포함 된 하드웨어를 기반으로 합니다.

## <a name="billing-rates"></a>요금 청구 요금

계층에는 더 비싼 하드웨어에서 실행 되거나 더 많은 비용이 드는 기능을 제공 하는 계층에 대 한 더 높은 요금을 가진 다른 요금 청구 요금이 있습니다. 청구 요금은 Azure Cognitive Search의 각 서비스 계층에 대 한 [azure 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/) 에 표시 됩니다.

서비스를 만들면 요금 청구 요금은 클록에 대 한 서비스 실행에 대 한 *고정 비용* 및 용량을 추가 하도록 선택 하는 경우 *증분 비용* 을 모두 갖습니다.

검색 서비스는 *파티션* (저장소) 및 *복제본* (쿼리 엔진의 인스턴스) 형식으로 컴퓨팅 리소스를 할당 합니다. 처음에는 각 서비스를 하나씩 사용 하 여 서비스를 만들고 청구 요금은 두 리소스를 모두 포함 합니다. 그러나 용량을 조정 하는 경우 비용이 청구 가능 요금으로 증가 또는 축소 됩니다.

다음 예제에서 이에 대해 설명합니다. 매월 $100의 가상 청구 요금을 가정 합니다. 하나의 파티션과 하나의 복제본에 대 한 초기 용량으로 검색 서비스를 유지 하는 경우 $100는 해당 월의 끝에 지불할 수 있습니다. 그러나 고가용성을 위해 두 개 이상의 복제본을 추가 하는 경우 월별 청구는 $300 (첫 번째 복제본-파티션 쌍의 경우 $100, 두 복제본의 경우 $200)로 증가 합니다.

이 가격 책정 모델은 검색 서비스에서 사용 하는 SU (숫자 *검색 단위* )에 청구 요금을 적용 하는 개념을 기반으로 합니다. 모든 서비스는 처음에 하나의 SU에서 프로 비전 되지만 더 큰 워크 로드를 처리 하기 위해 파티션 또는 복제본을 추가 하 여 SU를 늘릴 수 있습니다. 자세한 내용은 [검색 서비스의 비용을 계산 하는 방법](search-sku-manage-costs.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 선택 하는 가장 좋은 방법은 가장 저렴 한 계층으로 시작한 후 환경 및 테스트에서 서비스를 유지 하거나 더 높은 계층에서 새 서비스를 만들 수 있도록 결정 하는 것입니다. 다음 단계에서 제안 하는 테스트 수준을 수용할 수 있는 계층에서 검색 서비스를 만든 다음 비용 및 용량을 예측 하는 방법에 대 한 권장 사항을 확인 하는 것이 좋습니다.

+ [검색 서비스 만들기](search-create-service-portal.md)
+ [비용 예측](search-sku-manage-costs.md)
+ [용량 예상](search-sku-manage-costs.md)