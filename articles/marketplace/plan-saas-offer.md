---
title: Microsoft 상업적 marketplace에 대 한 SaaS 제품을 계획 하는 방법
description: Microsoft 파트너 센터의 상용 마켓플레이스 프로그램을 사용 하 여 Microsoft AppSource, Azure Marketplace 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 나열 하거나 판매 하기 위한 새로운 SaaS (software as a service) 제품을 계획 하는 방법입니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: 7f3f3b2c5927b31bde4575a08888e8844f2a1027
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130003"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>상업적 marketplace에 대 한 SaaS 제품을 계획 하는 방법

이 문서에서는 Microsoft 상업적 marketplace에 SaaS (software as a service) 제품을 게시 하기 위한 다양 한 옵션 및 요구 사항을 설명 합니다. SaaS 제품을 사용 하면 개별 컴퓨터에서 로컬 설치 대신 온라인 구독을 통해 고객에 게 소프트웨어 솔루션을 제공 하 고 라이선스를 제공할 수 있습니다. 이 문서는 파트너 센터를 사용 하 여 상업적 marketplace에 게시할 제품을 준비 하는 데 도움이 됩니다.

## <a name="listing-options"></a>나열 옵션

새 SaaS 제품 게시를 준비할 때 선택할 _목록_ 옵션을 결정 해야 합니다. 파트너 센터에서 제품을 만들 때 제공 해야 하는 추가 정보를 결정 합니다. [상업적 marketplace에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)에 설명 된 대로 **제품 설정** 페이지에서 목록 옵션을 정의 합니다.

다음 표에서는 상업적 marketplace의 SaaS 제품에 대 한 목록 옵션을 보여 줍니다.

