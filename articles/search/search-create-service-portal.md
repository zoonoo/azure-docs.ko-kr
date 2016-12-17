---
title: "Azure Portal을 사용하여 Azure Search 서비스 만들기 | Microsoft Docs"
description: "Azure 포털을 사용하여 Azure 검색 서비스를 프로비전하는 방법을 알아봅니다."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5975bd5b2a2e7fe8799155ab47d96c3ecd0093ee


---
# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure 검색 서비스 만들기
이 가이드에서는 [Azure 포털](https://portal.azure.com/)을 사용하여 Azure 검색 서비스를 만드는(또는 프로비전하는) 프로세스를 안내합니다.

이 가이드에서는 사용자가 이미 Azure 구독을 소유하고 있으며 Azure 포털에 로그인할 수 있다고 가정합니다.

## <a name="find-azure-search-in-the-azure-portal"></a>Azure 포털에서 Azure 검색 찾기
1. [Azure 포털](https://portal.azure.com/) 로 이동하고 로그인합니다.
2. 왼쪽 위 모퉁이에서 더하기 기호("+")를 클릭합니다.
3. **데이터 + 저장소**를 선택합니다.
4. **Azure 검색**을 선택합니다.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>서비스 이름 및 서비스에 대한 URL 끝점을 선택합니다.
1. 서비스 이름은 API 호출에서 검색 서비스를 관리하고 사용하는 Azure 검색 서비스의 끝점 URL의 일부가 됩니다.
2. **URL** 필드에 서비스 이름을 입력합니다. 서비스 이름:
   * 소문자, 숫자 또는 대시("-")만 포함해야 합니다.
   * 대시("-")를 처음 두 문자 또는 마지막 단일 문자로 사용할 수 없습니다.
   * 연속 대시("--")를 포함할 수 없습니다.
   * 길이는 2-60자로 제한됩니다.

## <a name="select-a-subscription-where-you-will-keep-your-service"></a>서비스를 유지할 구독을 선택합니다
둘 이상의 구독이 있는 경우 이 Azure 검색 서비스를 포함할 구독을 선택할 수 있습니다.

## <a name="select-a-resource-group-for-your-service"></a>서비스에 대한 리소스 그룹을 선택합니다.
새 리소스 그룹을 만들거나 기존 항목을 선택합니다. 리소스 그룹은 함께 사용되는 Azure 서비스 및 리소스의 컬렉션입니다. 예를 들어 Azure 검색을 사용하여 SQL 데이터베이스를 인덱싱하는 경우 이러한 서비스는 모두 동일한 리소스 그룹의 일부여야 합니다.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>서비스를 호스트할 위치를 선택합니다.
Azure 서비스인 Azure 검색은 전 세계 데이터 센터에서 호스팅되는 데 사용할 수 있습니다. 지역별로 [가격이 다를 수](https://azure.microsoft.com/pricing/details/search/) 있습니다.

## <a name="select-your-pricing-tier"></a>가격 책정 계층 선택
[Azure 검색은 무료, 기본 또는 표준 등 여러 가지 가격 책정 계층에서 현재 제공됩니다](https://azure.microsoft.com/pricing/details/search/). 각 계층에는 자체 [용량 및 제한](search-limits-quotas-capacity.md)이 있습니다. 지침은 [가격 책정 계층 또는 SKU 선택](search-sku-tier.md) 을 참조하세요.

이 경우에 서비스에 대한 표준 계층을 선택했습니다.

## <a name="select-the-create-button-to-provision-your-service"></a>"만들기" 단추를 선택하여 서비스를 프로비전합니다.
![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>서비스 확장
서비스가 프로비전되면 사용자의 요구에 맞게 확장할 수 있습니다. Azure 검색 서비스에 대한 표준 계층을 선택한 경우 복제본과 파티션이라는 두 개의 차원에서 서비스를 확장할 수 있습니다. 기본 계층을 선택한 경우 복제본을 추가할 수 있습니다.

***파티션***을 사용하면 서비스를 저장하고 더 많은 문서를 통해 검색할 수 있습니다.

***복제본***을 사용하면 서비스가 검색 쿼리의 높은 부하를 처리할 수 있습니다. [서비스에는 읽기 전용 SLA를 달성할 2개의 복제본 및 읽기/쓰기 SLA를 달성할 3개의 복제본이 필요합니다](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Azure 포털에서 Azure 검색 서비스의 관리 블레이드로 이동합니다.
2. **설정** 블레이드에서 **규모**를 선택합니다.
3. 복제본 또는 파티션을 추가하여 서비스를 확장할 수 있습니다.
   * 36개의 검색 단위를 넘도록 서비스를 확장할 수 없습니다. 총 검색 단위 수는 복제본과 파티션을 합한 제품(복제본 * 파티션 = 총 검색 단위)입니다.
   * 기본 계층을 선택한 경우 3개 복제본으로 확장할 수 있습니다. 기본 서비스는 단일 파티션에 바인딩됩니다.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>다음
Azure 검색 서비스를 프로비전한 후에 [Azure 검색 인덱스를 정의](search-what-is-an-index.md) 할 준비가 되었으므로 데이터를 업로드하고 검색할 수 있습니다.

간략한 자습서는 [포털에서 Azure 검색 시작](search-get-started-portal.md) 을 참조하세요.




<!--HONumber=Nov16_HO3-->


