---
title: Azure Marketplace에서 기존 VM 제품 업데이트
description: Azure Marketplace에서 기존 VM 제안을 업데이트하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: 4a75d706d55512201786b2b74376047ff75380a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938126"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Azure Marketplace에서 기존 VM 제안 업데이트

이 문서에서는 [Cloud 파트너 포털](https://cloudpartner.azure.com/)에서 VM(가상 머신) 제안을 업데이트한 다음, 해당 제안을 다시 게시하는 다양한 측면을 안내합니다. 

제안을 업데이트하게 되는 일반적인 이유는 다음과 같습니다.

-  기존 SKU에 새 VM 이미지 버전 추가
-  SKU가 제공되는 지역 변경
-  새 SKU 추가
-  제안 또는 개별 SKU에 대한 마켓플레이스 메타데이터 업데이트
-  종량제 제안 가격 업데이트

이러한 수정 작업을 지원하기 위해 포털에서 **비교** 및 **기록** 기능을 제공합니다.  

>[!Note]
>클라우드 솔루션 공급자 (CSP) 파트너 채널 옵트인를 출시 되었습니다.  참조 하세요 [클라우드 솔루션 공급자](../../cloud-solution-providers.md) Microsoft CSP를 통해 제품을 마케팅 하는 방법은 채널 파트너입니다.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>VM 제안 또는 SKU에 허용되지 않는 변경

Azure Marketplace에서 제안이 라이브 상태가 되면 수정할 수 없는 몇 가지 VM 제안 또는 SKU 특성이 있습니다. 주로 다음과 같습니다.

-  제안의 **제안 ID** 및 **게시자 ID**
-  기존 SKU의 **SKU ID**
-  기존 SKU의 데이터 디스크 수
-  기존 SKU에 대한 청구/라이선스 모델 변경
-  게시된 SKU에 대한 가격 인상


## <a name="common-update-operations"></a>일반적인 업데이트 작업

VM 제안에서 변경할 수 있는 특성은 다양하지만 일반적인 작업은 다음과 같습니다.

### <a name="update-the-vm-image-version-for-a-sku"></a>SKU에 대한 VM 이미지 버전 업데이트

일반적으로 VM 이미지는 보안 패치, 추가 기능 등으로 정기적으로 업데이트됩니다.  이러한 시나리오에서는 다음 단계를 사용하여 SKU에서 참조하는 VM 이미지를 업데이트하려고 합니다.

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제안** 아래에서 업데이트할 제안을 찾습니다.

3.  **SKU** 탭에서 업데이트할 VM 이미지와 연결된 SKU를 클릭합니다.

4.  **디스크 버전** 아래에서 **+새 디스크 버전**을 클릭하여 새 VM 이미지를 추가합니다.

5.  새 VM 이미지 **디스크 버전**을 제공합니다. 디스크 버전은 [의미 체계 버전](https://semver.org/) 형식을 따라야 합니다. 버전은 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다. 제공하는 새 버전이 모든 이전 버전보다 높은지 확인합니다. 그렇지 않으면 새 버전이 다시 게시된 후에 포털 또는 Azure Marketplace에 표시되지 않습니다.

6.  **OS VHD URL**에서 운영 체제 VHD에 대해 만든 [SAS(공유 액세스 서명) URI](./cpp-get-sas-uri.md)를 입력합니다. 

    > [!WARNING] 
    > 데이터 디스크 수는 SKU의 다른 버전 간에 변경할 수 없습니다. 이전 버전에 데이터 디스크가 구성된 경우 이 새 버전의 데이터 디스크 수는 같아야 합니다.

7.  **게시**를 클릭하여 새 VM 버전을 Azure Marketplace에 게시하는 워크플로를 시작합니다.


### <a name="change-region-availability-of-a-sku"></a>SKU의 지역 가용성 변경

시간이 지남에 따라 더 많은 지역에서 제품/SKU를 사용할 수 있도록 하고 싶을 수 있습니다.  또는 특정 지역에서 제품/SKU 지원을 중지하고 싶을 수도 있습니다.
가용성을 수정하려면 다음 단계를 사용합니다.

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제안** 아래에서 업데이트하려는 제안을 찾습니다.

3.  **SKU** 탭에서 가용성을 수정하려는 SKU를 클릭합니다.

4.  **국가/지역 가용성** 필드 아래에서 **국가 선택** 단추를 클릭합니다.

5.  지역 가용성 팝업에서 이 SKU에 대한 지역을 추가하거나 제거합니다.

6.  **게시**를 클릭하여 SKU 지역 가용성을 업데이트하는 게시 워크플로를 시작합니다.

새 지역에서 SKU를 사용할 수 있게 되면 **가격 데이터 내보내기** 기능을 통해 특정 지역의 가격 책정을 지정할 수 있습니다. 이전에 한 번 사용할 수 있었던 지역을 다시 추가하는 경우 가격 책정 변경이 허용되지 않으므로 가격을 업데이트할 수 없습니다.


### <a name="add-a-new-sku"></a>새 SKU 추가

다음 단계를 사용하여 기존 제안에 새 SKU를 사용할 수 있게 합니다. 

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제안** 아래에서 업데이트하려는 제안을 찾습니다.

3.  **SKU** 탭 아래에서 **새 SKU 추가**를 클릭하고, 팝업에서 **SKU ID**를 제공합니다.

4.  [Azure Marketplace에 가상 머신 게시](./cpp-publish-offer.md) 문서에서 설명한 대로 VM을 다시 게시합니다.

5.  **게시**를 클릭하여 새 SKU를 게시하는 워크플로를 시작합니다.


### <a name="update-offer-marketplace-metadata"></a>제안 마켓플레이스 메타데이터 업데이트

다음 단계를 사용하여 제안과 연결된 마켓플레이스 메타데이터(회사 이름, 로고 등)를 업데이트합니다. 

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제안** 아래에서 업데이트하려는 제안을 찾습니다.

3.  **Marketplace** 탭으로 이동한 다음, 문서 [Azure Marketplace에 가상 머신 게시](./cpp-publish-offer.md)의 지침에 따라 메타데이터를 변경합니다.

4.  **게시**를 클릭하여 변경 내용을 게시하는 워크플로를 시작합니다.


### <a name="update-pricing-on-published-offers"></a>게시된 제안 가격 업데이트

종량제 제안이 게시되면 SKU 가격을 직접 높일 수 없습니다.  (그러나 동일한 제안 아래에서 새 SKU를 만들고, 이전 SKU를 삭제한 다음, 새 고객에게 제안을 다시 게시할 수 있습니다.)  반면에 다음 단계를 사용하여 게시된 제안의 가격을 낮출 수는 있습니다.

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제안** 아래에서 업데이트할 제안을 찾습니다.

3.  가격을 낮추려는 SKU를 클릭합니다.

4.  1x1 GUI에서 가격 책정을 설정한 경우 UI에서 직접 가격을 변경할 수 있습니다. 스프레드시트 가져오기/내보내기를 통해 가격 책정을 설정하는 경우 가져오기/내보내기 기능을 통해서만 가격을 낮출 수 있습니다.

3.  **저장**을 클릭합니다.

4.  **게시**를 클릭하여 변경 내용을 게시하는 워크플로를 시작합니다.

새로 할인된 가격은 웹 사이트에서 라이브 상태가 되면 새 고객에게 표시됩니다.  이러한 새 가격이 고객에게 영향을 주는 방식은 다음과 같습니다.

- 이러한 새 요율이 새 고객에게 부과됩니다. 
- 기존 고객의 경우 가격 인하는 발효된 청구 주기 시작 시점까지 소급하여 적용됩니다.
가격 인하가 발생한 주기에 대한 요금이 이미 청구되었으면 다음 청구 주기에 인하된 가격에 대한 환불을 받게 됩니다.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>간소화된 통화 가격 책정

2018년 9월 1일부터 **간소화된 통화 가격 책정**이라는 새 섹션이 포털에 추가됩니다. Microsoft는 전 세계 고객으로부터의 더욱 예측 가능한 가격 책정 및 요금 징수 방식을 사용하여 Azure Marketplace 비즈니스를 간소화하고 있습니다. 이 간소화에는 고객에게 발급되는 송장의 통화 단위 수를 줄이는 작업이 포함됩니다.

새로운 섹션에서 가격은 이러한 새 통화로 책정됩니다. 모든 고객이 이러한 새 결제 통화로 마이그레이션되면 원래 가격 책정 섹션은 더 이상 사용되지 않고 간소화된 통화 가격 책정 섹션만 유지됩니다.

결제 통화가 변경되는 지역에 대한 새로운 가격은 2018년 11월 1일까지 설정해야 합니다. 결제 통화가 변경되지 않는 지역에 대한 가격은 인상할 수 없습니다.

> [!NOTE] 
> API를 사용하여 제안을 게시하는 경우 제안 JSON에 새 섹션이 표시될 수 있습니다. 이 섹션은 제안 유형에 따라 `virtualMachinePricingV2` 또는 `monthlyPricingV2`라는 주석으로 지정됩니다. 

이 변경에 대한 질문이 있는 경우 [Azure Marketplace 지원](../../support-azure-marketplace.md)에 문의하세요.


## <a name="compare-feature"></a>기능 비교

이미 게시된 제안을 변경하는 경우 **비교** 기능을 활용하여 변경 내용을 감사할 수 있습니다. 이 기능을 사용하려면 다음을 수행합니다.

1.  편집 프로세스 중에 언제든지 제안에 대한 **비교** 단추를 클릭합니다.

    ![기능 비교 단추](./media/publishvm_037.png)


2.  마케팅 자산과 메타데이터의 버전이 나란히 표시됩니다.


## <a name="history-of-publishing-actions"></a>게시 작업 기록

이전의 모든 게시 작업을 보려면 Cloud 파트너 포털의 왼쪽 탐색 메뉴 모음에서 **기록** 항목을 클릭합니다. 여기서는 Azure Marketplace 제안의 수명 동안 수행된 타임스탬프 지정 작업을 확인할 수 있습니다.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

