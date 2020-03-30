---
title: 상업 시장에서 운영 제안을 위한 새로운 Dynamics 365 만들기
description: Microsoft 파트너 센터의 상용 마켓플레이스 포털을 사용하여 Azure 마켓플레이스, AppSource 또는 CSP(클라우드 솔루션 공급자) 프로그램을 통해 운영 용 새 Dynamics 365를 만드는 방법.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: f7c4b25dad50b8fe620d358aa7fb3e9decabc2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294349"
---
# <a name="create-a-new-dynamics-365-for-operations-offer"></a>운영 제공을 위한 새로운 Dynamics 365 만들기

이 항목에서는 운영 용 새 Dynamics 365를 만드는 방법에 대해 설명합니다. [재무 및 운영을 위한 Microsoft Dynamics 365는](https://dynamics.microsoft.com/finance-and-operations) 고급 재무, 운영, 제조 및 공급망 관리를 지원하는 ERP(전사적 자원 관리) 서비스입니다. 운영을 위한 Dynamics 365에 대한 모든 오퍼는 인증 절차를 거쳐야 합니다.

Operations 오퍼에 대한 Dynamics 365 를 만들기 시작하려면 먼저 [파트너 센터 계정을 만들고](./create-account.md) **개요** 페이지를 선택한 상업 [용 마켓플레이스 대시보드를](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)열도록 하십시오.

![파트너 센터의 상용 마켓플레이스 대시보드](./media/new-offer-overview.png)

>[!Note]
> 제안이 게시되면 파트너 센터에서 만든 오퍼에 대한 편집은 다시 게시한 후에만 시스템에서 업데이트되고 스토어 프론트에서만 업데이트됩니다. 변경한 후 게시를 위해 오퍼를 제출해야 합니다.


## <a name="create-a-new-offer"></a>새 제안 만들기

+ **새 오퍼** 버튼을 선택한 다음 작업 메뉴 항목에 **대한 Dynamics 365를** 선택합니다. **새 오퍼** 대화 상자가 나타납니다.

### <a name="offer-id-and-alias"></a>제공 ID 및 별칭

- **오퍼 ID**: 계정의 각 오퍼에 대한 고유 식별자입니다. 이 ID는 마켓플레이스 오퍼및 Azure 리소스 관리자 템플릿(해당하는 경우)의 URL 주소에 고객에게 표시됩니다. 오퍼 ID는 소문자 영숫자 문자여야 합니다(하이픈과 밑줄 포함,공백 없음). 이 ID는 50자로 제한되며 **만들기를**선택한 후에는 변경할 수 없습니다.  예를 들어 여기에 *테스트 제안-1을* 입력하면 오퍼 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`URL이 됩니다.

- **오퍼 별칭**: 파트너 센터 내의 오퍼를 참조하는 데 사용되는 이름입니다. 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 오퍼 이름 및 기타 값과 다릅니다. 이 값은 **만들기를**선택한 후에는 변경할 수 없습니다.

**쿠폰 ID** 및 **쿠폰 별칭을**입력하면 **을**선택합니다. 그러면 오퍼의 모든 다른 부분에서 작업할 수 있습니다.

## <a name="offer-setup"></a>오퍼 설정

**제안 설정** 페이지에서다음 정보를 요청합니다. 이러한 필드를 완료한 후 **저장을** 선택해야 합니다.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>잠재 고객이 이 리스팅 오퍼와 상호 작용하기를 어떻게 원하십니까?

이 오퍼에 사용할 옵션을 선택합니다.

#### <a name="get-it-now-free"></a>지금 받기 (무료)

앱에 액세스할 수 있는 유효한 *URL(http* 또는 *https로*시작)을 제공하여 고객에게 무료로 제품을 나열합니다.  예: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>무료 평가판(목록)

평가판을 받을 수 있는 유효한 *URL(http* 또는 *https로*시작)을 제공하여 무료 평가판에 대한 링크를 고객에게 나열합니다.  예: `https://contoso.com/trial/my-app` 제안 목록 무료 평가판은 서비스에서 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독이 없습니다.

> [!NOTE]
> 응용 프로그램이 평가판 링크를 통해 받게 되는 토큰은 Azure Active Directory(Azure AD)를 통해 사용자 정보를 획득하여 앱에서 계정 생성을 자동화하는 데만 사용할 수 있습니다. Microsoft 계정은 이 토큰을 사용하는 인증에 대해 지원되지 않습니다.

#### <a name="contact-me"></a>연락처

CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객은 자신의 정보를 공유 할 수있는 권한을 요청합니다. 이러한 고객 세부 정보( 오퍼 이름, ID 및 마켓플레이스 소스와 함께 쿠폰을 찾은 경우)는 구성한 CRM 시스템으로 전송됩니다. CRM 구성에 대한 자세한 내용은 [연결 잠재 고객 관리](#connect-lead-management)를 참조하십시오. 

### <a name="test-drive"></a>시험 사용

테스트 드라이브는 '구매 하기 전에 시도' 옵션을 제공 하 여 잠재 고객에 게 귀하의 제안을 선보일 수 있는 좋은 방법, 증가 전환 및 높은 자격을 갖춘 리드의 생성. [테스트 드라이브에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

테스트 드라이브를 사용하려면 **테스트 드라이브 활성화** 상자를 선택합니다. 그런 다음 고객이 일정 기간 동안 제품을 시험해 볼 수 있도록 [테스트 드라이브 기술 구성](#test-drive-technical-configuration) 구성에서 데모 환경을 구성해야 합니다.

#### <a name="type-of-test-drive"></a>시운전 유형

다음 옵션 중에서 선택합니다.

- **[Azure 리소스 관리자](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: 솔루션을 구성하는 모든 Azure 리소스를 포함하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다.
- **[비즈니스 센트럴용 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: Microsoft는 비즈니스 중앙 엔터프라이즈 리소스 계획 시스템(재무, 운영, 공급망, CRM 등)에 대한 테스트 드라이브 서비스(프로비저닝 및 배포 포함)를 호스팅하고 유지 관리합니다.  
- **[고객 참여를 위한 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: Microsoft는 고객 참여 시스템(영업, 서비스, 프로젝트 서비스, 현장 서비스 등)에 대한 테스트 드라이브 서비스(프로비저닝 및 배포 포함)를 호스팅하고 유지 관리합니다.  
- **[운영용 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: Microsoft는 재무 및 운영 엔터프라이즈 리소스 계획 시스템(재무, 운영, 제조, 공급망 등)에 대한 테스트 드라이브 서비스(프로비저닝 및 배포 포함)를 호스팅하고 유지 관리합니다. 
- **[논리 앱](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: 모든 복잡한 솔루션 아키텍처를 포함하는 배포 템플릿입니다. 모든 사용자 지정 제품은 이러한 유형의 테스트 드라이브를 사용해야 합니다.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: 사용자 지정 대시보드에 대한 임베디드 링크입니다. 대화형 Power BI 시각적 개체를 보여 주려는 제품은 이러한 유형의 테스트 드라이브를 사용해야 합니다. 포함된 Power BI URL만 업로드하면 됩니다.

#### <a name="additional-test-drive-resources"></a>추가 테스트 드라이브 리소스

- [테스트 드라이브 기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [테스트 드라이브 마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [테스트 드라이브 개요 한 호출기](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>잠재 고객 관리 연결

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [잠재 고객 관리 개요를](./commercial-marketplace-get-customer-leads.md)참조하십시오.

다음 섹션으로 이동하기 전에 **저장해야** 합니다.

## <a name="properties"></a>속성

**속성** 페이지에서는 마켓플레이스에서 오퍼를 그룹화하는 데 사용되는 카테고리 및 산업, 앱 버전 및 쿠폰을 지원하는 법적 계약을 정의할 수 있습니다. 이 페이지를 완료한 후 **저장을** 선택합니다.

### <a name="category"></a>Category

최소 범주를 1개, 최대 3개 범주를 선택합니다. 이러한 카테고리는 적절한 마켓플레이스 검색 영역에 쿠폰을 배치하는 데 사용됩니다. 쿠폰이 쿠폰 설명에서 이러한 카테고리를 지원하는 방법을 문의하세요.

### <a name="industry"></a>산업

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>앱 버전

제품의 버전 번호를 입력합니다. 고객은 쿠폰의 세부 정보 페이지에 나열된 이 버전을 볼 수 있습니다.

### <a name="terms-and-conditions"></a>사용 약관

**이용** 약관 필드에 고유한 법적 이용 약관을 제공합니다. 이용 약관을 찾을 수 있는 URL을 제공할 수도 있습니다. 고객은 제품을 사용해 본 전에 이 약관에 동의해야 합니다.

## <a name="offer-listing"></a>오퍼 리스팅

쿠폰 목록 페이지에는 쿠폰이 나열되는 언어가 표시됩니다. 현재 **영어(미국)가** 유일한 옵션입니다.

각 언어/시장에 대한 마켓플레이스 세부 정보(오퍼 이름, 설명, 이미지 등)를 정의해야 합니다. 이 정보를 제공하려면 언어/시장 이름을 선택합니다.

> [!NOTE]
> 오퍼 리스팅 콘텐츠(예: 설명, 문서, 스크린샷, 이용 약관 등)는 "이 응용 프로그램은 [영어 이외의 언어]에서만 사용할 수 있습니다"라는 문구로 시작되는 한 영어로 작성할 필요가 없습니다. 또한 오퍼 리스팅 콘텐츠에 사용된 언어 이외의 언어로 콘텐츠를 제공하는 *유용한 링크 URL을* 제공하는 것도 허용됩니다.

### <a name="name"></a>이름

여기에 입력한 이름은 고객에게 쿠폰 목록의 제목으로 표시됩니다. 이 필드는 **오퍼 별칭에** 대해 입력한 텍스트로 미리 채워지지만 이 값을 변경할 수 있습니다. 이 이름은 상표가 지정될 수 있으며 상표 또는 저작권 기호가 포함될 수 있습니다. 이름은 50자를 초과할 수 없으며 이모티콘을 포함할 수 없습니다.

### <a name="short-description"></a>간단한 설명

오퍼에 대한 간략한 설명(최대 100자)을 제공합니다. 이 설명은 마켓플레이스 검색 결과에 사용될 수 있습니다.

### <a name="description"></a>설명

오퍼에 대한 더 긴 설명을 제공합니다(최대 3,000자). 이 설명은 마켓플레이스 목록 개요의 고객에게 표시됩니다. 오퍼의 가치 제안, 주요 혜택, 카테고리 및/또는 산업 협회, 인앱 구매 기회 및 필요한 공개를 포함합니다.

설명을 작성하기 위한 몇 가지 팁:  

- 설명의 처음 몇 문장에서 제품의 가치 제안을 명확하게 설명합니다. 가치 제안에 다음 정보를 포함하십시오.
  - 제품 설명
  - 제품의 이점을 누릴 수 있는 사용자 유형
  - 제품이 해결하는 고객의 요구 또는 고통
- 처음 몇 문장은 검색 엔진 결과에 표시될 수 있다는 점에 유의하세요.  
- 제품을 판매하는 특징과 기능에 의존하지 않습니다. 대신, 전달하는 가치에 중점을 둡니다.  
- 산업별 어휘 또는 혜택 기반 단어를 최대한 많이 사용합니다.
- HTML 태그를 사용하여 설명의 서식을 지정하고 더 매력적으로 만드는 것이 좋습니다.

쿠폰 설명을 더욱 매력적으로 만들려면 고급 텍스트 편집기를 사용하여 설명의 서식을 지정합니다.

![리치 텍스트 편집기 사용](./media/text-editor2.png)

다음 지침을 사용하여 다음 지침을 사용하여 이 하위 텍스트 편집기를 사용합니다.

- 콘텐츠 의 형식을 변경하려면 아래와 같이 서식을 지정하고 텍스트 스타일을 선택할 텍스트를 강조 표시합니다.

     ![진부한 텍스트 편집기를 사용하여 텍스트 형식 변경](./media/text-editor3.png)

- 글머리 기호 또는 번호가 매겨진 목록을 텍스트에 추가하려면 아래 옵션을 사용하십시오.

     ![목록을 추가 하려면 하위 텍스트 편집기를 사용 하 여](./media/text-editor4.png)

- 텍스트에 들여쓰기를 추가하거나 제거하려면 아래 옵션을 사용하십시오.

     ![리치 텍스트 편집기를 사용하여 들여쓰기](./media/text-editor5.png)

### <a name="search-keywords"></a>검색 키워드

선택적으로 최대 3개의 검색 키워드를 입력하여 고객이 마켓플레이스에서 쿠폰을 찾을 수 있도록 도와줍니다. 최상의 결과를 얻으려면 설명에 이러한 키워드를 사용해 보세요.

### <a name="products-your-app-works-with"></a>앱과 연동되는 제품

고객에게 앱이 특정 제품에서 작동한다는 것을 알리려면 여기에 최대 3개의 제품 이름을 입력하세요.

### <a name="support-urls"></a>지원 URL

이 섹션에서는 고객이 쿠폰에 대해 더 많이 이해할 수 있도록 링크를 제공할 수 있습니다.

#### <a name="help-link"></a>도움말 링크

고객이 쿠폰에 대해 자세히 알아볼 수 있는 URL을 입력합니다.

#### <a name="privacy-policy-url"></a>개인정보 처리방침 URL

조직의 개인 정보 보호 정책에 대한 URL을 입력합니다. 귀하는 앱이 개인 정보 보호 법률 및 규정을 준수하는지 보장하고 유효한 개인 정보 보호 정책을 제공할 책임이 있습니다.

### <a name="contacts"></a>연락처

이 섹션에서는 **지원 연락처** 및 엔지니어링 **연락처의**이름, 전자 메일 및 전화 번호를 제공합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에게 제공될 수 있습니다.

지원 **연락처** 섹션에서 CSP 파트너가 오퍼에 대한 지원을 찾을 수 있는 **지원 URL을** 제공합니다.

### <a name="supporting-documents"></a>서류 지원

백서, 브로셔, 체크리스트 또는 프레젠테이션과 같은 관련 마케팅 문서를 하나 이상 (최대 3개) 여기에 제공합니다. 이러한 문서는 .pdf 형식이어야 합니다.

### <a name="marketplace-images"></a>마켓플레이스 이미지

이 섹션에서는 고객에게 제안을 표시할 때 사용할 로고와 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식이어야 합니다.

#### <a name="store-logos"></a>스토어 로고

**스몰(48 x 48)** 및 **대형(216 x 216)의**두 가지 크기로 오퍼의 로고를 제공합니다.

#### <a name="hero"></a>Hero

영웅 이미지는 선택 사항입니다. 하나를 제공하는 경우 815 x 290 픽셀을 측정해야합니다.

#### <a name="screenshots"></a>스크린샷

쿠폰의 작동 방식을 보여 주는 스크린샷을 추가합니다. 스크린샷이 하나 이상 필요하며 최대 5개까지 추가할 수 있습니다. 모든 스크린샷은 1280 x 720 픽셀이어야 합니다.

#### <a name="videos"></a>비디오

선택적으로 쿠폰을 보여주는 동영상을 최대 4개까지 추가할 수 있습니다. 이 동영상은 YouTube 및/또는 Vimeo에서 호스팅되어야 합니다. 각 동영상에 대해 동영상 이름, URL 및 동영상의 축소판 이미지(1280 x 720픽셀)를 입력합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 리스팅 리소스

- [마켓플레이스 오퍼 리스팅에 대한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>가용성

**가용성** 페이지에서는 쿠폰을 사용할 수 있도록 하는 위치와 방법에 대한 옵션을 제공합니다.

### <a name="markets"></a>시장

이 섹션에서는 쿠폰을 사용할 수 있는 시장을 지정할 수 있습니다. 이렇게 하려면 **시장 선택** 팝업 창을 표시하는 **시장 편집을** 선택합니다.

기본적으로 시장이 선택되지 않습니다. 쿠폰을 게시할 마켓을 하나 이상 선택합니다. 모든 가능한 시장에서 쿠폰을 사용할 수 있도록 **모두 선택을** 클릭하거나 추가할 특정 시장을 선택합니다. 완료되면 **저장을**선택합니다.

여기에서 선택한 항목은 새로운 인수에만 적용됩니다. 누군가가 이미 특정 시장에 앱을 보유하고 있고 나중에 해당 시장을 제거하는 경우 해당 시장에서 이미 쿠폰을 받은 사용자는 계속 사용할 수 있지만 해당 시장에서 새로운 고객은 쿠폰을 받을 수 없습니다.

> [!IMPORTANT]
> 이러한 요구 사항이 여기에 나열되지 않은 경우에도 파트너 센터에 현지 법적 요구 사항을 충족하는 것은 사용자의 책임입니다.

모든 시장, 현지 법률 및 제한 사항 또는 기타 요인으로 인해 일부 국가 및 지역에서 특정 오퍼가 나열되지 않을 수 있습니다.

### <a name="preview-audience"></a>잠재고객 미리 보기

더 광범위한 마켓플레이스 오퍼에 실시간으로 쿠폰을 게시하기 전에 먼저 제한된 **미리 보기 대상에게**쿠폰을 제공해야 합니다. 여기에 **숨기기** 키(소문자 및/또는 숫자만 사용하는 모든 문자열)를 입력합니다. 미리 보기 대상의 구성원은 이 숨기기 키를 토큰으로 사용하여 마켓플레이스에서 쿠폰 의 미리 보기를 볼 수 있습니다.

그런 다음 쿠폰을 사용할 수 있도록 하고 미리 보기 제한을 제거할 준비가 되면 **숨기기 키를** 제거하고 다시 게시해야 합니다.

## <a name="technical-configuration"></a>기술 구성

**기술 구성** 페이지에서는 오퍼에 연결하는 데 사용되는 기술 세부 정보를 정의합니다. 이 연결을 통해 최종 고객이 제품을 구매하기로 선택한 경우 귀하의 제안을 프로비전할 수 있습니다.

### <a name="solution-identifier"></a>솔루션 식별자

솔루션에 대한 솔루션 식별자(GUID)를 제공합니다.

솔루션 식별자를 찾으려면 다음을 수행하십시오.
1. Microsoft 역학 수명 주기 서비스(LCS)에서 **솔루션 관리를 선택합니다.**
2. 솔루션을 선택한 다음 패키지 개요에서 **솔루션 식별자를** **찾습니다.** 식별자가 비어 있으면 패키지 **편집** 및 다시 게시를 선택한 다음 다시 시도하십시오.

### <a name="release-version"></a>릴리스 버전

이 솔루션에서 작동하는 재무 및 운영용 Dynamics 365 버전을 선택합니다.

## <a name="test-drive-technical-configuration"></a>테스트 드라이브 기술 구성

[쿠폰 설정](#offer-setup) 페이지에서 테스트 **드라이브 활성화를** 선택한 경우 고객이 제품의 테스트 드라이브를 경험할 수 있도록 여기에 세부 정보를 제공해야 합니다.

**테스트 드라이브** 페이지에서는 데모(또는 "테스트 드라이브")를 설정하여 고객이 제품을 구매하기 전에 제품을 시험해 볼 수 있도록 합니다. 이 기사에서 자세히 알아보기 [테스트 드라이브란 무엇입니까?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). 오퍼에 대한 테스트 드라이브를 더 이상 제공하지 않으려면 **[제안 설정](#offer-setup)** 페이지로 돌아가서 **테스트 드라이브 사용 활성화를**선택 취소합니다.

다음과 같은 유형의 테스트 드라이브를 사용할 수 있으며 각 드라이브에는 고유한 기술 구성 요구 사항이 있습니다.

- [Azure 리소스 관리자](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [논리 앱](#technical-configuration-for-logic-app-test-drive)
- [전원 BI(기술](#technical-configuration-not-required-for-power-bi-test-drives) 구성이 필요하지 않음)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 리소스 관리자 테스트 드라이브에 대한 기술 구성

솔루션을 구성하는 모든 Azure 리소스를 포함하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다. Azure 리소스 관리자 [테스트 드라이브](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)설정에 대해 자세히 알아봅니다.

- **지역(필수):** 현재 테스트 드라이브를 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. 일반적으로 가장 많은 고객이 예상되는 지역에서 테스트 드라이브를 사용할 수 있도록 하여 가장 좋은 성능을 위해 가장 가까운 지역을 선택할 수 있도록 해야 합니다. 구독이 선택한 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인해야 합니다.

- **인스턴스:** 사용 가능한 인스턴스 유형(핫 또는 콜드) 및 사용 가능한 인스턴스 수를 선택하여 쿠폰을 사용할 수 있는 지역 수를 곱합니다.

**핫**: 이 유형의 인스턴스가 배포되고 선택한 지역당 액세스를 기다리고 있습니다. 고객은 배포를 기다리지 않고 테스트 드라이브의 *Hot* 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객은 전체 배포를 기다리지 않으려므로 *핫* *인스턴스를* 사용할 수 없는 경우 고객 사용량이 중단되는 것을 매우 권장합니다.

**Cold**: 이 유형의 인스턴스는 지역별 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 테스트 드라이브를 요청할 때 배포하기 위해 전체 테스트 드라이브 리소스 관리자 템플릿을 사용해야 하므로 *콜드* 인스턴스는 *Hot* 인스턴스보다 로드 속도가 훨씬 느립니다. 장단점은 테스트 드라이브 기간 동안만 비용을 지불하면 *핫* 인스턴스와 마찬가지로 Azure 구독에서 항상 실행되는 *것은 아닙니다.*

- **테스트 드라이브 Azure 리소스 관리자 템플릿:** Azure 리소스 관리자 템플릿이 포함된 .zip을 업로드합니다.  빠른 시작 문서에서 Azure 리소스 관리자 템플릿 만들기 [및 Azure 포털을 사용 하 여 Azure 리소스 관리자 템플릿 을 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)하는 방법에 대해 자세히 알아봅니다.

- **테스트 드라이브** 기간(필수): 테스트 드라이브가 활성 상태로 유지되는 시간을 # 시간으로 입력합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 이 기간은 전체 시간(예: "2" 시간)에 의해서만 설정할 수 있습니다. "1.5"가 잘못되었습니다).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 테스트 드라이브를 위한 기술 구성

Microsoft는 이러한 유형의 테스트 드라이브를 사용하여 서비스 프로비저닝 및 배포를 호스팅하고 유지 관리하여 테스트 드라이브를 설정하는 복잡성을 제거할 수 있습니다. 이러한 유형의 호스팅된 테스트 드라이브에 대한 구성은 테스트 드라이브가 비즈니스 센트럴, 고객 참여 또는 운영 대상을 대상으로 하는지 여부에 관계없이 동일합니다.

- **최대 동시 테스트** 드라이브(필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정합니다. 각 동시 사용자는 테스트 드라이브가 활성화되어 있는 동안 Dynamics 365 라이선스를 사용하므로 최대 제한 집합을 지원할 수 있는 충분한 라이센스가 있는지 확인해야 합니다. 권장 값은 3~5입니다.

- **테스트 드라이브** 기간(필수): 시간 수를 정의하여 테스트 드라이브가 활성 상태로 유지되는 시간을 입력합니다. 이 많은 시간이 지나면 세션이 종료되고 더 이상 라이선스 중 하나를 사용하지 않습니다. 제품의 복잡성에 따라 2~24시간 의 가치를 제공하는 것이 좋습니다. 이 기간은 전체 시간(예: "2" 시간)에 의해서만 설정할 수 있습니다. "1.5"가 잘못되었습니다).  시간이 부족하고 테스트 드라이브에 다시 액세스하려는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스** URL(필수): 고객이 테스트 드라이브를 시작할 URL입니다. 일반적으로 샘플 데이터가 설치된 앱을 실행하는 Dynamics 365 인스턴스의 `https://testdrive.crm.dynamics.com`URL입니다(예: ).

- **인스턴스 웹 API** URL(필수): Microsoft 365 계정에 로그인하고 **설정** \&gt으로 이동하여 Dynamics 365 인스턴스에 대한 웹 API URL을 검색합니다. **사용자 정의** \&GT; **개발자 리소스** \&gt; **인스턴스 웹 API(서비스 루트 URL)**- 여기에 있는 `https://testdrive.crm.dynamics.com/api/data/v9.0`URL을 복사합니다(예: ).

- **역할** 이름(필수): 사용자 지정 Dynamics 365 테스트 드라이브에 정의한 보안 역할 이름을 제공합니다. 이는 테스트 드라이브(예: 테스트 드라이브 역할)동안 사용자에게 할당됩니다.

### <a name="technical-configuration-for-logic-app-test-drive"></a>로직 앱 테스트 드라이브를 위한 기술 구성

모든 사용자 지정 제품은 다양한 복잡한 솔루션 아키텍처를 포함하는 이러한 유형의 테스트 드라이브 배포 템플릿을 사용해야 합니다. 로직 앱 테스트 드라이브 설정에 대한 자세한 내용은 GitHub의 [운영](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 참여를](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) 참조하십시오.

- **지역(필수,** 단일 선택 드롭다운 목록): 현재 테스트 드라이브를 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. Logic 앱의 리소스는 선택한 지역에 배포됩니다. Logic App에 특정 지역에 저장된 사용자 지정 리소스가 있는 경우 해당 지역이 여기에서 선택되어 있는지 확인합니다. 해당 지역에 대한 사용자 지정 리소스를 사용할 수 있도록 하는 가장 좋은 방법은 포털의 Azure 구독에 Logic App을 로컬로 완전히 배포하고 이 옵션을 선택하기 전에 제대로 작동하는지 확인하는 것입니다.

- **최대 동시 테스트** 드라이브(필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정합니다. 이러한 테스트 드라이브는 이미 배포되어 있으므로 고객은 배포를 기다리지 않고도 즉시 액세스할 수 있습니다.

- **테스트 드라이브** 기간(필수): 테스트 드라이브가 활성 상태로 유지되는 시간을 # 시간으로 입력합니다. 이 기간이 끝나면 테스트 드라이브가 자동으로 종료됩니다.

- **Azure 리소스 그룹** 이름(필수): Logic App 테스트 드라이브가 저장된 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) 이름을 입력합니다.

- **Azure 논리 앱** 이름(필수): 사용자에게 테스트 드라이브를 할당하는 논리 앱의 이름을 입력합니다. 이 논리 응용 프로그램은 위의 Azure 리소스 그룹에 저장 해야 합니다.

- **프로비저닝 해제 논리 앱** 이름(필수): 고객이 완료되면 테스트 드라이브를 프로비저닝해제하는 Logic 앱의 이름을 입력합니다. 이 논리 응용 프로그램은 위의 Azure 리소스 그룹에 저장 해야 합니다.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 테스트 드라이브에 기술 구성이 필요하지 않습니다.

대화형 Power BI 시각적 개체를 보여 주려는 제품은 임베디드 링크를 사용하여 사용자 지정 대시보드를 테스트 드라이브로 공유할 수 있으며 추가 기술 구성이 필요하지 않습니다. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 템플릿 앱 설정에 대해 자세히 알아보세요.

### <a name="deployment-subscription-details"></a>배포 구독 세부 정보

대신 테스트 드라이브를 배포하려면 별도의 고유한 Azure 구독을 만들고 제공합니다. (Power BI 테스트 드라이브는 필요하지 않습니다).

- **Azure 구독 ID(Azure** 리소스 관리자 및 논리 앱에 필요): 구독 ID를 입력하여 리소스 사용량 보고 및 청구에 대한 Azure 계정 서비스에 대한 액세스 권한을 부여합니다. 아직 테스트 드라이브에 사용할 [별도의 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 것이 좋습니다. [Azure 포털에](https://portal.azure.com/) 로그인하고 왼쪽 메뉴의 구독 탭으로 이동하여 Azure 구독 **ID를** 찾을 수 있습니다. 탭을 선택하면 구독 ID가 표시됩니다(예: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD 테넌트** ID(필수): Azure Active Directory(AD) [테넌트 ID를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)입력합니다. 이 ID를 찾으려면 [Azure 포털에](https://portal.azure.com/)로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택하고 **속성을**선택한 다음 나열된 **디렉터리 ID** 번호(예: 50c464d3-4930-493c-963c-1e951d15360e)를 찾습니다. 도메인 이름 URL을 사용하여 조직의 테넌트 ID를 [https://www.whatismytenantid.com](https://www.whatismytenantid.com)조회할 수도 있습니다.

- **Azure AD 테넌트 이름(동적** 365에 필요): Azure Active Directory(AD) 이름을 입력합니다. 이 이름을 찾으려면 오른쪽 상단 모서리에 있는 [Azure Portal에](https://portal.azure.com/)로그인하면 테넌트 이름이 계정 이름 아래에 나열됩니다.

- **Azure AD 앱** ID(필수): Azure Active Directory(AD) [응용 프로그램 ID를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)입력합니다. 이 ID를 찾으려면 [Azure 포털에](https://portal.azure.com/)로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택하고 **앱 등록을**선택한 다음 나열된 **응용 프로그램 ID** 번호를 찾습니다(예: 50c464d3-4930-493c-963c-1e951d15360e).

- **Azure AD 앱 클라이언트** 보안(필수): Azure AD 응용 프로그램 [클라이언트 보안](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)을 입력합니다. 이 값을 찾으려면 Azure [포털에](https://portal.azure.com/)로그인합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택하고 **앱 등록을**선택한 다음 테스트 드라이브 앱을 선택합니다. 그런 다음 **인증서 및 비밀을**선택하고 새 클라이언트 **비밀을**선택하고 설명을 입력하고 **만료**되지 **않는 것을** 선택한 다음 **추가를**선택합니다. 값을 복사해야 합니다. (값을 기록하기 전에 페이지에서 멀리 이동하지 마십시오. 또는 값에 대한 액세스 권한이 없습니다.)

다음 섹션으로 이동하기 전에 **저장해야** 합니다!

### <a name="test-drive-marketplace-listings"></a>테스트 드라이브 마켓플레이스 목록

**테스트 드라이브** 탭 아래에 있는 **마켓플레이스 목록** 옵션에는 테스트 드라이브를 사용할 수 있는 언어가 표시됩니다. 현재 **영어(미국)가** 유일한 위치입니다. 테스트 드라이브 환경을 설명하는 정보를 입력하려면 언어 이름을 선택합니다.

- **설명(필수):** 테스트 드라이브, 시연할 목표, 사용자가 실험할 목표, 탐색할 기능 및 사용자가 오퍼를 획득할지 여부를 결정하는 데 도움이 되는 관련 정보를 설명합니다. 이 필드에는 최대 3,000자의 텍스트를 입력할 수 있습니다.

- **액세스 정보(Azure** 리소스 관리자 및 논리 테스트 드라이브에 필요): 이 테스트 드라이브에 액세스하고 사용하기 위해 고객이 알아야 할 사항을 설명합니다. 오퍼를 사용하기 위한 시나리오와 고객이 테스트 드라이브 전체에서 기능에 액세스하기 위해 알아야 할 사항을 정확히 알아보십시오. 이 필드에는 최대 10,000자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서(필수):** 테스트 드라이브 환경을 심층분석해 보십시오. 사용자 설명서는 고객이 테스트 드라이브를 경험하면서 얻을 수 있는 것을 정확히 다루어야 하며, 고객이 가질 수 있는 질문에 대한 참조 역할을 해야 합니다. 파일은 PDF 형식으로 되어 있어야 하며 업로드 후 최대 255자(최대 255자)의 이름을 지정해야 합니다.

- **동영상 추가(선택** 사항): 동영상은 YouTube 또는 Vimeo에 업로드하고 여기에 링크 및 썸네일 이미지(533 x 324 픽셀)를 참조하여 고객이 테스트 드라이브를 더 잘 이해할 수 있도록 하여 제품의 기능을 성공적으로 사용하고 혜택을 강조하는 시나리오를 이해하는 방법을 포함하여 테스트 드라이브를 더 잘 이해할 수 있도록 할 수 있습니다.
  - **이름(필수)**
  - **URL(유튜브 또는 비메오만 해당)** (필수)
  - **썸네일 (533 x 324px)**: 이미지 파일은 PNG 형식이어야 합니다.

## <a name="supplemental-content"></a>추가 콘텐츠

이 페이지에서는 오퍼의 유효성을 확인하는 데 도움이 되는 쿠폰에 대한 추가 정보를 제공할 수 있습니다. 이 정보는 고객에게 표시되지 않거나 마켓플레이스에 게시되지 않습니다.

### <a name="validation-assets"></a>유효성 검사 자산

이 섹션에서 [사용자 지정 분석 보고서(CAR)를](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) 업로드합니다. 이 보고서는 미리 정의된 모범 사례 규칙 집합을 기반으로 사용자 지정 및 확장 모델을 분석하여 생성됩니다.

이 파일은 .xls 또는 .xlsx 형식이어야 합니다. 보고서가 두 개 이상인 경우 모든 보고서가 포함된 .zip 파일을 업로드할 수 있습니다.

### <a name="does-solution-include-localizations"></a>솔루션에 지역화가 포함되어 있습니까?

솔루션에서 현지 표준 및 정책을 사용할 수 있는 경우 **예를** 들며(예: 다른 국가/지역에서 요구하는 다른 급여 규칙을 수용하는 경우)를 선택합니다. 그렇지 않은 경우 **아니요**를 선택합니다.

### <a name="does-solution-enable-translations"></a>솔루션에서 번역을 사용할 수 있습니까?

솔루션의 텍스트를 다른 언어로 번역할 수 있는 경우 **예라고** 대답합니다. 그렇지 않은 경우 **아니요**를 선택합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기에 제안 제출

오퍼의 모든 필수 섹션을 완료한 후 포털의 오른쪽 상단 모서리에 **게시를** 선택합니다. **검토 및 게시** 페이지로 리디렉션됩니다.

이 제안을 처음 게시하는 경우 다음을 수행할 수 있습니다.

- 오퍼의 각 섹션에 대한 완료 상태를 참조하십시오.
    - *시작되지 않음* - 섹션이 만지지 않았고 완료되어야 한다는 것을 의미합니다.
    - *불완전* - 섹션에 수정해야 하거나 더 많은 정보를 제공해야 하는 오류가 있음을 의미합니다. 섹션으로 돌아가서 업데이트합니다.
    - *완료* - 섹션이 완료되고 필요한 모든 데이터가 제공되었으며 오류가 없음을 의미합니다. 오퍼의 모든 섹션은 제안을 제출하기 전에 완전한 상태여야 합니다.
- 인증 **용 메모** 섹션에서 인증 팀에 테스트 지침을 제공하여 앱을 이해하는 데 도움이 되는 추가 메모 와 함께 앱이 올바르게 테스트되었는지 확인합니다.
- 제출을 선택하여 게시 제안을 **제출합니다.** 제안의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일을 보내드립니다. 파트너 센터로 돌아가서 쿠폰을 공개(또는 비공개 오퍼의 경우 개인 청중에게 게시할 수 있도록 **Go-live)를** 선택합니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
