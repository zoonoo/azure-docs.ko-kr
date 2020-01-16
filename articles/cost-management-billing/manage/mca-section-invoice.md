---
title: 필요에 따라 청구서 구성-Azure
description: 청구서에 대 한 비용을 구성 하는 방법을 알아보세요.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991062"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>청구 계정을 사용자 지정 하 여 비용 구성

Microsoft 고객 계약의 청구 계정은 부서, 프로젝트 또는 개발 환경에 대 한 요구 사항에 따라 비용을 유연 하 게 구성할 수 있는 유연성을 제공 합니다. 

이 문서에서는 Azure Portal를 사용 하 여 비용을 구성 하는 방법을 설명 합니다. Microsoft 고객 계약에 대 한 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>청구 프로필 및 청구서 섹션으로 계정 구조화

Microsoft 고객 계약의 청구 계정에서 요금 청구 프로필 및 청구서 섹션을 사용 하 여 비용을 구성 합니다.

![Microsoft 고객 계약 청구 계층을 보여 주는 스크린샷](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>청구 프로필

청구 프로필은 청구서 및 지불 방법 및 청구 주소와 같은 관련 청구 정보를 나타냅니다. 월초에 사용자 계정에서 각 청구 프로필의 월별 청구서가 생성됩니다. 청구서에는 이전 달의 Azure 사용량 및 기타 구매 요금이 포함 됩니다.

청구 프로필은 Azure에 등록할 때 청구 계정과 함께 자동으로 생성 됩니다. 추가 청구 프로필을 만들어 여러 월별 청구서로 비용을 구성할 수 있습니다. 

> [!IMPORTANT]
>
> 추가 청구 프로필을 만들면 전체 비용에 영향을 줄 수 있습니다. 자세한 내용은 [새 청구 프로필을 추가할 때 고려해 야 할 사항](#things-to-consider-when-adding-new-billing-profiles)을 참조 하세요.

### <a name="invoice-section"></a>청구서 섹션

송장 섹션은 청구서의 비용 그룹화를 나타냅니다. 청구서 섹션은 계정의 각 청구 프로필에 대해 자동으로 생성 됩니다. 필요에 따라 비용을 구성 하기 위해 추가 섹션을 만들 수 있습니다. 각 청구서 섹션은 해당 월에 발생 한 요금으로 청구서에 표시 됩니다. 

아래 이미지는 두 개의 송장 섹션인 엔지니어링 및 마케팅을 포함 하는 송장을 보여줍니다. 각 섹션에 대 한 요약 및 세부 요금이 청구서에 표시 됩니다. 이미지에 표시 된 가격은 예를 들어 목적 으로만 사용 되며 Azure 서비스의 실제 가격은 표시 하지 않습니다. 

![섹션이 있는 송장을 보여 주는 이미지](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>일반적인 시나리오에 대 한 청구 계정 구조

이 섹션에서는 비용 및 해당 청구 계정 구조를 구성 하는 일반적인 시나리오에 대해 설명 합니다.

|시나리오  |구조  |
|---------|---------|
|Azure에 대 한 잭 등록에는 단일 월별 송장이 필요 합니다. | 청구 프로필 및 송장 섹션 이 구조는 Azure에 등록할 때 잭에 대해 자동으로 설정 되며 추가 단계가 필요 하지 않습니다. |

![간단한 청구 시나리오에 대 한 정보 그래픽](./media/mca-section-invoice/organize-billing-scenario1.png)

|시나리오  |구조  |
|---------|---------|
|Contoso는 단일 월별 송장을 필요로 하는 소규모 조직으로, 마케팅 및 재무 부서에 따른 비용을 그룹화 합니다.  | Contoso에 대 한 청구 프로필 및 마케팅 및 재무 부서에 대 한 송장 섹션 |

![간단한 청구 시나리오에 대 한 정보 그래픽](./media/mca-section-invoice/organize-billing-scenario2.png)

|시나리오  |구조  |
|---------|---------|
|Fabrikam은 엔지니어링 및 마케팅 부서에 대 한 별도의 송장을 필요로 하는 중간 규모의 조직입니다. 엔지니어링 부서에서는 환경 (생성 및 개발) 별로 비용을 그룹화 하려고 합니다.  | 마케팅 및 재무 부서에 대 한 청구 프로필 마케팅 부서의 경우 각각 프로덕션 및 개발 환경에 대 한 송장 섹션입니다. |

![간단한 청구 시나리오에 대 한 정보 그래픽](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>새 송장 섹션 만들기

청구서 섹션을 만들려면 **청구 프로필 소유자**이거나 **청구 프로필 기여자**여야 합니다. 자세한 내용은 [청구 프로필에 대 한 청구서 섹션 관리](understand-mca-roles.md#manage-invoice-sections-for-billing-profile)를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/mca-section-invoice/search-cmb.png)

3. 왼쪽 창에서 **청구 프로필** 을 선택 합니다. 목록에서 청구 프로필을 선택 합니다. 새 섹션이 선택한 청구 프로필의 청구서에 표시 됩니다. 

   [청구 프로필 목록을 표시 하는 ![스크린샷](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. 왼쪽 창에서 **송장 섹션** 을 선택 하 고 페이지 위쪽에서 **추가** 를 선택 합니다.

   [송장 섹션 추가를 보여 주는 ![스크린샷](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. 청구서 섹션의 이름을 입력 합니다. 

   [송장 섹션 만들기 페이지를 표시 하는 ![스크린샷](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. **만들기**를 선택합니다.

## <a name="create-a-new-billing-profile"></a>새 청구 프로필 만들기

청구 프로필을 만들려면 **청구 계정 소유자** 또는 **청구 계정 참가자**여야 합니다. 자세한 내용은 [청구 계정에 대 한 청구 프로필 관리](understand-mca-roles.md#manage-billing-profiles-for-billing-account)를 참조 하세요.

> [!IMPORTANT]
>
> 추가 청구 프로필을 만들면 전체 비용에 영향을 줄 수 있습니다. 자세한 내용은 [새 청구 프로필을 추가할 때 고려해 야 할 사항](#things-to-consider-when-adding-new-billing-profiles)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/mca-section-invoice/search-cmb.png)

3. 왼쪽 창에서 **청구 프로필** 을 선택 하 고 페이지 위쪽에서 **추가** 를 선택 합니다.

   [청구 프로필 목록을 표시 하는 ![스크린샷](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > 청구 프로필 페이지에 추가 단추가 표시 되지 않으면 해당 기능을 계정에 사용할 수 없습니다. 현재 Microsoft 담당자를 사용 하는 동안 설정 된 계정에만 사용할 수 있습니다.

4. 양식을 채우고 **만들기**를 클릭합니다.

   [청구 프로필 생성 페이지를 표시 하는 ![스크린샷](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |필드  |정의  |
    |---------|---------|
    |이름     | Azure Portal에서 청구 프로필을 쉽게 식별 하는 데 도움이 되는 표시 이름입니다.  |
    |PO 번호    | 선택적 구매 주문 번호입니다. PO 번호는 청구 프로필에 대해 생성 된 청구서에 표시 됩니다. |
    |청구 주소   | 청구 주소는 청구 프로필에 대해 생성 된 청구서에 표시 됩니다. |
    |전자 메일 송장   | 전자 메일 청구서 상자를 선택 하 여이 청구 프로필에 대 한 송장을 메일로 받습니다. 옵트인 (opt in) 하지 않은 경우 Azure Portal에서 송장을 보고 다운로드할 수 있습니다.|

5. **만들기**를 선택합니다.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>청구서 섹션 및 청구 프로필에 요금 연결

사용자의 요구에 따라 청구 계정을 사용자 지정한 후에는 구독 및 기타 제품을 원하는 청구서 섹션과 청구 프로필에 연결할 수 있습니다.

### <a name="link-a-new-subscription"></a>새 구독 연결

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **구독**을 검색합니다.

   [포털에서 구독에 대 한 검색을 보여 주는 ![스크린샷](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. 페이지 맨 위에서 **추가**를 선택합니다.

   ![구독 보기의 추가 단추를 보여 주는 스크린샷](./media/mca-section-invoice/subscription-add.png)

4. 여러 청구 계정에 액세스할 수 있는 경우 Microsoft 고객 계약 청구 계정을 선택 합니다.

   ![구독 보기의 추가 단추를 보여 주는 스크린샷](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. 구독 사용량에 대해 요금이 청구 될 청구 프로필을 선택 합니다. 이 구독에 대 한 Azure 사용량 및 기타 구매 요금은 선택한 청구 프로필의 청구서로 청구 됩니다.

6. 청구서 섹션을 선택 하 여 구독의 요금을 연결 합니다. 요금은 청구 프로필 청구서의이 섹션 아래에 표시 됩니다.

7. Azure 계획을 선택 하 고 구독에 대 한 친숙 한 이름을 입력 합니다. 

9. **만들기**를 클릭합니다.  

### <a name="link-existing-subscriptions-and-products"></a>기존 구독 및 제품 연결

기존 Azure 구독 또는 Azure Marketplace 및 앱 원본 리소스와 같은 기타 제품이 있는 경우 기존 청구서 섹션에서 다른 청구서 섹션으로 이동 하 여 비용을 재구성할 수 있습니다. 

> [!IMPORTANT]
>
> 구독과 기타 제품은 동일한 청구 프로필에 속한 청구서 섹션 간에만 이동할 수 있습니다. 다른 청구 프로필의 청구서 섹션에서 구독 및 제품을 이동 하는 것은 지원 되지 않습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![포털의 구독 검색을 보여 주는 스크린샷](./media/mca-section-invoice/search-cmb.png)

3. 구독을 새 송장 섹션에 연결 하려면 화면 왼쪽에서 **Azure 구독** 을 선택 합니다. Azure Marketplace 및 앱 원본 리소스와 같은 다른 제품의 경우 **되풀이 요금**을 선택 합니다.

   [청구서 섹션을 변경 하는 옵션을 보여 주는 ![스크린샷](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. 페이지에서 새 송장 섹션에 연결 하려는 구독 또는 제품에 대 한 줄임표 (...)를 클릭 합니다. **송장 변경 섹션**을 선택 합니다.

5. 드롭다운에서 새 송장 섹션을 선택 합니다. 드롭다운에는 기존 청구서 섹션과 동일한 청구 프로필에 연결 된 청구서 섹션만 표시 됩니다.

    [새 송장 섹션 선택을 보여 주는 ![스크린샷](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. **저장**을 선택합니다.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>새 청구 프로필을 추가할 때 고려해 야 할 사항

### <a name="azure-usage-charges-may-be-impacted"></a>Azure 사용 요금은 영향을 받을 수 있습니다.

Microsoft 고객 계약에 대 한 청구 계정에서 Azure 사용량은 각 청구 프로필에 대해 매월 집계 됩니다. 계층화 된 가격 책정을 사용 하는 Azure 리소스의 가격은 각 청구 프로필에 대 한 사용량에 따라 별도로 결정 됩니다. 사용량은 가격을 계산할 때 청구 프로필에서 집계 되지 않습니다. 이는 여러 청구 프로필을 사용 하는 계정의 Azure 사용량에 대 한 전반적인 비용에 영향을 줄 수 있습니다.

두 시나리오에서 비용의 차이가 어떻게 다른 지 살펴보겠습니다. 시나리오에서 사용 되는 가격은 예를 들어 목적 으로만 사용 되며 Azure 서비스의 실제 가격은 표시 하지 않습니다.

#### <a name="you-only-have-one-billing-profile"></a>청구 프로필은 하나만 있습니다.

Azure 블록 blob 저장소를 사용 하 고 있다고 가정 합니다 .이 저장소는 첫 50 tb (TB)의 경우 GB 당 00184이 고, 다음 450 tb (TB)의 경우 GB 당 00177입니다. 청구 프로필에 대해 청구 되는 구독에서 100 TB를 사용 했습니다. 비용은 다음과 같습니다.

|  계층 가격 책정 (USD) |수량 | Amount (USD)|
|---------|---------|---------|
|첫 50 t b/월에 대해 TB 당 1.84    | 50 TB        | 92.0   |
|다음 450 t b/월에 대해 TB 당 1.77    |  50 TB         | 88.5   |
|합계     |     100TB  | 180.5

이 시나리오에서 100 TB의 데이터 사용에 대 한 총 요금은 **180.5** 입니다.

#### <a name="you-have-multiple-billing-profiles"></a>여러 청구 프로필이 있습니다.

이제 다른 청구 프로필을 만들고 첫 번째 청구 프로필에 대해 청구 되는 구독을 통해 50 GB를 사용 하 고 두 번째 청구 프로필에 대해 청구 되는 구독을 통해 50 GB를 사용 했다고 가정해 보겠습니다. 여기에는 요금이 부과 됩니다.

`Charges for the first billing profile`

|  계층 가격 책정 (USD) |수량 | Amount (USD)|
|---------|---------|---------|
|첫 50 t b/월에 대해 TB 당 1.84    | 50 TB        | 92.0  |
|다음 450 t b/월에 대해 TB 당 1.77    |  0TB         | 0.0  |
|합계     |     50 TB  | 92.0 

`Charges for the second billing profile`

|  계층 가격 책정 (USD) |수량 | Amount (USD)|
|---------|---------|---------|
|첫 50 t b/월에 대해 TB 당 1.84    | 50 TB        | 92.0  |
|다음 450 t b/월에 대해 TB 당 1.77    |  0TB         | 0.0  |
|합계     |     50 TB  | 92.0 

이 시나리오에서 100 TB의 데이터 사용에 대 한 총 요금은 **184.0** (92.0 * 2)입니다.

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Azure 예약 혜택은 모든 구독에 적용 되지 않을 수 있습니다.

공유 범위를 포함 하는 Azure 예약은 단일 청구 프로필의 구독에 적용 되며, 청구 프로필 간에 공유 되지 않습니다. 

![다른 청구 계정 구조의 예약 응용 프로그램에 대 한 정보 그래픽](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

위의 이미지에서 Contoso에는 두 개의 구독이 있습니다. Azure 예약 혜택은 청구 계정 구성 방법에 따라 다르게 적용 됩니다. 왼쪽의 시나리오에서, 예약 혜택은 엔지니어링 청구 프로필에 청구 되는 두 구독 모두에 적용 됩니다. 오른쪽의 시나리오에서, 예약 혜택은 엔지니어링 청구 프로필에 청구 되는 유일한 구독 이므로 구독 1에만 적용 됩니다. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움이 필요하세요? 고객 지원

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대한 추가 Azure 구독 만들기](create-subscription.md)
- [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [다른 청구 계정의 사용자에서 Azure 구독의 청구 소유권 얻기](mca-request-billing-ownership.md)
