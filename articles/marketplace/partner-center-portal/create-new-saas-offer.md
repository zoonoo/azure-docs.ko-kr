---
title: Microsoft 상업용 Marketplace를 위한 새 SaaS 제품 만들기
description: Microsoft 파트너 센터의 Microsoft 상업용 Marketplace 프로그램을 사용하여 Microsoft AppSource, Azure Marketplace 또는 CSP(클라우드 솔루션 공급자) 프로그램을 통해 나열 또는 판매할 새 SaaS(Software as a Service) 제품을 만드는 방법입니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3393fb6e33cbf80db68c49ac31edb54de35bae64
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980599"
---
# <a name="create-a-new-saas-offer-in-the-commercial-marketplace"></a>상업용 Marketplace에서 새 SaaS 제품 만들기

상업용 Marketplace에서 SaaS(Software as a Service) 제품을 만들기 시작하려면 먼저 [파트너 센터 계정을 만든](./create-account.md) 다음 **개요** 탭이 선택된 상태에서 [상업용 Marketplace 대시보드](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)를 열어야 합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요**를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Software as a Service**를 선택합니다.

   ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-saas.png)

> [!NOTE]
> 제품이 게시된 후 파트너 센터에서 해당 제품에 대해 편집한 내용이 상점에 표시되려면 제품을 다시 게시해야 합니다. 변경을 수행한 후에는 항상 다시 게시해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Marketplace 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 여기에 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
- **만들기**를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

**만들기**를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-overview"></a>제품 개요

**게시 상태**에는 이 제품을 게시하는 데 필요한 단계가 시각적으로 표시되고 각 단계를 완료하는 데 걸리는 시간이 표시됩니다. 완료되지 않은 게시 단계 아이콘은 회색으로 표시됩니다.

**제품 개요** 메뉴에는 이 제품에 대한 작업을 수행하기 위한 링크 목록이 포함되어 있습니다. 이 작업 목록은 제품에 대해 선택한 항목에 따라 변경됩니다.  

- 제품이 초안 상태인 경우 - 초안 삭제
- 제품이 라이브 상태인 경우 - 제품 판매 중지
- 제품이 미리 보기 상태인 경우 - 라이브 상태로 전환
- 게시자 로그 아웃을 완료 하지 않은 경우 게시 취소

## <a name="offer-setup"></a>제품 설정

이 페이지에서는 다음 정보를 요청합니다.

- **Microsoft를 통해 판매하시겠습니까?** (예/아니요)
    - **예**, Microsoft를 통해 판매하고 Microsoft가 대신 트랜잭션을 호스트하도록 하겠습니다.
    - **아니요**, 마켓플레이스를 통해서 제품을 나열만 하고 트랜잭션을 독립적으로 처리하겠습니다.

### <a name="sell-through-microsoft"></a>Microsoft를 통해 판매

Microsoft를 통해 판매하면 더 효율적으로 고객을 검색 및 획득하고, Microsoft가 대신 마켓플레이스 트랜잭션을 호스트하도록 하고, Microsoft의 전 세계적으로 사용 가능한 상거래 기능을 활용할 수 있습니다.

#### <a name="saas-offer-requirements"></a>SaaS 제품 요구 사항

파트너 센터의 상업용 Marketplace에서 SaaS(Software as a Service) 제품을 나열하려면 다음 조건을 충족해야 합니다.

- 제품이 ID 관리 및 인증을 위해 [Azure AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)를 사용해야 합니다.
- 제품이 Azure Marketplace와 통합되도록 [SaaS 처리 API](pc-saas-fulfillment-api-v2.md)를 사용해야 합니다.

#### <a name="saas-pricing-and-billing-options"></a>SaaS 가격 책정 및 청구 옵션

SaaS 솔루션을 게시자의 Azure 구독에서 실행하는 경우 고객이 지불하는 라이선스 요금은 소프트웨어를 배포하는 인프라 비용을 포함합니다. Azure 인프라 사용량은 관리되며 파트너에게 직접 청구됩니다. 실제 인프라 사용 요금은 고객에게 표시되지 않습니다. 게시자는 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격 책정에 포함해야 합니다.

SaaS는 요금 청구 서비스를 사용하여 정액 요금, 사용자당 요금 또는 사용량 요금을 기준으로 월별 또는 연간 청구를 지원합니다. Microsoft의 상업용 Marketplace는 에이전시 모델을 기반으로 작동합니다. 즉, 게시자가 가격을 설정하고, Microsoft가 고객에게 요금을 청구하고, Microsoft가 에이전시 수수료를 공제 후 게시자에게 수익을 지급합니다.

다음은 에이전시 모델을 설명 하는 비용 및 지급의 샘플 분석입니다. 나열 된 가격은 예를 들기 위한 것 이며 실제 비용을 반영 하기 위한 것이 아닙니다.

|**라이선스 비용**|**매월 $100**|
|:---|:---|
|Azure 사용량 비용(D1/1개 코어)|고객이 아닌 게시자에게 직접 청구됩니다.|
|Microsoft에서 고객에게 청구하는 요금|월간 $100.00(게시자는 발생 또는 이전된 인프라 비용을 라이선스 요금으로 계산해야 함)|

|**Microsoft에 청구되는 요금**|**매월 $100**|
|:---|:---|
|Microsoft는 라이선스 비용의 80%를 지불합니다. <br>*Microsoft는 적격 SaaS 앱에 대해 90%의 라이선스 비용을 지급합니다.|매월 $80.00 <br>매월 *$* 90.00*|

