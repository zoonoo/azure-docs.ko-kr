---
title: 상업용 Marketplace에서 Dynamics 365 for Operations 제품 만들기
description: Microsoft 파트너 센터의 상업용 Marketplace 포털을 사용하는 CSP(클라우드 솔루션 공급자) 프로그램을 통하거나 Azure Marketplace, AppSource에서 나열 또는 판매하기 위해 새 Dynamics 365 for Operations 제품을 만드는 방법입니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 13e05a8771be162ebe37cc79fc93cfa404183d1d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846838"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 제품 만들기

이 항목에서는 Dynamics 365 for Operations 제품을 만드는 방법에 대해 설명합니다. [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations)는 고급 재무, 운영, 제조 및 공급망 관리를 지원하는 ERP(전사적 자원 관리)입니다. Dynamics 365 for Operations에 대한 모든 제품은 인증 프로세스를 통과해야 합니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만듭니다](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)(아직 만들지 않은 경우). 계정이 상업용 Marketplace 프로그램에 등록되어 있는지 확인합니다.

>[!NOTE]
> 제품이 게시되면 파트너 센터에서 만든 제품의 편집 내용은 시스템에만 업데이트되고 다시 게시된 후에 저장됩니다. 제품을 변경한 후 게시하기 위해 제출해야 합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요**를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Dynamics 365 for Operations**를 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-dynamics-365-ops.png)

> [!NOTE]
> 제품을 게시한 후 파트너 센터에서 해당 제품에 대해 편집한 내용은 제품을 다시 게시해야 상점에 표시됩니다. 변경한 후에는 항상 다시 게시해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Marketplace 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며 50자로 제한됩니다. 예를 들어 여기에 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력합니다. 파트너 센터의 제품에 사용되는 이름입니다.

- 이 이름은 Marketplace에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.

제품을 생성하고 계속하려면 **만들기**를 선택합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>잠재 고객이 이 제품 목록과 상호 작용하게 하려면 어떻게 해야 하나요?

이 제품에 사용할 옵션을 선택합니다.

#### <a name="get-it-now-free"></a>지금 받기(무료)

앱에 액세스할 수 있는 올바른 URL(*http* 또는 *https*로 시작)을 제공하여 고객에게 제품을 무료로 제공합니다.  예를 들어 `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>평가판(목록)

평가판을 받을 수 있는 올바른 URL(*http* 또는 *https*로 시작)을 제공하여 평가판에 대한 링크를 통해 고객에게 제품을 나열합니다. `https://contoso.com/trial/my-app`)을 입력합니다. 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다.

> [!NOTE]
> 평가판 링크를 통해 애플리케이션에서 수신하는 토큰은 앱에서 자동으로 계정을 만드는 Azure AD(Active Directory)를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용하는 인증에 Microsoft 계정이 지원되지 않습니다.

#### <a name="contact-me"></a>연락처

CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

### <a name="test-drive"></a>시험 사용