| 목록 옵션 | 트랜잭션 프로세스 |
| ------------ | ------------- |
| 연락처 | 고객은 목록의 정보에서 직접 연락 합니다.``*`` |
| 평가판 | 고객은 Azure Active Directory (Azure AD)를 통해 대상 URL로 리디렉션됩니다.``*`` |
| 지금 받기 (무료) | 고객은 Azure AD를 통해 대상 URL로 리디렉션됩니다.``*`` |
| Microsoft를 통해 판매  | Microsoft를 통해 판매 되는 제품을 _불가능_ 제품 이라고 합니다. 불가능 제품은 Microsoft가 게시자를 대신해 소프트웨어 라이선스에 대 한 비용 교환을 용이 하 게 하는 것입니다. Microsoft는 선택한 가격 책정 모델을 사용 하 여 SaaS 제안을 청구 하 고 사용자 대신 고객 트랜잭션을 관리 합니다. Azure 인프라 사용 요금은 파트너에 게 직접 청구 됩니다. 가격 책정 모델에서 인프라 비용을 고려해 야 합니다. 이 내용은 아래의 [SaaS 청구](#saas-billing) 에 자세히 설명 되어 있습니다.  |
|||

``*`` 게시자는 주문, 배송, 계량, 청구, 송장 청구, 지불 및 수집을 포함 하 되이에 제한 되지 않고 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원할 책임이 있습니다.

이러한 목록 옵션에 대 한 자세한 내용은 [상업적 marketplace의 기능](marketplace-commercial-transaction-capabilities-and-considerations.md)을 참조 하세요.

제품이 게시 된 후 제품에 대해 선택한 목록 옵션이 제품 목록 페이지의 왼쪽 위 모서리에 단추로 나타납니다. 예를 들어 다음 스크린샷에서는 **나에 게 연락** 및 **시험** 사용 단추와 Azure Marketplace의 제품 목록 페이지를 보여 줍니다.

![온라인 스토어의 제품 목록을 보여 줍니다.](./media/listing-options.png)

## <a name="technical-requirements"></a>기술적인 요구 사항

기술 요구 사항은 제품에 대해 선택 하는 목록 옵션에 따라 다릅니다.

_연락처_ 목록 옵션에는 기술 요구 사항이 없습니다. CRM 시스템을 연결 하 여 고객 리드를 관리할 수 있습니다 .이에 대해서는이 문서의 뒷부분에 나오는 [고객 리더](#customer-leads) 섹션에서 설명 합니다.

_지금 가져오기 (무료)_ , _무료 평가판_ 및 Microsoft 목록 옵션을 _통해 판매_ 하는 기술 요구 사항은 다음과 같습니다.

- SaaS 응용 프로그램은 다중 테 넌 트 솔루션 이어야 합니다.
- 사용자를 인증 하기 위해 Microsoft 계정 (MSA)과 [Azure Active Directory (AZURE AD)](https://azure.microsoft.com/services/active-directory/) 를 모두 사용 하도록 설정할 수 있습니다.
- 방문 페이지를 만들어야 합니다. 사용자가 제품을 구매한 후 방문 페이지로 이동 하 여 필요한 추가 프로 비전 또는 설치를 용이 하 게 합니다. 방문 페이지를 만드는 방법에 대 한 지침은 다음 문서를 참조 하세요.
  - [상업적 marketplace에서 불가능 SaaS 제품에 대 한 방문 페이지 빌드](azure-ad-transactable-saas-landing-page.md)
  - [상용 marketplace에서 무료 또는 평가판 SaaS 제품에 대 한 방문 페이지 빌드](azure-ad-free-or-trial-landing-page.md)

이러한 추가 기술 요구 사항은 Microsoft에서 _판매_ (불가능) 목록 옵션에만 적용 됩니다.

- 방문 페이지에 액세스 하는 구매 사용자는 SSO (Single Sign-On) id 관리 및 인증을 사용 하 여 Azure AD가 필요 합니다. 자세한 지침은 [상업적 marketplace의 AZURE AD 및 불가능 SaaS 제품](azure-ad-saas.md)을 참조 하세요.
- [SaaS 처리 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md) 를 사용 하 여 Azure Marketplace 및 Microsoft AppSource와 통합 해야 합니다. 사용자 계정 및 서비스 계획을 만들고, 업데이트 하 고, 삭제할 수 있도록 SaaS 구독과 상호 작용할 수 있는 서비스를 노출 해야 합니다. 24시간 내에 중요한 API 변경 내용이 지원되어야 합니다. 중요하지 않은 API 변경 내용은 정기적으로 릴리스됩니다. 수집 된 필드 사용을 설명 하는 다이어그램 및 자세한 설명은 [api](./partner-center-portal/pc-saas-fulfillment-api-v2.md)에 대 한 설명서에서 확인할 수 있습니다.
- 제품에 대 한 계획을 하나 이상 만들어야 합니다. 요금제는 게시 하기 전에 선택 하는 가격 책정 모델을 기준으로 가격이 책정 됩니다 _per-user_ _._ [요금제](#plans) 에 대 한 자세한 내용은이 문서의 뒷부분에 제공 됩니다.
- 고객은 언제 든 지 제품을 취소할 수 있습니다.

### <a name="technical-information"></a>기술 정보

불가능 제품을 만드는 경우 **기술 구성** 페이지에 대 한 다음 정보를 수집 해야 합니다. 불가능 제품을 만드는 대신 독립적으로 트랜잭션을 처리 하도록 선택 하는 경우이 섹션을 건너뛰고 [테스트 드라이브로](#test-drives)이동 합니다.

- **방문 페이지 url** : 사용자가 상업적 marketplace에서 제품을 확보 한 후 사용자가 전송 되는 SAAS 사이트 url (예: `https://contoso.com/signup` )은 새로 만든 saas 구독에서 구성 프로세스를 트리거합니다. 이 URL은 대화형 등록 페이지에 대 한 프로 비전 세부 정보를 가져오기 위해 처리 Api를 호출 하는 데 사용할 수 있는 토큰을 수신 합니다.

  이 URL은 특정 고객의 SaaS 구매를 고유 하 게 식별 하는 marketplace 구매 id 토큰 매개 변수를 사용 하 여 호출 됩니다. [확인 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)를 사용 하 여 해당 SaaS 구독 세부 정보에 대해이 토큰을 교환 해야 합니다. 이러한 세부 정보 및 수집 하려는 다른 사용자는 고객 등록을 완료 하 고 해당 구매를 활성화 하는 사용자의 경험을 기반으로 하는 고객 대화형 웹 페이지의 일부로 사용 해야 합니다. 이 페이지에서 사용자는 Azure Active Directory (Azure AD)를 사용 하 여 한 번의 클릭으로 인증을 통해 등록 해야 합니다.

  사용자가 Azure Portal 또는 M365 관리 센터에서 관리 되는 SaaS 환경을 시작할 때 marketplace 구매 id 토큰 매개 변수를 포함 하는이 URL도 호출 됩니다. 새 고객이 구매한 후 처음으로 토큰이 제공 되는 경우와 SaaS 솔루션을 관리 하는 기존 고객에 대해 다시 제공 되는 경우 두 흐름을 모두 처리 해야 합니다.

    구성 하는 방문 페이지는 24/7을 실행 해야 합니다. 이는 상업적 marketplace에서 만든 SaaS 제품의 새로운 구매 또는 제품의 활성 구독에 대 한 구성 요청에 대 한 통지를 받는 유일한 방법입니다.

- **연결 webhook** : Microsoft에서 사용자에 게 전송 해야 하는 모든 비동기 이벤트 (예: SaaS 구독이 취소 된 경우)의 경우 연결 webhook URL을 제공 해야 합니다. 이 URL을 호출 하 여 이벤트에 대해 알려줍니다.

  제공 하는 웹 후크는 24/7을 실행 해야 하며,이는 상업적 marketplace를 통해 구매한 고객의 SaaS 구독에 대 한 업데이트에 대 한 알림을 받을 수 있는 유일한 방법입니다.

  > [!NOTE]
  > Azure Portal 내에서 단일 Azure 앱 ID를 사용 하 여 두 서비스 간의 연결을 인증할 수 있도록 하는 단일 테 넌 트 [Azure Active Directory (AZURE AD) 앱](../active-directory/develop/howto-create-service-principal-portal.md) 을 만들어야 합니다. [테 넌 트 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 찾으려면 Azure Active Directory로 이동 하 고 **속성** 을 선택한 다음 나열 된 디렉터리 ID 번호를 확인 합니다. 예: `50c464d3-4930-494c-963c-1e951d15360e`.

- **Azure Active Directory 테 넌 트 id** : (디렉터리 id 라고도 함) Azure Portal 내에서 API의 ACL (액세스 제어 목록)에 추가 하 여 호출할 권한이 있는지 확인할 수 있도록 [AD (Azure Active Directory) 앱을 등록](../active-directory/develop/howto-create-service-principal-portal.md) 해야 합니다. Azure Active Directory (AD) 앱에 대 한 테 넌 트 ID를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동 합니다. **표시 이름** 열에서 앱을 선택 합니다. 그런 다음 나열 된 **디렉터리 (테 넌 트) ID** 번호 (예: `50c464d3-4930-494c-963c-1e951d15360e` )를 찾습니다.

- **Azure Active Directory 응용 프로그램 id** : [응용 프로그램 id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)도 필요 합니다. 해당 값을 가져오려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동 합니다. **표시 이름** 열에서 앱을 선택 합니다. 그런 다음 나열 된 응용 프로그램 (클라이언트) ID 번호 (예:)를 찾습니다 `50c464d3-4930-494c-963c-1e951d15360e` .

  Azure AD 응용 프로그램 ID는 파트너 센터 계정에서 게시자 ID와 연결 됩니다. 해당 계정의 모든 제품에 대해 동일한 응용 프로그램 ID를 사용 해야 합니다.

  > [!NOTE]
  > 게시자의 파트너 센터에 두 개 이상의 다른 계정이 있는 경우 각 계정에 대해 둘 이상의 서로 다른 Azure AD 앱 Id를 사용 해야 합니다. 파트너 센터의 각 파트너 계정은이 계정을 통해 게시 되는 모든 SaaS 제품에 대해 고유한 Azure AD 앱 ID를 사용 해야 합니다.

## <a name="test-drives"></a>시험 사용
SaaS 앱에 대해 테스트 드라이브를 사용 하도록 선택할 수 있습니다. 시험 사용을 통해 고객은 고정 된 시간 동안 미리 구성 된 환경에 액세스할 수 있습니다. 모든 게시 옵션에 대해 테스트 드라이브를 사용 하도록 설정할 수 있지만이 기능에는 추가 요구 사항이 있습니다. 테스트 드라이브에 대 한 자세한 내용은 [test drive 란?](what-is-test-drive.md)을 참조 하세요. 다양 한 종류의 테스트 드라이브를 구성 하는 방법에 대 한 자세한 내용은 [테스트 드라이브 기술 구성](test-drive-technical-configuration.md)을 참조 하세요.

> [!TIP]
> 시험 [사용은 평가판](plans-pricing.md#free-trials)과 다릅니다. 시험 사용, 평가판 또는 둘 다를 제공할 수 있습니다. 사용자는 일정 기간 동안 고객에 게 솔루션을 제공 합니다. 그러나 테스트 드라이브에는 실제 구현 시나리오에서 설명 하는 제품의 주요 기능 및 이점에 대 한 실습 자체 둘러보기도 포함 되어 있습니다.

## <a name="customer-leads"></a>잠재 고객

제품을 CRM (고객 관계 관리) 시스템에 연결 하 여 고객 정보를 수집 해야 합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 제품을 찾은 제품 이름, ID 및 온라인 스토어와 함께 이러한 고객 세부 정보는 구성 된 CRM 시스템으로 전송 됩니다. 상업적 marketplace는 Azure 테이블을 사용 하거나 전원 자동화를 사용 하 여 HTTPS 끝점을 구성 하는 옵션과 함께 다양 한 CRM 시스템을 지원 합니다.

제공을 만든 후 또는 후에 언제 든 지 CRM 연결을 추가 하거나 수정할 수 있습니다. 자세한 지침은 [상용 marketplace 제품의 고객 리드](partner-center-portal/commercial-marketplace-get-customer-leads.md)를 참조 하세요.

## <a name="selecting-an-online-store"></a>온라인 스토어 선택

SaaS 제안을 게시할 때 Microsoft AppSource, Azure Marketplace 또는 둘 다에 나열 됩니다. 각 온라인 스토어는 고유한 고객 요구 사항을 제공 합니다. AppSource는 비즈니스 솔루션을 위한 것 이며 IT 솔루션에 대 한 Azure Marketplace입니다. 제품 유형, 거래 기능 및 범주에 따라 제품이 게시 되는 위치가 결정 됩니다. 범주 및 하위 범주는 솔루션 유형에 따라 각 온라인 저장소에 매핑됩니다. 온라인 저장소를 선택 하는 방법에 대 한 자세한 내용은 [온라인 저장소 선택](determine-your-listing-type.md#selecting-an-online-store)을 참조 하세요.

## <a name="legal-contracts"></a>법적 계약

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft는 상업적 marketplace에서 제품에 사용할 수 있는 표준 계약을 제공 합니다. 표준 계약에 따라 소프트웨어를 제공 하는 경우 고객은 한 번만 읽고 동의 하면 되며 사용자 지정 사용 약관을 만들 필요가 없습니다.

표준 계약을 사용 하도록 선택 하는 경우 표준 계약에 유니버설 수정 용어 및 최대 10 개의 사용자 지정 개정를 추가할 수 있습니다. 표준 계약 대신 사용자 고유의 사용 조건을 사용할 수도 있습니다. 이러한 세부 정보는 **속성** 페이지에서 관리 합니다. 자세한 내용은 [Microsoft 상업적 marketplace에 대 한 표준 계약](standard-contract.md)을 참조 하세요.

> [!NOTE]
> 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 한 후에는 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약이 나 사용자 고유의 사용 약관에 따라 솔루션을 제공 합니다. 표준 계약의 조건을 수정 하려면 표준 계약 개정을 통해이 작업을 수행할 수 있습니다.

## <a name="offer-listing-details"></a>제안 목록 세부 정보

파트너 센터에서 [새 SaaS 제품을 만들](create-new-saas-offer.md) 때 **제품 목록** 페이지에 텍스트, 이미지, 선택적 비디오 및 기타 세부 정보를 입력 합니다. 다음 예제와 같이 상용 marketplace에서 제품 목록을 검색할 때 고객이 보게 될 정보입니다.

:::image type="content" source="./media/example-saas-1.png" alt-text="이 제품이 Microsoft AppSource 표시 되는 방식을 보여 줍니다.":::

**호출 설명**

1. 로고
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

다음 예에서는 Azure Portal의 제품 목록을 보여 줍니다.

![Azure Portal의 제품 목록을 보여 줍니다.](./media/example-managed-services.png)

**호출 설명**

1. 제목
1. 설명
1. 유용한 링크
1. 스크린샷

> [!NOTE]
> 제품 설명이 "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다."로 시작 하는 경우 제품 목록 콘텐츠가 영어로 표시 되지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 이러한 항목 중 일부를 미리 준비 합니다. 별도로 언급 하지 않는 한 다음 항목이 필요 합니다.

- **이름** :이 이름은 상용 marketplace에서 제품의 제목으로 표시 됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약** : 100 자 이하의 줄 바꿈 없이 단일 문장으로 제품의 목적 또는 기능을 설명 합니다. 이 요약은 상용 marketplace 목록 검색 결과에 사용 됩니다.
- **설명** :이 설명은 상업적 marketplace 목록에 표시 됩니다. 가치 제안, 주요 이점, 의도 한 사용자 기반, 범주 또는 업계 연결, 앱 내 구매 기회, 모든 필수 공개 및 자세한 내용을 볼 수 있는 링크를 포함 하는 것이 좋습니다.
    
    이 텍스트 상자에는 설명이 더 유용 하 게 만드는 데 사용할 수 있는 다양 한 텍스트 편집기 컨트롤이 있습니다. HTML 태그를 사용 하 여 설명 서식을 지정할 수도 있습니다. HTML 태그를 포함 하 여이 상자에 최대 3000 자의 텍스트를 입력할 수 있습니다. 추가 팁은 [유용한 앱 설명 작성](/windows/uwp/publish/write-a-great-app-description)을 참조하세요.

- **시작 지침** : Microsoft (불가능 제품)를 통해 제품을 판매 하도록 선택 하는 경우이 필드는 필수입니다. 고객이 SaaS 제품에 연결 하는 데 도움이 되는 지침입니다. 최대 3000 문자 텍스트를 추가 하 고 자세한 온라인 설명서에 대 한 링크를 추가할 수 있습니다.
- **검색 키워드** (옵션): 고객이 온라인 상점에서 제품을 찾는 데 사용할 수 있는 검색 키워드를 최대 3 개까지 제공 합니다. 제품 **이름** 및 **설명을** 포함할 필요가 없습니다. 해당 텍스트가 검색에 자동으로 포함 됩니다.
- **개인 정보 취급 방침 링크** : 회사의 개인 정보 취급 방침에 대 한 URL입니다. 유효한 개인 정보 취급 방침을 제공 해야 하며, 앱이 개인 정보 법률 및 규정을 준수 하는지 확인 해야 합니다.
- **연락처 정보** : 조직에서 다음 연락처를 지정 해야 합니다.
  - **지원 연락처** : 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 전자 메일을 제공 합니다. 또한 지원 웹 사이트에 대 한 URL을 포함 해야 합니다.
  - **엔지니어링 연락처** : 제품에 문제가 있는 경우 직접 사용할 수 있는 이름, 전화 번호 및 전자 메일을 Microsoft에 제공 합니다. 이 연락처 정보는 상업적 marketplace에 나열 되지 않습니다.
  - **Csp 프로그램 연락처** (선택 사항): csp 프로그램을 옵트인 (opt in) 하는 경우 이름, 전화 번호 및 전자 메일을 입력 합니다. 그러면 이러한 파트너가 질문에 대해 연락할 수 있습니다. 마케팅 자료에 대 한 URL을 포함할 수도 있습니다.
- **유용한 링크** (선택 사항): 제품의 사용자에 대 한 다양 한 리소스에 대 한 링크를 제공할 수 있습니다. 예를 들어 포럼, Faq, 릴리스 정보 등이 있습니다.
- **지원 문서** : 백서, 브로슈어, 검사 목록 또는 PowerPoint 프레젠테이션과 같은 최대 3 개의 고객 관련 문서를 제공할 수 있습니다.
- **미디어 – 로고** : **큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작고** **중간** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로 바꿀 수 있습니다.

   - 큼 (216 x 216에서 350 x 350 px, required)
   - 중간 (90 x 90 px, 선택 사항)
   - 작음 (48 x 48 px, 선택 사항)

  이러한 로고는 온라인 상점에서 다른 위치에 사용 됩니다.

  - 작은 로고는 Azure Marketplace 검색 결과 및 Microsoft AppSource 기본 페이지 및 검색 결과 페이지에 표시 됩니다.
  - Microsoft Azure에서 새 리소스를 만들 때 Medium 로고가 표시 됩니다.
  - Azure Marketplace 및 Microsoft AppSource의 제품 목록 페이지에 많은 로고가 표시 됩니다.

- **미디어 스크린샷** : 제품의 작동 방식을 보여 주는 다음 요구 사항에 따라 하나 이상의 스크린샷을 추가 해야 합니다.
  - 1280 x 720 픽셀
  - .png 파일
  - 캡션을 포함 해야 합니다.
- **미디어-비디오** (선택 사항): 제품을 설명 하는 다음과 같은 요구 사항을 사용 하 여 최대 4 개의 비디오를 추가할 수 있습니다.
  - Name
  - URL: YouTube 또는 Vimeo에만 호스팅되어야 합니다.
  - 축소판 그림: 1280 x 720 .png 파일

> [!Note]
> 제품은 일반 [상용 marketplace 인증 정책](/legal/marketplace/certification-policies#100-general) 및 소프트웨어를 상업적 marketplace에 게시 하는 [서비스 정책으로](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) 충족 해야 합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹
미리 보기 대상 사용자는 온라인 상점에 게시 하기 전에 제품에 액세스 하 여 종단 간 기능을 실시간으로 게시할 수 있습니다. **대상 미리 보기** 페이지에서 제한 된 미리 보기 대상 그룹을 정의할 수 있습니다. Microsoft를 통해 제품을 판매 하지 않고 독립적으로 트랜잭션을 처리 하도록 선택 하는 경우이 설정을 사용할 수 없습니다. 이 경우이 섹션을 건너뛰고 [추가 판매 기회](#additional-sales-opportunities)로 이동할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 개인 계획과 다릅니다. 비공개 요금제는 사용자가 선택 하는 특정 대상에만 사용할 수 있도록 하는 계획입니다. 이렇게 하면 사용자 지정 요금제를 특정 고객과 협상할 수 있습니다. 자세한 내용은 다음 섹션인 계획을 참조 하세요.

Microsoft 계정 (MSA) 또는 Azure Active Directory (Azure AD) 전자 메일 주소로 초대를 보낼 수 있습니다. 전자 메일 주소를 최대 10 개까지 수동으로 추가 하거나 .csv 파일을 사용 하 여 최대 20 개까지 가져오세요. 제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용 또는 업데이트를 테스트 하기 위한 미리 보기 대상을 계속 정의할 수 있습니다.

## <a name="plans"></a>계획

불가능 제공에는 하나 이상의 계획이 필요 합니다. 계획은 솔루션 범위와 제한 및 관련 된 가격을 정의 합니다. 제품에 대 한 여러 요금제를 만들어 고객에 게 다른 기술 및 가격 옵션을 제공할 수 있습니다. 불가능 제품을 만드는 대신 독립적으로 트랜잭션을 처리 하도록 선택 하는 경우 **계획** 페이지가 표시 되지 않습니다. 그럴 경우이 섹션을 건너뛰고 [추가 판매 기회](#additional-sales-opportunities)로 이동 합니다.

가격 책정 모델, 무료 평가판 및 비공개 요금제를 비롯 한 요금제에 대 한 일반적인 지침은 [상업적 marketplace 제품에 대 한 계획 및 가격 책정](plans-pricing.md) 을 참조 하세요. 다음 섹션에서는 SaaS 제품에만 적용 되는 추가 정보를 설명 합니다.

### <a name="saas-pricing-models"></a>SaaS 가격 책정 모델

SaaS 제품은 각 요금제를 사용 하는 두 가지 가격 책정 모델 중 _하나를 사용할_ 수 _있습니다._ 동일한 제품의 모든 요금제는 동일한 가격 책정 모델에 연결 되어 있어야 합니다. 예를 들어 제품에는 단일 요금제와 사용자 당 다른 요금제를 사용할 수 없습니다.

**Flat rate** – 월별 또는 연간 단일 정액 요금 가격으로 제품에 대 한 액세스를 허용 합니다. 이를 사이트 기반 가격 책정이라고도 합니다. 이 가격 책정 모델을 사용 하면 필요에 따라 marketplace 계량 서비스 API를 사용 하 여 고객에 게 정액 요금에 포함 되지 않은 사용량에 대 한 요금을 부과 하는 요금제 요금제를 정의할 수 있습니다. 요금제에 대 한 자세한 내용은 [상용 marketplace 계량 서비스를 사용 하 여 SaaS에 대 한 요금제 청구](./partner-center-portal/saas-metered-billing.md)를 참조 하세요. SaaS 서비스의 사용 동작이 버스트 인 경우에도이 옵션을 사용 해야 합니다.

**사용자 단위** – 제품에 액세스할 수 있는 사용자 수에 따라 가격으로 제품에 대 한 액세스를 허용 합니다. 이 사용자 기반 모델을 사용 하면 계획에서 지원 되는 사용자의 최소 및 최대 수를 설정할 수 있습니다. 여러 요금제를 만들어 사용자 수에 따라 다양 한 가격을 구성할 수 있습니다. 이러한 필드는 선택 사항입니다. 이를 선택 하지 않은 경우 사용자 수는 제한이 없는 것으로 해석 됩니다 (최소 1 개, 서비스에서 지원할 수 있는 최대 개수). 이러한 필드는 플랜 업데이트의 일부로 편집할 수 있습니다.

> [!IMPORTANT]
> 제품을 게시 한 후에는 가격 책정 모델을 변경할 수 없습니다. 또한 동일한 제품의 모든 플랜은 동일한 가격 책정 모델을 공유해야 합니다.

### <a name="saas-billing"></a>SaaS 청구

(게시자의) Azure 구독에서 실행 되는 SaaS 앱의 경우 인프라 사용이 직접 청구 됩니다. 고객에 게 실제 인프라 사용 요금은 표시 되지 않습니다. 솔루션을 실행 하기 위해 배포한 인프라의 비용을 보정 하기 위해 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격 책정에 번들 해야 합니다.

Microsoft에서 제공 하는 SaaS 앱 제품은 [요금제 서비스](./partner-center-portal/saas-metered-billing.md)를 사용 하 여 정액 요금, 사용자 당 요금 또는 소비 요금을 기준으로 월별 또는 연간 요금 청구로 판매 됩니다. 상업적 marketplace는 기관 모델에서 작동 합니다. 즉, 게시자가 가격을 설정 하 고, Microsoft 청구서 고객을 원천 하 고, Microsoft는 고객 요금을 지불 하는 동시에 고객에 게 수익

이는 에이전시 모델을 보여 주기 위해 비용 및 지급 분석의 샘플입니다. 이 예제에서 Microsoft는 소프트웨어 라이선스에 대해 고객에게 $100.00를 청구하고 게시자에게 $80.00를 지급합니다.

| 라이선스 비용 | 매월 $100 |
| ------------ | ------------- |
| Azure 사용량 비용(D1/1개 코어) | 고객이 아닌 게시자에게 직접 청구됩니다. |
| Microsoft에서 고객에게 청구하는 요금 | 월간 $100.00(게시자는 발생 또는 이전된 인프라 비용을 라이선스 요금으로 계산해야 함) |
| **Microsoft에 청구되는 요금** | **매월 $100** |
| Microsoft는 라이선스 비용의 80%를 지불합니다.<br>`*` Microsoft는 정규화 된 SaaS 앱에 대해 90%의 라이선스 비용을 지불 합니다.| 매월 $80.00<br>``*`` 매월 $90.00 |
|||

**`*` Marketplace 서비스 요금 절감** – 상업적 Marketplace에 게시 한 특정 SaaS 제품의 경우 Microsoft는 microsoft 게시자 계약에 설명 된 대로 20%에서 Marketplace 서비스 요금을 10%까지 줄일 수 있습니다. 제품을 자격이 있는 경우 Microsoft에서 귀하의 제품을 Azure IP 공동 판매 incentivized로 지정 해야 합니다. 해당 월의 Marketplace 서비스 요금을 절감 하려면 각 월이 끝날 때까지 영업일 (5) 이상의 영업일을 충족 해야 합니다. 또한 Azure IP 공동 판매 incentivized Vm, 관리 되는 앱 및 상업적 Marketplace를 통해 제공 되는 기타 정규화 된 불가능 IaaS 제품에도 적용 됩니다.

## <a name="additional-sales-opportunities"></a>추가 판매 기회

Microsoft에서 지 원하는 마케팅 및 판매 채널을 옵트인 (opt in) 하도록 선택할 수 있습니다. 파트너 센터에서 제품을 만들 때 프로세스의 끝에 두 개의 탭이 표시 됩니다.

- **Csp를 통한 재판매** :이 옵션을 사용 하면 csp (Microsoft 클라우드 솔루션 공급자) 파트너가 번들 제안의 일부로 솔루션을 재판매 수 있습니다. 자세한 내용은 [클라우드 솔루션 공급자 프로그램](cloud-solution-providers.md) 을 참조 하세요.

- **Microsoft와 공동 판매** :이 옵션을 사용 하면 microsoft 영업 팀에서 고객의 요구를 평가할 때 IP 공동 판매에 적합 한 솔루션을 고려할 수 있습니다. 평가를 위해 제품을 준비 하는 방법에 대 한 자세한 내용은 [파트너 센터의 공동 판매 옵션](./partner-center-portal/commercial-marketplace-co-sell.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [상업적 marketplace에서 SaaS 제품을 만드는 방법](create-new-saas-offer.md)
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)