- 이 예제에서 Microsoft는 소프트웨어 라이선스에 대해 고객에게 $100.00를 청구하고 게시자에게 $80.00를 지급합니다.

> [!NOTE]
> **Marketplace 서비스 요금 절감** – 상업적 Marketplace에 게시 한 특정 SaaS 제품의 경우 Microsoft는 Microsoft 게시자 계약에 설명 된 대로 20%에서 Marketplace 서비스 요금을 10%까지 줄일 수 있습니다. 제품을 적격 하 게 하려면 Microsoft에서 제공 하는 IP 공동 판매 준비 또는 IP 공동 판매 우선 순위를 지정 해야 합니다. 해당 월의 Marketplace 서비스 요금을 절감 하려면 각 월이 끝날 때까지 영업일 (5) 이상의 영업일을 충족 해야 합니다. 낮은 Marketplace 서비스 요금은 Vm, 관리 되는 앱 또는 상업적 Marketplace를 통해 제공 되는 다른 제품에는 적용 되지 않습니다.

### <a name="list-through-microsoft"></a>Microsoft를 통해 나열

마켓플레이스 목록을 만들어 Microsoft로 비즈니스를 홍보하세요. Microsoft가 아닌 제품을 나열하도록 선택하는 것은 Microsoft가 소프트웨어 라이선스 트랜잭션에 직접 참여하지 않는다는 것을 의미합니다. 관련된 트랜잭션 수수료는 없으며 게시자는 고객으로부터 수금한 소프트웨어 라이선스 수수료를 100% 수취합니다. 단, 게시자는 주문, 이행, 계량, 청구, 송장, 지불 및 수금을 포함하되 이에 제한되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원할 책임이 있습니다.

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>지금 받기(무료)

[Azure Active Directory (AZURE AD)를 사용 하 여 한 번의 클릭으로 인증](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)을 통해 평가판을 얻을 수 있는 올바른 주소 ( *http* 또는 *https*로 시작)를 제공 하 여 고객에 게 제품을 무료로 제공 합니다. `https://contoso.com/saas-app`)을 입력합니다.

#### <a name="free-trial-listing"></a>평가판(목록)

올바른 주소 ( *http* 또는 *https*로 시작)를 제공 하 여 평가판에 대 한 링크를 고객에 게 나열 합니다. 여기에서 [Azure Active Directory (Azure AD)를 사용 하 여 한 번의 클릭으로 인증](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)을 통해 평가판을 얻을 수 있습니다. 예: `https://contoso.com/trial/saas-app`. 무료 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다.

> [!NOTE]
> 평가판 링크를 통해 애플리케이션에서 수신하는 토큰은 앱에서 자동으로 계정을 만드는 Azure AD를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용하는 인증에 MSA(Microsoft 계정)가 지원되지 않습니다.

#### <a name="contact-me"></a>연락처

CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

#### <a name="example-marketplace-offer-listing"></a>마켓플레이스 제품 목록 예

<!-- ![Example marketplace offer listing with notes](./media/marketplace-offer.svg) -->

Microsoft AppSource에서 제공 정보를 표시 하는 방법의 예는 다음과 같습니다.

:::image type="content" source="media/example-appsource-saas.png" alt-text="이 제품이 Microsoft AppSource 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 크게 로고
2. 범주
3. 산업
4. 지원 주소 (링크)
5. 사용 약관
6. 개인 정보 보호 정책
7. Offer name
8. 요약
9. 설명
10. 스크린샷/비디오
11. 문서

<br>다음은 Azure Portal에서 제공 정보를 표시 하는 방법의 예입니다.

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="이 제품이 Azure Portal 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 제목
2. 설명
3. 유용한 링크
4. 스크린샷

## <a name="enable-a-test-drive"></a>시험 사용을 사용하도록 설정

시험 사용은 "구입 전 체험" 옵션을 제공하여 잠재적인 고객에게 제품을 선보임으로써 구매 전환율을 높이고 적절한 잠재 고객을 창출할 수 있는 유용한 방법입니다. [시험 사용에 대해 자세히 알아보세요](../what-is-test-drive.md).

일정 기간 동안 시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

자세한 내용은 [상업용 Marketplace에서 제품 시험 사용](test-drive.md)을 참조하세요.

### <a name="test-drive-resources"></a>시험 사용 리소스

- [시험 사용이란?](../what-is-test-drive.md)
- [기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF. 팝업 차단이 해제되어 있는지 확인)

