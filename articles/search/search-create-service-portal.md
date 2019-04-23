---
title: 포털에서 Azure Search 서비스 만들기 - Azure Search
description: Azure Portal에서 Azure Search 리소스를 프로비전합니다. 리소스 그룹, 지역 및 SKU 또는 가격 책정 계층을 선택합니다.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c48acf7e9074ac3c5a7d19765a9524a411fa26c8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264032"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>포털에서 Azure Search서비스 만들기

Azure Search는 사용자 지정 앱에 검색 환경을 추가하는 데 사용되는 독립 실행형 리소스입니다. Azure Search는 다른 여러 Azure 서비스와 쉽게 통합되지만 네트워크 서버의 앱 또는 다른 클라우드 플랫폼에서 실행되는 소프트웨어를 통해 단독으로 실행할 수도 있습니다.

이 문서에서는 [Azure Portal](https://portal.azure.com/)에서 Azure Search 리소스를 만드는 방법을 알아봅니다.

[![애니메이션 GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

PowerShell을 선호합니까? Azure Resource Manager [서비스 템플릿](https://azure.microsoft.com/resources/templates/101-azure-search-create/)을 사용합니다. 시작하기 도움말은 [PowerShell로 Azure Search 관리](search-manage-powershell.md)를 참조하세요.

## <a name="subscribe-free-or-paid"></a>구독(무료 또는 유료)

[무료 Azure 계정을 열고](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) 무료 크레딧을 사용하여 유료 Azure 서비스를 사용해보세요. 크레딧이 소진되더라도 계정이 유지되므로 Websites와 같은 무료 Azure 서비스를 계속 사용하세요. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.

아니면 [MSDN 구독자 혜택을 활성화합니다](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다. 

## <a name="find-azure-search"></a>Azure Search 찾기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 위 모퉁이에서 더하기 기호("+ 리소스 만들기")를 클릭합니다.
3. 검색 창을 사용하여 "Azure Search"를 찾거나 **웹** > **Azure Search**를 통해 리소스로 이동합니다.

![Azure Search 리소스로 이동](./media/search-create-service-portal/find-search3.png "Azure Search에 대한 탐색 경로")

## <a name="name-the-service-and-url-endpoint"></a>서비스 및 URL 엔드포인트의 이름

서비스 이름은 API 호출이 발급되는 URL 엔드포인트의 일부입니다.`https://your-service-name.search.windows.net` **URL** 필드에 서비스 이름을 입력합니다.

예를 들어 엔드포인트가 `https://my-app-name-01.search.windows.net`이기를 원하는 경우 `my-app-name-01`을 입력합니다.

서비스 이름 요구 사항:

* search.windows.net 네임스페이스 내에서 고유해야 함
* 2 ~ 60자 길이
* 소문자, 숫자 또는 대시("-") 사용
* 첫 두 문자 또는 마지막 한 문자에는 대시("-")를 사용하지 않음
* 어디서든 연속 대시("--")를 사용할 수 없음

## <a name="select-a-subscription"></a>구독 선택

둘 이상의 구독이 있는 경우 데이터 또는 파일 저장소 서비스도 있는 구독을 선택합니다. Azure Search는 [*인덱서*](search-indexer-overview.md)를 통해 인덱싱하기 위해 Azure Table 및 Blob Storage, SQL Database, Azure Cosmos DB를 자동 검색할 수 있지만, 동일한 구독의 서비스에 대해서만 가능합니다.

## <a name="select-a-resource-group"></a>리소스 그룹 선택

리소스 그룹은 함께 사용된 Azure 서비스 및 리소스의 컬렉션입니다. 예를 들어 Azure Search를 사용하여 SQL Database를 인덱싱하는 경우 이들 두 서비스는 동일한 리소스 그룹의 일부여야 합니다.

단일 그룹에 리소스를 결합하지 않거나 기존 리소스 그룹을 관련이 없는 솔루션에서 사용되는 리소스로 채우는 경우 Azure Search 리소스에 대해서만 새 리소스 그룹을 만듭니다.

> [!TIP]
> 리소스 그룹을 삭제하면 그 안의 서비스도 삭제됩니다. 여러 서비스를 이용하는 프로토타입 프로젝트의 경우, 이들을 모두 동일한 리소스 그룹에 배치하면 프로젝트가 종료된 후 쉽게 정리할 수 있습니다.

## <a name="select-a-hosting-location"></a>호스팅 위치 선택

Azure 서비스인 Azure Search는 전 세계 데이터 센터에서 호스팅될 수 있습니다. 지역별로 [가격이 다를 수](https://azure.microsoft.com/pricing/details/search/) 있습니다.

Azure 서비스(Azure 스토리지, Azure Cosmos DB, Azure SQL Database)에 있는 콘텐츠를 인덱싱하는 경우 대역폭 요금을 피하기 위해 데이터와 같은 지역에서 Azure Search 서비스를 만듭니다. 서비스가 동일한 지역에 있는 경우 아웃바운드 데이터에 대한 요금이 없습니다.

## <a name="select-a-pricing-tier-sku"></a>가격 책정 계층(SKU) 선택

[Azure Search는 현재 여러 가지 가격 책정 계층에서 제공되며](https://azure.microsoft.com/pricing/details/search/) 무료, 기본 또는 표준 등이 있습니다. 각 계층에는 자체 [용량 및 제한](search-limits-quotas-capacity.md)이 있습니다. 지침은 [가격 책정 계층 또는 SKU 선택](search-sku-tier.md) 을 참조하세요.

표준은 프로덕션 워크로드에서 일반적으로 선택되지만 대부분의 고객은 무료 서비스를 시작합니다.

서비스를 만든 후에 가격 책정 계층을 변경할 수 없습니다. 나중에 상위 또는 하위 계층이 필요한 경우 서비스를 다시 만들어야 합니다.

## <a name="create-your-service"></a>서비스 만들기

로그인할 때마다 손쉽게 액세스할 수 있도록 서비스를 대시보드에 고정합니다.

![대시보드에 고정](./media/search-create-service-portal/new-service3.png "편리한 액세스를 위해 리소스를 대시보드에 고정")

## <a name="get-a-key-and-url-endpoint"></a>키 및 URL 엔드포인트 가져오기

거의 예외 없이, 새 서비스를 사용하려면 URL 엔드포인트와 권한 부여 api-key 키를 제공해야 합니다. 빠른 시작, [Azure Search REST API 살펴보기(Postman)](search-fiddler.md) 및 [.NET에서 Azure Search 사용 방법](search-howto-dotnet-sdk.md)과 같은 자습서, 샘플 및 사용자 지정 코드 모두에서 특정 리소스에서 실행할 엔드포인트와 키가 필요합니다.

1. 서비스 개요 페이지의 오른쪽에서 URL 엔드포인트를 찾아 복사합니다.

   ![URL 엔드포인트가 있는 서비스 개요 페이지](./media/search-create-service-portal/url-endpoint.png "URL 엔드포인트 및 기타 서비스 정보")

2. 왼쪽 탐색 창에서 **키**를 선택한 다음, 관리 키 중 하나(둘 다 동일)를 복사합니다. 관리 api-key는 서비스에서 개체를 생성, 업데이트 및 삭제하는 데 필요합니다.

   ![기본 및 보조 키가 표시된 키 페이지](./media/search-create-service-portal/admin-api-keys.png "권한 부여를 위한 관리 api-keys")

엔드포인트 및 키는 포털 기반 작업에 필요하지 않습니다. 포털은 관리자 권한으로 Azure Search 리소스에 이미 연결되어 있습니다. 포털 자습서는 [자습서: Azure Search에서 가져오기, 인덱싱 및 쿼리](search-get-started-portal.md)로 시작합니다.

## <a name="scale-your-service"></a>서비스 확장

서비스를 만드는 데 몇 분 정도 걸릴 수 있습니다(15분 이상 계층에 따라). 서비스가 프로비전되면 사용자의 요구에 맞게 확장할 수 있습니다. Azure Search 서비스에 대한 표준 계층을 선택했기 때문에 복제본과 파티션이라는 두 개의 차원에서 서비스를 확장할 수 있습니다. 기본 계층을 선택한 경우 복제본만 추가할 수 있습니다. 무료 서비스를 프로비전한 경우 확장이 불가능합니다.

***파티션***을 사용하면 서비스를 저장하고 더 많은 문서를 통해 검색할 수 있습니다.

***복제본***을 사용하면 서비스가 더 큰 부하의 검색 쿼리를 처리할 수 있습니다.

리소스를 추가하면 월별 청구서가 증가합니다. [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)는 리소스 추가의 청구 영향을 이해하는 데 도움이 됩니다. 부하에 따라 리소스를 조정할 수 있도록 해야 합니다. 예를 들어 전체 초기 인덱스를 만들려면 리소스를 늘린 다음, 나중에 증분 인덱싱에 더 적합한 수준으로 리소스를 줄일 수 있습니다.

> [!Important]
> 서비스는 [SLA 읽기 전용으로 2개의 복제본과 SLA 읽기/쓰기용으로 3개의 복제본](https://azure.microsoft.com/support/legal/sla/search/v1_0/)이 있어야 합니다.

1. Azure Portal의 검색 서비스 페이지로 이동합니다.
2. 왼쪽 탐색 창에서 **설정** > **규모**를 선택합니다.
3. 슬라이드 바를 사용하여 두 형식의 리소스 중 하나를 추가합니다.

![용량 추가](./media/search-create-service-portal/settings-scale.png "복제본과 파티션을 통해 용량 추가")

> [!Note]
> 각 계층에는 단일 서비스에서 허용하는 총 검색 단위 수(복제본 * 파티션 = 총 검색 단위)에 여러 [제한](search-limits-quotas-capacity.md)이 있습니다.

## <a name="when-to-add-a-second-service"></a>두 번째 서비스를 추가하는 경우

대부분의 고객은 [적절히 균형 잡힌 리소스](search-sku-tier.md)를 제공하는 계층에 프로비전되는 하나의 서비스만 사용합니다. 하나의 서비스는 [선택한 계층의 최대 제한](search-capacity-planning.md)에 따라 서로 분리된 여러 인덱스를 호스트할 수 있습니다. Azure Search에서 요청은 하나의 인덱스로만 리디렉션될 수 있으므로 같은 서비스의 다른 인덱스에서 실수로 또는 의도적으로 데이터가 검색될 가능성이 최소화됩니다.

대부분의 고객이 하나의 서비스만 사용하지만 운영과 관련해서 다음과 같은 사항이 요구될 경우 서비스 중복성이 필요할 수 있습니다.

* 재해 복구(데이터 센터 작동 중단). Azure Search는 가동 중단 시 즉각적인 장애 조치(failover)를 제공하지 않습니다. 권장 사항 및 지침에 대해서는 [서비스 관리](search-manage.md)를 참조하세요.
* 다중 테넌트 모델링을 조사한 결과, 추가 서비스가 최적의 디자인이라는 사실이 확인되었습니다. 자세한 내용은 [다중 테넌트 디자인](search-modeling-multitenant-saas-applications.md)을 참조하세요.
* 전역으로 배포된 애플리케이션의 경우 애플리케이션 해외 트래픽 대기 시간을 최소화하기 위해 여러 하위 지역에 Azure Search 인스턴스가 필요할 수 있습니다.

> [!NOTE]
> Azure Search에서는 인덱싱 및 쿼리 작업을 분리할 수 없습니다. 따라서 분리 된 작업에 대해 여러 서비스를 만들지 마세요. 인덱스는 항상 만들어진 서비스에서 쿼리됩니다(한 서비스에서 인덱스를 만든 후 다른 서비스로 복사할 수 없음).

고가용성을 위해 두 번째 서비스가 필요하지 않습니다. 동일한 서비스에 두 개 이상의 복제본을 사용하는 경우 쿼리에 대한 가용성을 높일 수 있습니다. 복제본 업데이트는 순차적입니다. 즉, 서비스 업데이트가 롤아웃될 때도 적어도 하나의 서비스는 작동됩니다. 가동 시간에 대한 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/search/v1_0/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Search 서비스를 프로비전하면 포털에서 첫 번째 인덱스를 계속 만들 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: 포털에서 데이터 가져오기, 인덱싱 및 쿼리 실행](search-get-started-portal.md)