시험 사용은 "구입 전 체험" 옵션을 제공하여 잠재적인 고객에게 제안을 선보임으로써 구매 전환율을 높이고 적절한 잠재 고객을 창출할 수 있는 유용한 방법입니다. [시험 사용에 대한 자세한 정보](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

시험 사용을 고정된 시간 동안 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란 선택을 취소합니다. 이 항목 뒤에 나오는 [시험 사용 기술 구성](#test-drive-technical-configuration) 섹션에서 시험 사용 환경을 구성합니다.

자세한 내용은 [상업용 Marketplace에서 제품 시험 사용](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)을 참조하세요.

#### <a name="type-of-test-drive"></a>시험 사용 유형

다음 옵션 중에서 선택합니다.

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – 솔루션을 구성하는 모든 Azure 리소스를 포함하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft가 회사 중앙 엔터프라이즈 리소스 계획 시스템(재무, 운영, 공급망, CRM 등)에 대해 시험 사용 서비스(프로비저닝 및 배포 포함)를 호스팅하고 유지 관리합니다.  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – Microsoft에서 고객 참여 시스템(판매, 서비스, 프로젝트 서비스, 현장 서비스 등)에 대한 시험 사용 서비스(프로비저닝 및 배포 포함)를 호스팅하고 유지 관리합니다.  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – Microsoft는 재무 및 운영 엔터프라이즈 리소스 계획 시스템(재무, 운영, 제조, 공급망 등)에 대해 시험 사용 서비스(프로비저닝 및 배포 포함)를 호스팅하고 유지 관리합니다.
- **[논리 앱](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – 모든 복잡한 솔루션 아키텍처를 포괄하는 배포 템플릿입니다. 모든 사용자 지정 제품은 이 유형의 시험 사용을 사용해야 합니다.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – 사용자 지정 빌드 대시보드에 포함된 링크입니다. 대화형 Power BI 시각적 개체를 보여 주려는 제품은 이 유형의 시험 사용을 사용해야 합니다. 포함된 Power BI URL만 업로드하면 됩니다.

#### <a name="additional-test-drive-resources"></a>추가 시험 사용 리소스

- [마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF. 팝업 차단이 해제되어 있는지 확인)

### <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [잠재 고객 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조하세요.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 Marketplace에서 제품을 그룹화하는 데 사용되는 범주와 산업, 앱 버전 및 제품을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="category"></a>Category

최소 1개에서 최대 3개의 범주를 선택합니다. 이러한 범주는 제품을 적절한 Marketplace 검색 영역에 추가하는 데 사용됩니다. 제품 설명에서 제품이 이러한 범주를 어떻게 지원하는지 확인해야 합니다.

### <a name="industry"></a>산업

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>앱 버전

제품의 버전 번호를 입력합니다. 이 버전은 제품의 세부 정보 페이지에서 표시됩니다.

### <a name="terms-and-conditions"></a>사용 약관

**사용 약관** 필드에 사용자의 법적 조건을 제공합니다. 사용 약관을 찾을 수 있는 URL을 제공할 수 있습니다. 고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

이 페이지에는 제품에서 나열되는 언어가 표시됩니다. 현재 **영어(미국)** 가 유일하게 사용할 수 있는 옵션입니다.

각 언어/시장에서 Marketplace 세부 정보(제품 이름, 설명, 이미지 등)를 정의해야 합니다. 이 정보를 제공하려면 언어/시장 이름을 선택합니다.

> [!NOTE]
> 제품 설명(예: 설명, 문서, 스크린샷, 사용 약관 등)이 "[영어가 아닌 언어]에서만 사용할 수 있습니다."로 시작하는 경우 제품 세부 정보는 영어로 작성되지 않아도 됩니다. 또한 제품 목록 콘텐츠에 사용된 것과 다른 언어로 콘텐츠를 제공하는 *유용한 링크 URL*을 제공할 수 있습니다.

### <a name="name"></a>속성

여기에 입력하는 이름은 고객에게 제품 목록의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭**에 입력한 텍스트로 미리 채워져 있지만 이 값을 변경할 수 있습니다. 이 이름은 상표로 등록할 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50자를 초과할 수 없으며 모든 이모지를 포함할 수 없습니다.

### <a name="short-description"></a>간단한 설명

제품에 대한 간단한 설명을 최대 100자까지 제공합니다. 이 설명은 Marketplace 검색 결과에 사용됩니다.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>검색 키워드

필요에 따라 고객이 Marketplace에서 제품을 찾을 수 있도록 최대 3개의 검색 키워드를 입력할 수 있습니다. 최상의 결과를 얻으려면 설명에 이러한 키워드를 사용해 보세요.

### <a name="products-your-app-works-with"></a>앱과 연동되는 제품

고객에게 앱이 특정 제품과 호환된다는 것을 알리려면 여기에 최대 3개의 제품 이름을 입력합니다.

### <a name="support-urls"></a>지원 URL

이 섹션에서 고객이 제품을 이해하는 데 도움이 되는 링크를 제공할 수 있습니다.

#### <a name="help-link"></a>도움말 링크

고객이 제품에 대해 자세히 알아볼 수 있는 URL을 입력합니다.

#### <a name="privacy-policy-url"></a>개인정보처리방침 URL

조직의 개인정보처리방침 URL을 입력합니다. 앱이 개인 정보 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공해야 합니다.

### <a name="contacts"></a>연락처

이 섹션에서는 **지원 연락처** 및 **엔지니어링 연락처**의 이름, 이메일 및 전화번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에게 제공될 수 있습니다.

**지원 연락처** 섹션에서 CSP 파트너가 제품에 대한 지원을 찾을 수 있는 **지원 URL**을 제공합니다.

### <a name="supporting-documents"></a>지원 문서

백서, 브로슈어, 검사 목록, 프레젠테이션 등의 관련 마케팅 문서를 하나 이상(3개까지) 제공합니다. 이러한 문서는 PDF 형식이어야 합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

이 섹션에서 제품을 고객에게 표시할 때 사용되는 로고 및 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식이어야 합니다.

>[!Note]
>파일 업로드에 문제가 발생한 경우 로컬 네트워크에서 파트너 센터의 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인합니다.

#### <a name="store-logos"></a>스토어 로고

두 픽셀 크기의 제품 로고를 제공합니다.

- **작음**(48 x 48)
- **큼**(216 x 216)

#### <a name="hero"></a>Hero

대표 이미지는 선택 사항입니다. 제공하는 경우 815 x 290픽셀이어야 합니다.

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 스크린샷을 추가합니다. 스크린샷은 하나 이상 필요하며 최대 5 개까지 추가할 수 있습니다. 모든 스크린샷은 1280 x 720픽셀이어야 합니다.

#### <a name="videos"></a>동영상

필요에 따라 제품을 설명하는 비디오를 4개까지 추가할 수 있습니다. 이러한 비디오는 YouTube 및/또는 Vimeo에서 호스팅되어야 합니다. 각 항목에 대해 비디오 이름, 해당 URL 및 비디오의 썸네일 이미지(1280 x 720픽셀)를 입력합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열하는 추가 Marketplace

[Marketplace 제품 목록에 대한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="availability"></a>가용성

이 페이지에서는 제품을 사용할 수 있게 하는 위치 및 방법에 대한 옵션을 제공합니다.

### <a name="markets"></a>시장

이 섹션에서 제품을 사용할 수 있는 지역/국가를 지정할 수 있습니다. 이렇게 하려면 **지역/국가 선택** 팝업 창이 표시되는 **지역/국가 편집**을 선택합니다.

기본적으로 시장은 선택되어 있지 않습니다. 제품을 게시할 지역/국가를 하나 이상 선택합니다. 가능한 모든 지역/국가에서 제품을 사용할 수 있도록 하려면 **모두 선택**을 클릭하거나 추가하려는 특정 지역/국가를 선택합니다. 완료되면 **저장**을 선택합니다.

여기에서 선택한 항목은 새로운 합병에만 적용됩니다. 사용자가 특정 지역/국가에 앱을 이미 보유하고 있으며 나중에 해당 지역/국가를 제거하는 경우 해당 지역/국가에서 제품이 이미 있는 사용자는 계속 사용할 수 있지만 해당 지역/국가의 새로운 고객이 제품을 얻을 수는 없습니다.

> [!IMPORTANT]
> 이러한 요구 사항이 여기 또는 파트너 센터에 나열되지 않은 경우에도 로컬 법적 요구 사항을 충족하는 것은 사용자의 책임입니다.

모든 지역/국가를 선택하는 경우에도 지역 법률 및 제한 사항이나 기타 요인을 통해 특정 제품이 일부 국가 및 지역에 나열되지 않을 수 있습니다.

### <a name="preview-audience"></a>미리 보기 대상

제품을 광범위한 Marketplace 제품에 라이브로 게시하기 전에 먼저 제한된 **미리 보기 대상**에서 사용할 수 있도록 해야 합니다. 여기에서 **숨기기 키**(소문자 및/또는 숫자만 사용하는 문자열)를 입력합니다. 미리 보기 대상의 멤버는 이 숨기기 키를 토큰으로 사용하여 Marketplace에서 제품의 미리 보기를 볼 수 있습니다.

그런 다음 제품을 사용할 수 있도록 설정하고 미리 보기 제한을 제거할 준비가 되면 **숨기기 키**를 제거하고 다시 게시합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="technical-configuration"></a>기술 구성

이 페이지에서는 제품에 연결하는 데 사용되는 기술 세부 정보를 정의합니다. 이 연결을 통해 최종 고객을 위한 제품을 프로비저닝할 수 있습니다.

### <a name="solution-identifier"></a>솔루션 식별자

솔루션에 대한 솔루션 식별자(GUID)를 제공합니다.

솔루션 식별자를 찾으려면:
1. Microsoft Dynamics LCS(수명 주기 서비스)에서 **솔루션 관리**를 선택합니다.
2. 솔루션을 선택한 다음 **패키지 개요**에서 **솔루션 식별자**를 찾습니다. 식별자가 비어 있으면 **편집**을 선택하고 패키지를 다시 게시한 후 다시 시도하세요.

### <a name="release-version"></a>릴리스 버전

이 솔루션이 작동하는 Dynamics 365 for Finance and Operations의 버전을 선택합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="test-drive-technical-configuration"></a>시험 사용 기술 구성

이 페이지에서는 고객이 제품을 구입하기 전에 제품을 사용해 볼 수 있도록 시연("시험 사용")을 설정할 수 있습니다. 자세한 내용은 [시험 사용이란?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) 문서를 참조하세요.

시험 사용을 사용하도록 설정하려면 [제품 설치](#test-drive) 탭에서 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란 선택을 취소합니다.

각각의 기술 구성 요구 사항을 포함하는 다음과 같은 종류의 시험 사용을 사용할 수 있습니다.

- [Azure 리소스 관리자](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [논리 앱](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives)(기술 구성 필요 없음)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 시험 사용에 대한 기술 구성

솔루션을 구성하는 모든 Azure 리소스를 포함한 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다. [Azure Resource Manager 시험 사용](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)을 설정하는 방법에 대해 자세히 알아보세요.

- **지역**(필수) - 현재 시험 사용을 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. 최상의 성능을 위해 일반적으로 고객 수가 가장 많은 지역에서 시험 사용을 사용할 수 있도록 하여 가장 가까운 지역을 선택할 수 있게 합니다. 구독에서 선택한 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인해야 합니다.

- **인스턴스** – 유형(핫 또는 콜드)과, 제품을 사용할 수 있는 지역 수를 곱하여 사용 가능한 인스턴스 수를 선택합니다.

    **핫** – 이 유형의 인스턴스는 배포된 후 선택한 지역별로 액세스를 기다립니다. 고객은 배포를 기다릴 필요 없이 시험 사용의 *핫* 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객이 전체 배포를 기다리지 않으므로 *핫* 인스턴스를 하나 이상 사용하는 것이 좋습니다. *핫* 인스턴스를 사용할 수 없는 경우 고객 사용량이 줄어들게 됩니다.

    **콜드** – 이 유형의 인스턴스는 지역별로 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 시험 사용을 요청할 때 배포할 전체 시험 사용 Resource Manager 템플릿이 필요하므로 *콜드* 인스턴스의 로드 속도는 *핫* 인스턴스보다 훨씬 느립니다. 단점은 시험 사용의 기간 동안 비용을 지불하는 것이고 *핫* 인스턴스와 같이 Azure 구독에서 항상 실행되는 것은 *아닙니다*.

- **시험 사용 Azure Resource Manager 템플릿** – Azure Resource Manager 템플릿이 포함된 .zip 파일을 업로드합니다.  자세한 내용은 빠른 시작 문서 [Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)에서 Azure Resource Manager 템플릿을 만드는 방법을 참조하세요.

- **시험 사용 기간**(필수) – 시험 사용이 활성 상태로 유지되는 기간(시간 단위)을 입력합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 이 기간은 시간 단위(예: "2"시간. "1.5"시간은 유효하지 않음)로만 설정할 수 있습니다.

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 시험 사용에 대한 기술 구성

Microsoft는 이 유형의 시험 사용을 사용하는 서비스 프로비저닝 및 배포를 호스팅하고 유지 관리하여 시험 사용의 복잡한 설치 과정을 제거할 수 있습니다. 이 유형의 호스트된 시험 사용에 대한 구성은 시험 사용 대상이 Business Central, 고객 참여 또는 작업 대상인지에 관계없이 동일합니다.

- **최대 동시 시험 사용 수**(필수) – 시험 사용을 동시에 사용할 수 있는 최대 고객 수를 설정합니다. 각 동시 사용자는 시험 사용이 활성화된 상태에서 Dynamics 365 라이선스를 사용하므로 최대 제한 집합을 지원하는 라이선스가 충분한지 확인해야 합니다. 권장 값은 3~5입니다.

- **시험 사용 기간**(필수) – 시험 사용이 활성 상태로 유지되는 기간을 시간 단위로 입력합니다. 이 시간이 지난 후에는 세션이 종료되고 더 이상 라이선스를 사용하지 않습니다. 추천 값은 제품의 복잡성에 따라 2~24시간입니다. 이 기간은 시간 단위(예: "2"시간. "1.5"시간은 유효하지 않음)로만 설정할 수 있습니다.  시간이 만료된 후 시험 사용에 다시 액세스하려는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스 URL**(필수) – 고객이 시험 사용을 시작하는 URL입니다. 일반적으로 샘플 데이터가 설치된 앱을 실행하는 Dynamics 365 인스턴스의 URL입니다(예: `https://testdrive.crm.dynamics.com`).

- **인스턴스 웹 API URL**(필수) – Microsoft 365 계정에 로그인하고 **설정** \&gt; **사용자 지정** \&gt; **개발자 리소스** \&gt; **인스턴스 웹 API(서비스 루트 URL)** 로 이동하여 Dynamics 365 인스턴스의 Web API URL을 검색합니다. 여기에 있는 URL(예: `https://testdrive.crm.dynamics.com/api/data/v9.0`)을 복사합니다.

- **역할 이름**(필수) – 사용자 지정 Dynamics 365 시험 사용에서 정의한 보안 역할 이름을 제공합니다. 이는 시험 사용 중에 사용자에게 할당됩니다(예: test-drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>논리 앱 시험 사용에 대한 기술 구성

모든 사용자 지정 제품은 복잡한 솔루션 아키텍처의 다양성을 포함하는 이 유형의 시험 사용 배포 템플릿을 사용해야 합니다. 논리 앱 시험 사용을 설정하는 방법에 대한 자세한 내용은 GitHub의 [작업](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 참여](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)를 참조하세요.

- **지역**(필수, 단일 선택 드롭다운 목록) - 현재 시험 사용을 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. 논리 앱에 대한 리소스는 선택한 지역에 배포됩니다. 논리 앱이 특정 지역에 저장된 사용자 지정 리소스를 포함하는 경우 여기서 해당 지역을 선택해야 합니다. 사용 가능한 지역에 대한 사용자 지정 리소스를 유지하는 가장 좋은 방법은 포털의 Azure 구독에 로컬로 논리 앱을 완전히 배포하고 이를 선택하기 전에 제대로 작동하는지 확인하는 것입니다.

- **최대 동시 시험 사용 수**(필수) – 시험 사용을 동시에 사용할 수 있는 최대 고객 수를 설정합니다. 이러한 시험 사용은 이미 배포되어 있어 고객은 배포를 기다리지 않고 즉시 액세스할 수 있습니다.

- **시험 사용 기간**(필수) – 시험 사용이 활성 상태로 유지되는 기간(시간 단위)을 입력합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다.

- **Azure 리소스 그룹 이름**(필수) - 논리 앱 시험 사용이 저장된 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) 이름을 입력합니다.

- **Azure 논리 앱 이름**(필수) – 시험 사용을 사용자에게 할당하는 논리 앱의 이름을 입력합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장되어야 합니다.

- **논리 앱 이름 프로비저닝 해제**(필수) – 고객이 완료되면 시험 사용 프로비저닝을 해제하는 논리 앱의 이름을 입력합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장되어야 합니다.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 시험 사용에는 기술 구성이 필요하지 않습니다.

대화형 Power BI 시각적 개체를 시연하려는 제품은 포함된 링크를 사용하여 사용자 지정 빌드 대시보드를 시험 사용으로 공유할 수 있으며 추가 기술 구성은 필요하지 않습니다. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 템플릿 앱 설정에 대해 자세히 알아보세요.

### <a name="deployment-subscription-details"></a>배포 구독 세부 정보

사용자 대신 시험 사용을 배포하기 위해 별도의 고유한 Azure 구독을 만들고 제공합니다. (Power BI 시험 사용에는 필요하지 않음)

- **Azure 구독 ID**(Azure Resource Manager 및 Logic Apps에 필요) - 리소스 사용량 보고 및 청구를 위해 Azure 계정 서비스에 대한 액세스 권한을 부여하는 구독 ID를 입력합니다. 아직 없는 경우 시험 사용에 사용할 [별도의 Azure 구독을 만드는 것](https://docs.microsoft.com/azure/billing/billing-create-subscription)이 좋습니다. [Azure Portal](https://portal.azure.com/)에 로그인하여 왼쪽 메뉴의 **구독** 탭으로 이동하면 Azure 구독 ID를 확인할 수 있습니다. 이 탭을 선택하면 구독 ID(예: "a83645ac-1234-5ab6-6789-1h234g764ghty")가 표시됩니다.

- **Azure AD 테넌트 ID**(필수) - Azure AD(Active Directory) [테넌트 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택하고 **속성**을 선택한 다음 나열된 **디렉터리 ID** 번호(예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 주소를 사용하여 조직의 테넌트 ID를 조회할 수도 있습니다.

- **Azure AD 테넌트 이름**(Dynamic 365에 필요) – Azure AD(Active Directory) 이름을 입력합니다. 이 이름을 찾기 위해 오른쪽 위에 있는 [Azure Portal](https://portal.azure.com/)에 로그인하면 테넌트 이름이 계정 이름 아래에 나열됩니다.

- **Azure AD 앱 ID**(필수) – Azure AD(Active Directory) [애플리케이션 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택한 후 **앱 등록**을 선택하고 나열된 **애플리케이션 ID** 번호(예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다.

- **Azure AD 앱 클라이언트 비밀**(필수) – Azure AD 애플리케이션 [클라이언트 비밀](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)을 입력합니다. 이 값을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택하고 **앱 등록**을 선택한 다음 시험 사용 앱을 선택합니다. 그런 다음 **인증서 및 비밀**을 선택하고 **새 클라이언트 비밀**을 선택하여 설명을 입력하고 **만료**에서 **안 함**을 선택한 다음 **추가**를 선택합니다. 이 값을 적어 두어야 합니다. (값을 기록하기 전에 페이지에서 벗어나지 마세요. 그러지 않으면 이 값에 액세스할 수 없습니다.)

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="test-drive-marketplace-listings"></a>시험 사용 Marketplace 목록

**시험 사용** 탭에 있는 **Marketplace 목록** 옵션은 시험 사용을 사용할 수 있는 언어를 표시합니다. 현재 **영어(미국)** 만 사용할 수 있습니다. 언어 이름을 선택하여 시험 사용 환경을 설명하는 정보를 입력합니다.

- **설명**(필수) – 시험 사용, 시연 대상, 사용자의 실험 목표, 탐색할 기능 및 사용자가 제품을 가져올지 여부를 결정하는 데 도움이 되는 관련 정보를 설명합니다. 이 필드에는 최대 3000자의 텍스트를 입력할 수 있습니다.

- **액세스 정보**(Azure Resource Manager 및 논리 시험 사용에 필요) - 이 시험 사용에 액세스하고 사용하기 위해 고객이 알아야 할 사항을 설명합니다. 제품 사용에 대한 시나리오와 고객이 시험 사용을 통해 기능에 액세스하기 위해 알아야 할 사항을 정확히 살펴봅니다. 이 필드에는 최대 10000자의 텍스트를 입력할 수 있습니다.

- **사용자 매뉴얼**(필수) – 시험 사용 환경의 심층 연습입니다. 사용자 매뉴얼은 고객이 시험 사용을 통해 얻을 수 있는 사항을 정확하게 다루며, 고객에게 있을 수 있는 질문에 대한 참조로 사용됩니다. 업로드한 파일은 PDF 형식이어야 하며 최대 255자로 이름이 지정되어야 합니다.

- **동영상**(선택 사항) – 동영상을 YouTube 또는 Vimeo에 업로드하고 링크 및 썸네일 이미지(533 x 324 픽셀)로 참조되도록 할 수 있으므로 여기서 고객이 제품의 기능을 잘 사용하는 방법을 포함하여 시험 사용을 보다 잘 이해하고 혜택을 강조하는 시나리오를 이해할 수 있도록 안내하는 정보를 볼 수 있습니다.
  - **이름**(필수)
  - **URL**(필수 - YouTube 또는 Vimeo만 해당)
  - **썸네일**(이미지는 PNG 형식으로 533 x 324 픽셀이어야 함)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="supplemental-content"></a>추가 콘텐츠

이 페이지에서 제품의 유효성을 검사하는 데 도움이 되는 제품의 추가 정보를 제공할 수 있습니다. 이 정보는 고객에게 표시되거나 Marketplace에 게시되지는 않습니다.

### <a name="validation-assets"></a>유효성 검사 자산

이 섹션에서 [CAR(사용자 지정 분석 보고서)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report)을 업로드합니다. 이 보고서는 미리 정의된 모범 사례 규칙 집합을 기반으로 사용자 지정 및 확장 모델을 분석하여 생성됩니다.

이 파일은 .xls 또는 .xlsx 형식이어야 합니다. 두 개 이상의 보고서가 있는 경우 모든 보고서를 포함하는 .zip 파일을 업로드할 수 있습니다.

### <a name="does-solution-include-localizations"></a>솔루션에 지역화 버전이 포함되어 있나요?

솔루션에서 로컬 표준 및 정책을 사용하도록 설정하는 경우 **예**를 선택합니다(예: 다른 국가/지역에 필요한 다른 급여 규칙을 수용하는 경우). 그렇지 않은 경우 **아니요**를 선택합니다.

### <a name="does-solution-enable-translations"></a>솔루션에서 번역이 가능한가요?

솔루션의 텍스트를 다른 언어로 번역할 수 있는 경우 **예**로 응답합니다. 그렇지 않은 경우 **아니요**를 선택합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제품의 필수 섹션을 모두 완료한 후에는 포털의 오른쪽 위 모서리에서 **검토 및 게시**를 선택합니다.

이 제품을 처음 게시하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대한 완료 상태를 확인합니다.
    - **시작되지 않음** – 섹션이 작업되지 않았으므로 완료되어야 합니다.
    - **완료되지 않음** – 섹션에 수정해야 하는 오류가 있거나 추가 정보를 입력해야 합니다. 섹션으로 돌아가서 업데이트합니다.
    - **완료됨** – 섹션이 완료되고 필요한 모든 데이터가 입력되었으며 오류가 없습니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료 상태여야 합니다.
- **인증 참고 사항** 섹션에서 앱을 이해하는 데 도움이 되는 추가 정보뿐만 아니라 앱이 올바르게 테스트되는지 확인하는 테스트 지침을 인증 팀에 제공합니다.
- **제출**을 선택하여 게시할 제품을 제출합니다. 제품의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일을 보내 드립니다. 파트너 센터로 돌아가서 제품을 공개 게시(또는 프라이빗 제품을 프라이빗 사용자에게 게시)하려면 **라이브 상태로 전환**을 선택합니다.

## <a name="next-step"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