### <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>추가 잠재 고객 관리 리소스
- [리드 관리 faq](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [일반적인 리드 구성 오류](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [잠재 고객 관리 개요 원페이저](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 마켓플레이스에서 제품을 그룹화하는 데 사용되는 범주와 제품 및 앱 버전을 지원하는 법적 계약을 질문합니다.

### <a name="category"></a>Category

제품은 제품 및 범주 선택과 연관 된 트랜잭션 기능에 따라 AppSource 또는 Azure Marketplace에 게시 됩니다. 자세한 내용은 [Microsoft AppSource 비교 및 Azure Marketplace](../comparing-appsource-azure-marketplace.md) 를 참조 하세요. 제품 및 원하는 대상에 가장 적합 한 범주 및 하위 범주를 선택 합니다. 선택:

- 기본 및 보조 범주 (선택 사항)를 포함 하 여 적어도 하나 이상의 범주입니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 제안에 적용 되는 하위 범주가 없으면 **해당 없음**을 선택 합니다.

[제안 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 각 Storefront에 적용 되는 범주 및 하위 범주에 대 한 전체 목록을 확인 하세요.

### <a name="industries"></a>산업

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

산업 선택은 AppSource에 게시 된 제품에만 적용 됩니다.

### <a name="app-version"></a>앱 버전

이 필드는 선택 사항이며 AppSource 마켓플레이스에서 제품의 버전 번호를 식별하는 데 사용됩니다.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업용 Marketplace 표준 계약

Microsoft에서는 표준 계약 템플릿을 제공합니다.

- **Microsoft 상업용 Marketplace 표준 계약을 사용합니까?**

고객에게는 구매 프로세스를 간소화하고 소프트웨어 공급업체에게는 법적 복잡성을 줄이기 위해 Microsoft는 마켓플레이스에서 트랜잭션을 용이하게 하는 Microsoft 상업용 Marketplace 표준 계약을 제공합니다. 상업용 Marketplace 게시자는 사용자 지정 약관을 만드는 대신 표준 계약에 따라 소프트웨어를 제공하도록 선택할 수 있으며, 고객은 이를 한 번만 심사하고 수락하면 됩니다. 표준 계약은 https://go.microsoft.com/fwlink/?linkid=2041178 에서 찾을 수 있습니다.

"상업용 Marketplace 표준 계약 사용" 확인란을 선택하여 사용자 지정 약관을 제공하는 대신 표준 계약을 사용하도록 선택할 수 있습니다.

![표준 계약 사용 확인란](./media/use-standard-contract.png)

> [!NOTE]
> Microsoft 상업용 Marketplace 표준 계약을 사용하여 제품을 게시한 후에는 사용자 지정 약관을 사용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약 **또는** 게시자 고유 사용 약관에 따라 솔루션을 제공합니다. 표준 계약의 조건을 수정하려면 표준 계약 수정을 통해 이 작업을 수행할 수 있습니다.

#### <a name="standard-contract-amendments"></a>표준 계약 수정

게시자는 표준 계약 수정을 통해 간소화를 위해 표준 계약 조건을 선택하면서 제품 또는 비즈니스에 대한 조건을 사용자 지정할 수 있습니다. 고객은 Microsoft 표준 계약을 이미 검토하고 수락한 경우 계약 수정 내용만 검토하면 됩니다.

상업용 마켓플레이스 게시자가 사용할 수 있는 수정은 두 가지 종류가 있습니다.

- 범용 수정: 이러한 수정은 모든 고객에 대한 표준 계약에 전체적으로 적용됩니다. 범용 수정은 구매 흐름에서 제품의 모든 고객에게 표시됩니다. 고객은 제품을 사용하기 전에 표준 계약 및 수정 내용에 동의해야 합니다.
- 사용자 지정 수정: 이러한 수정은 Azure 테넌트 ID를 통해 특정 고객만을 대상으로 하는 표준 계약 특별 수정입니다. 게시자는 대상으로 지정할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 제품의 구매 흐름에 사용자 지정 수정 조건이 표시됩니다.  고객은 제품을 사용하기 전에 표준 계약 및 수정 내용에 동의해야 합니다.

>[!NOTE]
> 이러한 두 가지 유형의 수정은 누적됩니다. 사용자 지정 수정을 대상으로 하는 고객은 구매 중 표준 계약에 대한 범용 수정도 적용받게 됩니다.

**Microsoft의 상업용 Marketplace 표준 계약에 대한 범용 수정 조건** – 이 상자에 범용 수정 조건을 입력합니다. 제품별로 단일 범용 수정 계약을 제공할 수 있습니다. 이 상자에서는 문자를 무제한으로 입력할 수 있습니다. 이러한 사용 약관은 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에게 표시됩니다.

**Microsoft의 상업용 Marketplace 표준 계약에 대한 사용자 지정 수정 조건** – **사용자 지정 수정 조건 추가**를 선택하여 시작합니다. 제품당 최대 10개의 사용자 지정 수정 조건을 제공할 수 있습니다.

- **사용자 지정 수정 조건** – 사용자 지정 수정 조건 상자에 사용자 지정 수정 조건을 입력합니다. 이 상자에는 문자를 개수 제한 없이 입력할 수 있습니다. 이러한 사용자 지정 약관에 지정하는 테넌트 ID의 고객에게만 Azure Portal의 제품 구매 흐름에 사용자 지정 수정 조건이 표시됩니다.  
- **테넌트 ID**(필수) – 각 사용자 지정 수정마다 최대 20개의 테넌트 ID를 대상으로 지정할 수 있습니다. 사용자 지정 수정을 추가하는 경우 하나 이상의 테넌트 ID를 제공해야 합니다. 테넌트 ID는 Azure에서 고객을 식별합니다. 게시자는 고객에게 이 ID를 요청할 수 있으며 고객은 portal.azure.com > Azure Active Directory > 속성으로 이동하여 ID를 확인할 수 있습니다. 디렉터리 ID 값은 테넌트 ID입니다(예: 50c464d3-4930-494c-963c-1e951d15360e). 사용자의 도메인 이름 URL을 사용 하 여 고객의 테 넌 트 ID를 조회할 수도 있습니다. [Microsoft Azure 및 Office 365 테 넌 트 id는 무엇 인가요?](https://www.whatismytenantid.com)
- **설명**(선택 사항) – 필요에 따라 수정 조건에서 대상으로 지정하는 고객을 식별하는 데 도움이 되도록 테넌트 ID에 대한 친숙한 설명을 제공합니다.

#### <a name="terms-and-conditions"></a>사용 약관

사용자 지정 사용 약관을 제공하려는 경우 사용 약관 필드에 입력할 수 있습니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다. 사용 약관에 더 긴 설명이 필요한 경우 이 필드에 사용 약관을 찾을 수 있는 단일 URL 링크를 입력합니다. 이는 고객에게 활성 링크로 표시됩니다.

고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

이 페이지에는 제품을 사용할 수 있는 언어 및 시장이 표시됩니다. 현재는 영어(미국)만 사용할 수 있습니다. 또한 이 페이지에는 언어별 목록의 상태와 목록이 추가된 날짜/시간이 표시됩니다. 각 언어/시장에 대해 마켓플레이스 세부 정보(제품 이름, 설명, 검색어 등)를 정의해야 합니다.

> [!NOTE]
> 제품 설명이 "[영어가 아닌 언어]에서만 사용할 수 있습니다."로 시작하는 경우 제품 목록 콘텐츠(예: 제품 설명, 문서, 스크린샷, 사용 약관, 개인정보처리방침)는 영어로 작성되지 않아도 됩니다. 또한 제품 목록 콘텐츠에 사용되는 것 이외의 언어로 콘텐츠를 제공하는 유용한 링크 URL을 제공할 수 있습니다.

### <a name="offer-listings"></a>제품 목록

제품 및 마케팅 자산에 대한 설명을 포함하여 마켓플레이스에 표시되는 세부 정보를 제공합니다.

- **이름**(필수) – 여기에 정의된 이름은 선택한 마켓플레이스에서 제품 목록의 제목으로 표시됩니다. 이 이름은 이전 **새 제품** 항목을 기준으로 미리 채워집니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **요약**(필수) – 마켓플레이스 목록 검색 결과에 사용할 간단한 제품 설명을 제공합니다. 이 필드에는 최대 100자의 텍스트를 입력할 수 있습니다.
- **설명**(필수) – 마켓플레이스 목록에 표시되는 제품 설명을 제공합니다. 가치 제안, 주요 이점, 범주 또는 업계 연결, 앱 내 구매 기회, 필요한 정보 공개, 자세한 내용을 볼 수 있는 링크를 포함하는 것이 좋습니다. 이 필드에 태그를 포함하여 최대 3,000자의 텍스트를 입력할 수 있습니다. 추가 팁은 [유용한 앱 설명 작성](/windows/uwp/publish/write-a-great-app-description)을 참조하세요.
- **검색 키워드** – 고객이 마켓플레이스에서 제품을 찾는 데 사용할 수 있는 최대 3개의 검색 키워드를 입력합니다.
- **시작 지침**(필수) – 잠재 고객을 위해 앱 사용을 구성하고 시작하는 방법을 설명합니다.  이 빠른 시작에는 보다 자세한 온라인 설명서에 대한 링크가 포함될 수 있습니다. 이 필드에는 최대 3,000자의 텍스트를 입력할 수 있습니다.

#### <a name="description"></a>Description

이 필드는 필수입니다.

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>링크

- **개인정보처리방침**(필수) – 조직의 개인정보처리방침에 대한 링크입니다. 앱이 개인 정보 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공해야 합니다.
- **CSP 프로그램 마케팅 자료**(선택 사항) – 제품을 [CSP(클라우드 솔루션 공급자)](../cloud-solution-providers.md) 프로그램으로 확장하도록 선택하는 경우 마케팅 자료에 대한 링크를 제공합니다. CSP는 CSP 파트너가 제품을 번들링, 마케팅 및 재판매할 수 있도록 하여 제품을 더 광범위한 범위의 고객으로 확장합니다. 이러한 재판매인은 제품 마케팅 자료에 액세스해야 합니다. 자세한 내용은 [시장 진출 서비스](https://partner.microsoft.com/reach-customers/gtm)를 참조하세요.
- **유용한 링크**(선택 사항) – **제목** 및 **URL**을 제공하여 나열된 앱 또는 관련 서비스에 대한 선택적 온라인 보충 문서입니다. **+ URL 추가**를 클릭하여 유용한 링크를 더 추가합니다.

#### <a name="contact-information"></a>연락처 정보

- **연락처** – 각 고객 연락처에 대해 직원 **이름**, **전화 번호**, **이메일** 주소를 제공합니다(이들 정보는 공개적으로 표시되지 않음). **지원 URL**은 **지원 연락처** 그룹에 필요합니다(공개적으로 표시됨).

    - **지원 연락처**(필수) – 일반적인 지원 질문을 위한 연락처입니다.
    - **엔지니어링 연락처**(필수) - 기술 관련 질문을 위한 연락처입니다.
    - **채널 관리자 연락처**(필수) – CSP 프로그램과 관련된 재판매인 질문을 위한 연락처입니다.

#### <a name="files-and-images"></a>파일 및 이미지

- **Documents** (필수) – 제품에 대 한 관련 마케팅 문서를 하나 이상의 문서에 대 한 하나 이상의 문서 (PDF 형식)로 추가 합니다.
- **이미지**(선택 사항) – 제품의 로고 이미지가 마켓플레이스의 여러 위치에 나타날 수 있으며, 다음 픽셀 크기의 PNG 형식이어야 합니다.

    - **소형**(48 x 48, 필수)
    - **중형**(90 x 90, 필수)
    - **대형**(216 x 216, 필수)
    - **와이드**(255 x 115)

- **스크린샷** (필수)-1280 x 720 픽셀로 크기를 지정 하 여 제품을 보여 주는 최대 5 개의 스크린샷을 추가 합니다. 모든 이미지는 .PNG 형식이어야 합니다.
- **동영상**(선택 사항) – 제품을 보여 주는 동영상에 대한 링크를 추가합니다. 고객에게 제품과 함께 표시되는 YouTube 및/또는 Vimeo 동영상의 링크를 사용할 수 있습니다. 또한 동영상 미리 보기 이미지를 입력해야 합니다. 이 이미지는 PNG 형식의 1280 x 720 픽셀로 크기가 지정됩니다. 제품당 최대 4개의 동영상을 표시할 수 있습니다.

>[!NOTE]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인하세요.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 목록 리소스

- [Marketplace 제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

이 페이지에서는 제품을 더 광범위한 마켓플레이스 대상에 게시하기 전에 제품 출시를 위해 제한된 **미리 보기 대상 그룹**을 정의할 수 있습니다.

> [!IMPORTANT]
> 미리 보기에서 제품을 점검한 후에 제품을 마켓플레이스 공개 대상에 라이브 게시할 수 있도록 **라이브 상태로 전환**을 선택합니다.

선택적 설명과 함께 단일 AAD/MSA 계정 이 메일을 줄 단위로 추가합니다.

라이브 게시 전 MSA(Microsoft 계정) 또는 Azure Active Directory 계정에서 제품의 유효성을 검사하는 데 도움이 되도록 이메일 주소를 최대 10개, CSV 파일의 경우 최대 20개를 수동으로 추가합니다. 이러한 계정을 추가하여 마켓플레이스에 게시하기 전에 제품에 대한 미리 보기 액세스를 허용하는 대상 그룹을 정의합니다. 제품이 이미 라이브 상태인 경우 제품에 대한 변경 내용 또는 업데이트를 테스트하기 위한 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 비공개 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 마켓플레이스에서 라이브 게시되기 전에 제품에 대한 액세스를 허용합니다. 플랜을 만들어 비공개 대상 그룹만 사용할 수 있도록 설정할 수도 있습니다. **플랜 목록** 탭에서 **비공개 플랜** 확인란을 사용하여 프라이빗 대상 그룹을 정의할 수 있습니다. 그런 다음 Azure 테넌트 ID를 사용하여 최대 2만 명의 고객을 비공개 대상 그룹으로 정의할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="technical-configuration"></a>기술 구성

**기술 구성** 탭에서는 Marketplace에서 SaaS 서비스와 통신 하는 데 사용 하는 기술 세부 정보를 정의 합니다. 이 연결을 통해 최종 고객의 제품을 획득 하 고 관리 하는 경우 해당 제품을 프로 비전 할 수 있습니다. 

>[!Note]
>제안 세부 정보에서 이러한 세부 정보를 구성 하기 전에 [SaaS 처리 api](./pc-saas-fulfillment-api-v2.md) 와의 통합을 구현 해야 합니다.

수집 된 필드 사용을 설명 하는 다이어그램 및 자세한 설명은 [api](./pc-saas-fulfillment-api-v2.md)에 대 한 설명서에서 확인할 수 있습니다.

- **방문 페이지 url** (필수) – `https://contoso.com/signup` marketplace에서 제품을 획득 하 고 새로 만든 saas 구독에서 구성 프로세스를 트리거하는 최종 고객의 saas 사이트 URL (예:)을 정의 합니다.  이 URL은 특정 최종 고객의 SaaS 구매를 고유 하 게 식별 하는 marketplace 구매 id 토큰 매개 변수를 사용 하 여 호출 됩니다.  [확인](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) API를 사용 하 여 해당 SaaS 구독 세부 정보에 대해이 토큰을 교환 해야 합니다.  이러한 세부 정보 및 수집할 다른 모든 항목은 최종 고객 등록을 완료 하 고 구매를 활성화 하는 사용자의 경험을 기반으로 하는 고객 대화형 웹 페이지의 일부로 사용 해야 합니다.  이 페이지에서 사용자는 Azure Active Directory (Azure AD)를 사용 하 여 한 번의 클릭으로 인증을 통해 등록 해야 합니다. <br> <br> 또한 marketplace 구매 식별 토큰 매개 변수를 사용 하 여이 URL은 최종 고객이 Azure Portal 또는 M365 관리 센터에서 관리 되는 SaaS 환경을 시작할 때 호출 됩니다. 새 고객에 대해 구매한 후에 처음 토큰을 제공 하는 경우와 SaaS를 관리 하는 기존 고객을 위해 제공 된 경우 두 흐름을 모두 처리 해야 합니다. <br> <br> 여기에서 구성 하는 방문 페이지는 24/7을 실행 해야 합니다. Marketplace에서 만든 SaaS 제품의 새로운 구매 또는 제품의 활성 구독에 대 한 구성 요청에 대 한 알림이 표시 되는 유일한 방법입니다.

- **Connection webhook** (필수) – Microsoft에서 사용자에 게 전송 해야 하는 모든 비동기 이벤트 (예: SaaS 구독이 취소 됨)에 대해 연결 webhook URL을 제공 해야 합니다. 이 URL을 호출 하 여 이벤트에 대해 알려줍니다. <br> <br> 제공 하는 웹 후크는 marketplace를 통해 구매한 고객의 SaaS 구독에 대 한 업데이트에 대 한 알림을 받는 유일한 방법 이므로 24/7을 실행 해야 합니다.  웹 후크 시스템이 아직 없는 경우 가장 간단한 구성은 게시 되는 모든 이벤트를 수신 대기 하 고 적절 하 게 처리 하는 HTTP 끝점 논리 앱을 보유 하는 것입니다 (예: `https://prod-1westus.logic.azure.com:443/work` ). 자세한 내용은 [Logic Apps의 HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](../../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

- **AZURE ad 테 넌 트 ID** (필수) – Azure Portal 내에서는 두 서비스 간의 연결에 대 한 유효성을 검사 하 여 인증 된 통신을 수행할 수 있도록 [Azure Active Directory (ad) 앱을 만들어야](../../active-directory/develop/howto-create-service-principal-portal.md) 합니다. [테 넌 트 ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 찾으려면 Azure Active Directory로 이동 하 고 **속성**을 선택한 다음 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다.

- **AZURE AD 앱 id** (필수) – [응용 프로그램 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)도 필요 합니다. 해당 값을 가져오려면 Azure Active Directory로 이동 하 여 **앱 등록**을 선택한 다음 나열 된 **응용 프로그램 ID** 번호 (예:)를 찾습니다 `50c464d3-4930-494c-963c-1e951d15360e` .

>[!Note]
>Azure AD 앱 ID는 파트너 센터 계정의 게시자 ID에 연결 됩니다.  모든 제품에서 동일한 응용 프로그램 ID를 사용 하는지 확인 합니다.

>[!Note]
>게시자의 파트너 센터에 두 개 이상의 다른 계정이 있는 경우 각 계정에 대해 둘 이상의 서로 다른 Azure AD 앱 Id를 사용 해야 합니다. 파트너 센터의 각 파트너 계정은이 계정을 통해 게시 되는 모든 SaaS 제품에 대해 고유한 Azure AD 앱 ID를 사용 해야 합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="plan-overview"></a>플랜 개요

이 페이지를 통해 동일한 제품 내에서 다양한 플랜 옵션을 제공할 수 있습니다. 이러한 플랜(SKU라고도 함)은 버전, 수익화 또는 서비스 계층 측면에서 다를 수 있습니다. 마켓플레이스에서 제품을 판매하려면 플랜을 하나 이상 설정해야 합니다.

플랜이 만들어지면 플랜 이름, ID, 가격 책정 모델, 가용성(공개 또는 비공개), 현재 게시 상태 및 사용 가능한 작업이 표시됩니다.

**플랜 개요**에서 사용할 수 있는 **작업**은 플랜의 현재 상태에 따라 달라지며 다음이 포함될 수 있습니다.

- 플랜 상태가 **초안**인 경우 - 초안 삭제
- 플랜 상태가 **라이브**인 경우 - 플랜 판매 중지 또는 비공개 대상 그룹 동기화

**새 플랜 만들기**(Microsoft를 통해 판매하려면 최소 1개의 플랜)

- **플랜 ID:** 이 제품의 각 플랜에 대한 고유한 플랜 ID를 만듭니다. 이 ID는 제품 URL 및 Azure Resource Manager 템플릿(해당하는 경우)에서 고객에게 표시됩니다. 소문자 영숫자, 대시 또는 밑줄만 사용합니다. 이 플랜 ID에는 최대 50자까지 사용할 수 있습니다. 만들기를 선택한 후에는 ID를 수정할 수 없습니다.
- **플랜 이름:** 고객이 제품 내에서 선택할 플랜을 확인할 때 이 이름이 표시됩니다. 이 제품의 각 플랜에 고유한 제품 이름을 만듭니다. 플랜 이름은 동일한 제품에 포함될 수 있는 소프트웨어 요금제를 구분하는 데 사용됩니다(예: 제품 이름: Windows Server, 플랜: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>플랜 목록

이 페이지에서는 플랜 이름 및 설명을 정의할 수 있습니다. <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **이름** – 미리 보기 **새 플랜** 항목을 기준으로 미리 채워져 있으며 마켓플레이스에 표시되는 제품의 "소프트웨어 플랜"의 제목으로 표시됩니다.
- **설명** – 이 설명은 이 소프트웨어 플랜의 고유한 정의와 제품 내의 다른 소프트웨어 플랜과의 차이점을 설명하는 기회입니다. 최대 500자까지 포함할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

#### <a name="pricing-and-availability"></a>가격 책정 및 가용성

이 페이지에서는 이 플랜을 사용할 수 있는 시장, 원하는 수익화 모델, 가격 및 청구 기간을 구성할 수 있습니다. 또한 플랜이 모든 사용자에게 표시되도록 할지 아니면 특정 고객(비공개 대상 그룹)에게만 표시되도록 할지 지정할 수 있습니다.

#### <a name="markets-optional"></a>시장(선택 사항)

모든 플랜은 하나 이상의 시장에서 사용할 수 있어야 합니다. **시장 편집**을 선택하고 이 플랜을 제공할 시장 위치의 확인란을 선택합니다. 이 페이지에는 Microsoft가 게시자를 대신하여 판매세와 이용세를 송금하는 "세금 송금" 국가/지역을 선택하는 검색 상자 및 옵션이 포함되어 있습니다.

이미 플랜 가격을 미국 달러(USD)로 설정하고 다른 시장 위치를 추가하는 경우 새 시장의 가격은 현재 환율에 따라 계산됩니다. 게시 전에 각 시장의 가격을 검토합니다. 변경 내용을 저장한 후 "가격 내보내기(.xlsx)" 링크를 사용하여 가격 책정을 확인하세요.

계속하기 전에 **저장**을 선택합니다.

#### <a name="pricing"></a>가격 책정

##### <a name="pricing-model"></a>가격 책정 모델

**정액제** – 단일 월간 또는 연간 가격의 정액 요금으로 제품에 대한 액세스를 허용합니다. 이를 사이트 기반 가격 책정이라고도 합니다. 이 가격 책정 모델을 사용하는 경우 필요에 따라 비 표준 단위에 따라 고객에게 요금을 청구하기 위해 Marketplace 계량 서비스 API를 사용하는 종량제 플랜을 정의할 수 있습니다.  요금 청구에 대한 자세한 내용은 [ 계량 서비스를 사용한 요금 청구](./saas-metered-billing.md)를 참조하세요.  사용 동작이 SaaS 서비스에 대해 버스트로 된 경우에도이 옵션을 사용 해야 합니다.  매일 또는 매시간 계획을 자주 전환 하는 것은 고객에 게 권장 하지 않습니다.

**사용자당** - 제품에 액세스하는 사용자 수 또는 상시 사용자 수를 기준으로 한 가격으로 제품에 대한 액세스를 허용합니다. 이 사용자 기반 모델을 사용하면 가격에 따라 허용되는 최소 및 최대 사용자 수를 설정할 수 있습니다. 이러한 방식으로 여러 플랜을 구성하여 사용자 수를 기준으로 다양한 가격 지점을 구성할 수 있습니다.  이러한 필드는 선택 사항입니다. 선택하지 않을 경우 사용자 수에 제한이 없는 것으로 해석됩니다(최소는 1, 최대는 시스템에서 지원할 수 있는 최대한). 이러한 필드는 플랜 업데이트의 일부로 편집할 수 있습니다.

게시 후에는 청구 가격 책정 모델 선택을 변경할 수 없습니다. 또한 동일한 제품의 모든 플랜은 동일한 가격 책정 모델을 공유해야 합니다.

##### <a name="user-limits"></a>사용자 제한

해당하는 경우 최소 및 최대 사용자 제한을 선택하고 설정합니다.

##### <a name="billing-term-and-price"></a>청구 기간 및 가격

고객이 나열된 가격을 지불해야 하는 **기간** 및 **가격**을 선택합니다. 하나 이상의 월별 또는 연간 가격을 제공해야 하며, 두 옵션 모두 고객에게 제공할 수 있습니다.

USD(미국 달러)로 설정된 가격은 저장할 때 현재 환율을 사용하여 선택한 모든 시장의 현지 통화로 변환됩니다. 가격 책정 스프레드시트를 내보내고 각 시장에서 가격을 검토하여 게시하기 전에 해당 가격의 유효성을 검사합니다. 개별 시장에서 사용자 지정 가격을 설정하려면 가격 책정 스프레드시트를 수정하고 가져옵니다. 게시자가 이 가격 책정을 검증해야 하며 이러한 설정을 소유합니다.
*\** 가격 책정 데이터를 내보내려면 먼저 가격 책정 변경 내용을 저장해야 합니다.

플랜이 게시된 후에 변경 가능한 사항에는 일부 제한이 있으므로 게시 전에 가격 책정을 신중하게 검토해야 합니다.

- 플랜이 게시된 후에는 가격 책정 모델을 변경할 수 없습니다.
- 플랜에 대한 청구 기간이 게시된 후에는 나중에 제거할 수 없습니다.
- 특정 시장에 대한 가격이 게시된 후에는 나중에 변경할 수 없습니다.

#### <a name="free-trial"></a>평가판

상업적 marketplace를 통해 SaaS 제품을 사용 하면 Microsoft를 통해 판매 하는 경우 1 개월 평가판을 제공할 수 있습니다. 종량제 플랜을 제외한 모든 청구 모델 및 기간에 대해 무료 평가판이 지원됩니다. 이 옵션을 사용하면 고객은 1개월 무료 액세스를 통해 진입 장벽을 낮출 수 있습니다.  제품 내에서 플랜에 대해 무료 평가판을 사용하도록 선택하는 경우 고객은 최초 1개월 기간이 끝나기 전에 유료 구독으로 변환할 수 없습니다.  이 기간 동안 제품을 구매하는 고객은 무료 평가판에서 지원하는 모든 플랜을 사용해 보고 각 플랜 간 변환을 할 수 있습니다.  유료 구독으로 변환은 해당 기간이 끝날 때 자동으로 처리됩니다.

>[!NOTE]
>고객이 무료 평가판을 사용하지 않고 특정 플랜으로 전환하도록 선택하는 경우에는 변환이 처리되지만 무료 평가판은 즉시 손실됩니다. 또한 고객이 특정 플랜에 대한 요금을 지불하고 나면 무료 평가판을 지원하는 SKU로 변환하는 경우에도 더 이상 동일한 구독에서 무료 평가판을 사용할 수 없습니다.

여기에서 제품의 각 플랜에 대한 무료 평가판을 구성할 수 있습니다. 1개월 무료 평가판을 허용하려면 이 확인란을 선택합니다.

![1개월 무료 평가판 확인란](./media/free-trial-enable.png)

>[!NOTE]
>트랜잭션 가능한 제품이 무료 평가판을 적용하여 게시된 후에는 해당 플랜에 대해 사용하지 않도록 설정할 수 없습니다. 최초 게시에서 이 설정이 올바른지 확인하여 플랜을 다시 만들 일이 없도록 해야 합니다.

현재 무료 평가판에 참여하는 고객 구독에 대한 정보를 얻으려면 true 또는 false로 표시되는 새 API 속성 `isFreeTrial`을 사용합니다. 자세한 내용은 [SaaS 구독 가져오기 API](pc-saas-fulfillment-api-v2.md#get-subscription)를 참조하세요.

>[!NOTE]
>마켓플레이스 계량 서비스를 활용하는 플랜에 대한 무료 평가판은 지원되지 않습니다.

#### <a name="plan-visibility"></a>플랜 표시 여부

모든 사용자 또는 선택한 특정 대상 그룹에 표시되도록 각 플랜을 구성할 수 있습니다. Azure AD 테넌트 ID를 사용하여 이 제한된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

##### <a name="privacy"></a>개인 정보 보호

플랜을 비공개로 설정하고 선택하는 제한된 대상 그룹에게만 표시하려면 **비공개 플랜**을 선택합니다. 비공개 플랜으로 게시된 플랜은 대상 그룹을 업데이트하거나 모든 사용자가 해당 플랜을 사용할 수 있도록 지정할 수 있습니다. 플랜이 모든 사용자에게 표시되도록 게시된 후에는 모든 사용자에게 계속 표시되어야 합니다(다시 비공개 플랜으로 구성할 수 없음).

##### <a name="restricted-audience-tenant-ids"></a>**제한된 대상 그룹(테넌트 ID)**

이 비공개 플랜에 액세스할 수 있는 대상 그룹을 할당합니다. 지정된 각 테넌트 ID에 대한 설명을 포함하는 옵션과 함께 테넌트 ID를 사용하여 액세스 권한을 할당합니다. 최대 10개의 테넌트 ID를 추가할 수 있으며, .csv 스프레드시트 파일을 가져오는 경우 2만 개의 고객 테넌트 ID를 추가할 수 있습니다.

테넌트는 ID가 GUID(고유 식별자, 리소스를 식별하는 데 사용되는 128비트 정수)로 표시되는 조직의 표현입니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 만들 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 등록) 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다. 테넌트를 확인하려면 애플리케이션을 관리하는 데 사용하려는 계정으로 Azure Portal에 로그인합니다. 테넌트가 있으면 자동으로 로그인되고 계정 이름 바로 아래에 테넌트 이름이 표시됩니다. Azure Portal의 오른쪽 위에 있는 사용자의 계정 이름을 가리켜 사용자의 이름, 이메일, 디렉터리/테넌트 ID(GUID) 및 사용자의 도메인을 표시합니다. 계정이 여러 테넌트와 연결되어 있으면, 계정 이름을 선택하여 테넌트 간에 전환할 수 있는 메뉴를 열 수 있습니다. 각 테넌트가 자기만의 ID를 가지고 있습니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 URL을 사용하여 조직의 테넌트 ID를 조회할 수도 있습니다.

SaaS 제품이 테넌트 ID를 사용하여 비공개 대상 그룹을 정의하지만, 다른 제품 유형은 Azure 구독 ID(GUID로도 표시됨)를 사용할 수 있습니다.

> [!NOTE]
> 비공개 대상 그룹(또는 제한된 대상 그룹)은 미리 보기 대상 그룹과 다릅니다. **[미리 보기](#preview-audience)** 페이지에서 미리 보기 대상 그룹을 정의할 수 있습니다. 미리 보기 대상 그룹은 마켓플레이스에서 제품이 라이브 게시되기 전에 제품에 대한 액세스를 허용합니다. 비공개 대상 그룹은 특정 플랜에만 적용되는 한편, 미리 보기 대상 그룹은 모든 플랜(비공개 또는 공개)을 볼 수 있지만 플랜을 테스트하고 검증하는 제한된 미리 보기 기간 동안만 가능합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>마켓플레이스 제품 내 플랜 목록 예제

:::image type="content" source="media/marketplace-plan.png" alt-text="메모를 사용 하는 예제 마켓플레이스 계획":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 플랜 이름
2. 플랜 설명

<br>

## <a name="cloud-solution-provider-csp-reseller-audience"></a>CSP(클라우드 솔루션 공급자) 재판매인 대상 그룹

CSP 프로그램에서 제품을 사용할 수 있도록 선택하면 클라우드 솔루션 공급자가 고객에게 번들로 제공되는 솔루션의 일부로 제품을 판매할 수 있습니다. 자세한 내용은 [클라우드 솔루션 공급자](https://go.microsoft.com/fwlink/?linkid=2111109)를 참조하세요.

## <a name="publish"></a>게시

제품의 필수 섹션을 모두 완료한 후에는 포털의 우측 상단 모서리에서 **검토 및 게시**를 선택합니다.

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

이 제품을 처음으로 게시하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에서 완료 상태를 확인합니다.
    - **시작되지 않음** – 섹션이 작업되지 않았으므로 완료해야 합니다.
    - **완료되지 않음** – 섹션에 수정해야 하는 오류가 있거나 추가 정보를 입력해야 합니다. 섹션으로 돌아가서 업데이트해야 합니다.
    - **완료됨** – 섹션이 완료되고 필요한 모든 데이터가 입력되었으며 오류가 발생하지 않습니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료됨 상태여야 합니다.
- 앱을 이해 하는 데 도움이 되는 보조 노트 뿐만 아니라 앱이 올바르게 테스트 되었는지 확인 하기 위해 인증 팀에 테스트 지침을 제공 합니다.
- **제출**을 선택하여 게시할 제품을 제출합니다. 제품의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일을 보내 드립니다. 파트너 센터로 돌아가서 공개 **를 선택 하 여 공개** (또는 개인 제품의 경우 비공개 사용자에 게 제안)를 게시 합니다.

## <a name="next-step"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
