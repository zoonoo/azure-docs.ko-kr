---
title: Microsoft 상업적 marketplace에서 관리 서비스 제품 만들기
description: 파트너 센터에서 상업용 Marketplace 포털을 사용 하 여 Azure Marketplace에 나열 하기 위해 새 관리 서비스 제품을 만드는 방법입니다.
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: a8cd35ced61c5b0e8b3b534394c9b6a0f543ebbb
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691370"
---
# <a name="create-a-managed-service-offer"></a>관리되는 서비스 제품 만들기

관리 서비스는 [Azure Lighthouse](../../lighthouse/overview.md) 시나리오를 사용 하도록 설정 하는 데 도움이 됩니다. 고객이 관리 서비스 제안을 수락 하면 [Azure에서 위임 된 리소스 관리](../../lighthouse/concepts/azure-delegated-resource-management.md)를 위해 리소스를 등록할 수 있습니다. 아직 수행 하지 않은 경우 시작 하기 전에 [파트너 센터에서 상업적 Marketplace 계정을 만듭니다](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) . 계정이 상업적 marketplace 프로그램에 등록 되어 있는지 확인 합니다.

[실버 또는 골드 클라우드 플랫폼 역량 수준이](https://partner.microsoft.com/membership/cloud-platform-competency) 있거나 관리 서비스 제품을 게시 하려면 [Azure 전문가 MSP](https://partner.microsoft.com/membership/azure-expert-msp) 여야 합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인 합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택 합니다.
3. 개요 페이지에서 **+ 새 제품** > **관리 서비스**를 선택 합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-managed-service.png)

>[!NOTE]
>제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 상점에 표시 됩니다. 변경을 수행한 후 항상 다시 게시 해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력 합니다. 계정의 각 제품에 대 한 고유 식별자입니다.

* 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿에 대 한 웹 주소의 고객에 게 표시 됩니다 (해당 하는 경우).
* 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50 자로 제한 됩니다. 예를 들어 **테스트-제품-1**을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`입니다.
* **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력 합니다. 파트너 센터의 제안에 사용 되는 이름입니다.

* 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다.
* **만들기**를 선택한 후에는 번째 제안 별칭을 변경할 수 없습니다.

**만들기** 를 선택 하 여 제품을 생성 하 고 계속 합니다.

## <a name="offer-setup"></a>제품 설정

## <a name="connect-lead-management"></a>리드 관리 연결

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

[관리 되는 서비스 인증 정책](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)에 따라 **리드 대상이** 필요 합니다. 이렇게 하면 고객이 제품을 배포할 때마다 CRM 시스템에 레코드가 만들어집니다.

자세한 내용은 [리드 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조 하세요.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="properties"></a>속성

이 페이지에서는 marketplace에서 제품을 그룹화 하는 데 사용 되는 범주와 제품을 지 원하는 법적 계약을 정의할 수 있습니다.

### <a name="category"></a>범주

제품을 적절 한 marketplace 검색 영역에 추가 하는 데 사용 되는 최소 1 개 및 최대 5 개의 범주를 선택 합니다. 제품이 제품 설명에서 이러한 범주를 어떻게 지원 하는지 확인 해야 합니다.

### <a name="terms-and-conditions"></a>사용 약관

사용 **약관** 필드에 고유한 약관을 제공 합니다. 사용 약관을 찾을 수 있는 URL을 제공할 수도 있습니다. 고객은 이러한 약관에 동의 해야 제품을 사용해 볼 수 있습니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="offer-listing"></a>제품 목록

이 페이지에서 제품에 대 한 marketplace 세부 정보 (예: 제품 이름, 설명 및 이미지)를 정의할 수 있습니다.

> [!NOTE]
> 제품 설명 (예: 설명, 문서, 스크린샷 및 사용 약관)은 영어로 시작 해야 합니다. "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다." 라고 표시 됩니다. 또한 제품 목록 콘텐츠에 사용 되는 것 이외의 언어로 콘텐츠를 제공 하는 *데 유용한 링크 URL* 을 제공 하는 것도 허용 됩니다.

### <a name="name"></a>Name

여기에 입력 하는 이름은 고객에 게 제품 목록의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제공 별칭** 에 입력 한 텍스트로 미리 채워져 있지만이 값을 변경할 수 있습니다. 이 이름은 상표 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50 자를 초과할 수 없으며, 모든 emojis를 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

Marketplace 검색 결과에서 사용할 수 있는 제품에 대 한 간략 한 설명을 제공 합니다 (최대 100 자).

### <a name="long-summary"></a>긴 요약

제품에 대 한 더 긴 설명 (최대 256 자)을 제공 합니다. 이 긴 요약은 marketplace 검색 결과에도 사용할 수 있습니다.

### <a name="description"></a>Description

제품에 대 한 더 긴 설명 (최대 3000 자)을 제공 합니다. 이 설명은 marketplace 목록 개요에서 고객에 게 표시 됩니다. 제품의 가치 제안, 주요 이점, 범주 및/또는 업계 연결, 앱 내 구매 기회 및 필요한 공개를 포함 합니다.

설명 작성에 대 한 몇 가지 팁은 다음과 같습니다.  

- 설명의 처음 몇 문장에서 제품의 가치 제안을 명확하게 설명합니다. 값 제안에 다음 항목을 포함 합니다.
  - 제안에 대 한 설명
  - 제품을 활용 하는 사용자의 유형입니다.
  - 고객이 요구 하거나 제공 하는 불만
- 처음 몇 문장은 검색 엔진 결과에 표시될 수 있다는 점에 유의하세요.  
- 제품을 판매하는 특징과 기능에 의존하지 않습니다. 대신, 전달하는 가치에 중점을 둡니다.  
- 산업별 어휘 또는 혜택 기반 단어를 최대한 많이 사용합니다.

제품 설명을 보다 유용 하 게 사용 하려면 서식 있는 텍스트 편집기를 사용 하 여 서식 지정을 적용 합니다.

![서식 있는 텍스트 편집기 사용](./media/rich-text-editor.png)

| <center>텍스트 형식 변경 | <center>글머리 기호 또는 번호 매기기 추가 | <center>텍스트 들여쓰기 추가 또는 제거 |
| --- | --- | --- |
| <center>![서식 있는 텍스트 편집기를 사용 하 여 텍스트 형식 변경](./media/text-editor3.png) |  <center>![서식 있는 텍스트 편집기를 사용 하 여 목록 추가](./media/text-editor4.png) |  <center>![서식 있는 텍스트 편집기를 사용 하 여 들여쓰기](./media/text-editor5.png) |

### <a name="privacy-policy-link"></a>개인 정보 취급 방침 링크

조직의 개인 정보 취급 방침에 대 한 URL을 입력 합니다 (사이트에서 호스트). 사용자는 앱이 개인 정보 법률 및 규정을 준수 하는지 확인 하 고 유효한 개인 정보 취급 방침을 제공 하는 일을 담당 합니다.

### <a name="useful-links"></a>유용한 링크

솔루션에 대 한 선택적 보조 온라인 문서를 제공 합니다. **+ 링크 추가**를 클릭 하 여 유용한 링크를 더 추가 합니다.

### <a name="contact-information"></a>연락처 정보

이 섹션에서는 **지원 연락처** 와 **엔지니어링 연락처**에 대 한 이름, 전자 메일 및 전화 번호를 제공 해야 합니다. 이 정보는 고객에 게 표시 되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에 게 제공 될 수 있습니다.

### <a name="support-urls"></a>지원 Url

**Azure Global customers** 및/또는 **Azure Government 고객**에 대 한 지원 웹 사이트가 있는 경우 여기에서 해당 url을 제공 하세요.

### <a name="marketplace-images"></a>Marketplace 이미지

이 섹션에서는 고객에 게 제품을 표시할 때 사용 되는 로고 및 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식 이어야 합니다.

>[!NOTE]
>파일을 업로드 하는 데 문제가 있는 경우 로컬 네트워크가 파트너 센터에서 사용 하는 https://upload.xboxlive.com 서비스를 차단 하지 않는지 확인 합니다.

#### <a name="marketplace-logos"></a>마켓플레이스 로고

제품 로고를 4 픽셀 크기로 제공 합니다.

- **작음** (40 x 40)
- **보통** (90 x 90)
- **큼** (115 x 115)
- **넓게** (255 x 115)

로고에 대해서는 다음 지침을 따르세요.

- Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수를 제한합니다.
- 포털의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로 이러한 색을 사용하지 마십시오. 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다.
- 투명한 배경을 사용하는 경우 로고 및 텍스트는 흰색, 검은색 또는 파란색이 아니어야 합니다.
- 로고의 모양과 느낌은 평면적이어야 하며 그라데이션은 사용하지 마십시오. 로고의 배경에 그라데이션 효과를 사용하지 마십시오.
- 로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 마십시오.
- 로고가 늘어나지 않았는지 확인합니다.

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 최대 5 개의 스크린샷을 추가 합니다. 모든 스크린샷 1280 x 720 픽셀 이어야 합니다.

#### <a name="videos"></a>동영상

필요에 따라 제품을 보여 주는 비디오를 최대 5 개까지 추가할 수 있습니다. 이러한 비디오는 YouTube 및/또는 Vimeo에서 호스팅되어야 합니다. 각 항목에 대해 비디오 이름, 해당 URL 및 비디오의 미리 보기 이미지 (1280 x 720 픽셀)를 입력 합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열 하는 추가 마켓플레이스

- [Marketplace 제품 목록에 대 한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="preview"></a>미리 보기

제품을 광범위 한 marketplace 제품에 게시 하기 전에 먼저 제한 된 미리 보기 대상 사용자가 사용할 수 있도록 해야 합니다. 이를 통해 고객에 게 제공 하기 전에 Azure Marketplace에 표시 되는 방법을 확인할 수 있습니다. Microsoft 지원 및 엔지니어링 팀은이 미리 보기 기간 동안 제품을 볼 수 있습니다.

Preview **대상 그룹 섹션에** Azure 구독 id를 입력 하 여 미리 보기 대상 그룹을 정의할 수 있습니다. 최대 10 개의 구독 Id를 수동으로 입력 하거나 최대 100 구독 Id를 포함 하는 .csv 파일을 업로드할 수 있습니다.

이러한 구독과 연결 된 모든 고객은 live가 되기 전에 Azure Marketplace에서 제품을 볼 수 있습니다. 제품을 미리 볼 수 있도록 여기에 고유한 구독을 포함 해야 합니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="plan-overview"></a>계획 개요

각 제품에는 하나 이상의 플랜(SKU라고도 함)이 있어야 합니다. 여러 플랜을 추가하여 다양한 가격으로 다양한 기능 세트를 지원하거나, 제한된 특정 고객 대상 그룹을 위한 특정 플랜을 사용자 지정할 수 있습니다. 고객은 상위 제품 아래에서 사용할 수 있는 플랜을 볼 수 있습니다.

**계획 개요** 페이지에서 **+ 새 계획 만들기**를 선택 합니다. 그런 다음 **계획 ID** 와 **계획 이름을**입력 합니다. 이러한 값은 모두 소문자 영숫자, 대시 및 밑줄만 포함할 수 있으며 최대 50 자까지 포함할 수 있습니다. 이러한 값은 고객에 게 표시 될 수 있으며 제품을 게시 한 후에는 변경할 수 없습니다.

계획에 대 한 작업을 계속 하려면 이러한 값을 입력 한 후 **만들기** 를 선택 합니다. **계획 목록**, **가격 책정 및 가용성**, **기술 구성**의 세 가지 섹션을 완료할 수 있습니다.

### <a name="plan-listing"></a>계획 목록

먼저 계획에 대 한 **검색 결과 요약** 을 제공 합니다. Marketplace 검색 결과에 사용할 수 있는 계획에 대 한 간단한 설명입니다 (최대 100 자).

다음으로 계획에 대 한 자세한 설명을 제공 하는 **설명을** 입력 합니다.

### <a name="pricing-and-availability"></a>가격 책정 및 가용성

현재 관리 서비스 제공에 사용할 수 있는 가격 책정 모델은 하나 뿐입니다. **BYOL (사용자 라이선스 보유)**. 즉, 고객에게 이 제품과 관련된 비용을 직접 청구할 것이며 Microsoft는 사용자에게 요금을 부과하지 않음을 의미합니다.

**계획 표시 유형** 섹션에서이 계획이 [개인](../../marketplace/private-offers.md)계획 인지 여부를 나타낼 수 있습니다. **비공개 요금제 인 경우이** 확인란을 선택 취소 하면 특정 고객 또는 특정 고객 수로 계획이 제한 되지 않습니다.

이 플랜을 특정 고객만 사용할 수 있도록 설정하려면 **예**를 선택합니다. 이렇게 하면 구독 ID를 제공하여 고객을 식별해야 합니다. 하나는 하나 (최대 10 개 구독의 경우) 또는 .csv 파일 (모든 계획에서 최대 1만 구독의 경우)을 업로드 하 여 입력할 수 있습니다. 제품을 테스트하고 유효성을 검사할 수 있도록 여기에 고유한 구독을 포함해야 합니다.

> [!IMPORTANT]
> 계획이 공개로 게시 되 면 비공개로 변경할 수 없습니다. 제품을 수락 하 고 리소스를 위임할 수 있는 고객을 제어 하려면 비공개 요금제를 사용 합니다. 공개 계획을 사용 하면 특정 고객 또는 특정 수의 고객에 대 한 가용성을 제한할 수 없습니다 (이렇게 선택 하는 경우 계획을 완전히 판매 하지 않을 수 있음). 제품을 게시할 때 **역할 정의가** [관리 되는 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) 로 설정 된 **권한 부여** 를 포함 한 경우에만 고객이 제안을 수락한 후 [에 위임에 대 한 액세스 권한을 제거할](../../lighthouse/how-to/remove-delegation.md) 수 있습니다. 또한 고객에 게 연락 하 여 [액세스 권한을 제거](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)하도록 요청할 수 있습니다.

### <a name="technical-configuration"></a>기술 구성

계획의이 섹션에서는 고객 리소스를 관리 하기 위한 권한 부여 정보를 사용 하 여 매니페스트를 만듭니다. 이 정보는 [Azure 위임 된 리소스 관리](../../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하도록 설정 하는 데 필요 합니다.

[Azure Lighthouse 시나리오에서 테 넌 트, 역할 및 사용자](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) 를 검토 하 여 지원 되는 역할 및 권한 부여를 정의 하기 위한 모범 사례를 이해 해야 합니다.

> [!NOTE]
> 위에서 설명한 것처럼 **권한 부여** 항목의 사용자 및 역할은 플랜을 구매한 모든 고객에게 적용됩니다. 특정 고객에 대한 액세스를 제한하려는 경우 독점 사용을 위해 프라이빗 플랜을 게시해야 합니다.

#### <a name="manifest"></a>file:///

먼저 매니페스트의 **버전** 을 제공합니다. *n.n.n* 형식(예: 1.2.5)을 사용합니다.

그런 다음, **테넌트 ID**를 입력합니다. 이 GUID는 조직의 Azure Active Directory (Azure AD) 테 넌 트 ID와 연결 된 GUID입니다. 즉, 사용자가 고객의 리소스에 액세스할 테 넌 트를 관리 합니다. 이 작업이 어려운 경우 Azure Portal의 오른쪽 위에 있는 계정 이름으로 마우스를 가져가거나 **디렉터리 전환**을 선택하여 찾을 수 있습니다.

새 버전의 제품을 게시 하 고 업데이트 된 매니페스트를 만들어야 하는 경우 **+ 새 매니페스트**를 선택 합니다. 이전 매니페스트 버전에서 버전 번호를 늘려야 합니다.

#### <a name="authorization"></a>권한 부여

권한 부여는 계획을 구매한 고객의 리소스 및 구독에 액세스할 수 있는 관리 테 넌 트의 엔터티를 정의 합니다. 이러한 각 엔터티에는 특정 수준의 액세스를 부여 하는 기본 제공 역할이 할당 됩니다.

각 계획에 대해 최대 20 개의 권한 부여를 만들 수 있습니다.

> [!TIP]
> 대부분의 경우 일련의 개별 사용자 계정이 아닌 Azure AD 사용자 그룹 또는 서비스 주체에 역할을 할당 하는 것이 좋습니다. 이렇게 하면 액세스 요구 사항이 변경될 때 플랜을 업데이트한 후 다시 게시하지 않고도 개별 사용자에 대한 액세스 권한을 추가하거나 제거할 수 있습니다. Azure AD 그룹에 역할을 할당 하는 경우 [ **그룹 유형은** **Office 365**이 아닌 **보안** 이어야](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)합니다. 추가 권장 사항은 [Azure Lighthouse 시나리오의 테넌트, 역할 및 사용자](../../lighthouse/concepts/tenants-users-roles.md)를 참조하세요.

각 **권한 부여**에 대해 다음을 제공해야 합니다. 그런 다음 추가 사용자 및 역할 정의를 추가 하는 데 필요한 횟수 만큼 **+ 권한 부여 추가** 를 선택할 수 있습니다.

- **AZURE Ad 개체 ID**: 사용자, 사용자 그룹 또는 응용 프로그램의 azure ad 식별자로, 사용자, 사용자 그룹 또는 응용 프로그램의 사용자, 사용자 그룹 또는 응용 프로그램의 리소스에 대 한 특정 사용 권한이 부여 됩니다.
- **AZURE AD 개체 표시 이름**: 고객이이 권한 부여의 용도를 이해 하는 데 도움이 되는 친숙 한 이름입니다. 리소스를 위임할 때 고객에게 이 이름이 표시됩니다.
- **역할 정의**: 목록에서 사용 가능한 Azure AD 기본 제공 역할 중 하나를 선택 합니다. 이 역할은 **Azure AD 개체 ID** 필드의 사용자가 고객의 리소스에 대해 갖게 되는 권한을 결정합니다. 이러한 역할에 대 한 설명은 [기본 제공 역할](../../role-based-access-control/built-in-roles.md) 및 [Azure 위임 된 리소스 관리에 대 한 역할 지원](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)을 참조 하세요.
  > [!NOTE]
  > 해당 하는 새로운 기본 제공 역할이 Azure에 추가 되 면 여기에서 사용할 수 있게 되며, 이러한 역할은 표시 되기 전에 약간의 지연이 있을 수 있습니다.
- **할당 가능한 역할**:이 옵션은이 권한 부여에 대 한 **역할 정의** 에서 사용자 액세스 관리자를 선택한 경우에만 표시 됩니다. 그렇다면 여기에 할당 가능한 역할을 하나 이상 추가해야 합니다. **AZURE AD 개체 ID** 필드의 사용자는 관리 되는 [id](../../active-directory/managed-identities-azure-resources/overview.md)에 이러한 역할을 할당할 수 있습니다. 이러한 역할은 [재구성 가능한 정책을 배포](../../lighthouse/how-to/deploy-policy-remediation.md)하는 데 필요 합니다. 사용자 액세스 관리자 역할에 연결된 다른 사용 권한이 이 사용자에게 적용되지 않습니다.

> [!TIP]
> 필요한 경우 [위임에 대 한 액세스를 제거할](../../lighthouse/how-to/remove-delegation.md) 수 있도록 하려면 **역할 정의가** [관리 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)로 설정 된 **권한 부여** 를 포함 합니다. 이 역할을 할당하지 않으면 고객 테넌트의 사용자만 위임된 리소스를 제거할 수 있습니다.

계획에 대 한 모든 섹션을 완료 한 후에는 추가 계획을 만드는 데 필요한 만큼 **+ 새 계획 만들기** 를 선택할 수 있습니다. 완료되면 **저장**을 선택합니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="publish"></a>게시하기

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제품의 필수 섹션을 모두 완료 한 후에는 포털의 오른쪽 위 모서리에서 **검토 및 게시** 를 선택 합니다.

이 제품을 처음 게시 하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다.
  - **시작 되지 않음** -섹션이 작업 되지 않았으므로 완료 해야 합니다.
  - **불완전** -섹션에 수정 해야 하는 오류가 있거나 추가 정보를 제공 해야 합니다. 섹션으로 돌아가서 업데이트 합니다.
  - **완료** -섹션이 완료 되 고 필요한 모든 데이터가 제공 되었으며 오류가 발생 하지 않습니다. 제품의 모든 섹션은 제품을 제출 하기 전에 완전 한 상태 여야 합니다.
- **인증에 대 한 참고 사항** 섹션에서는 앱을 이해 하는 데 도움이 되는 보조 노트 뿐만 아니라 앱이 올바르게 테스트 되었는지 확인 하기 위해 인증 팀에 테스트 지침을 제공 합니다.
- **제출**을 선택 하 여 게시에 대 한 제품을 제출 합니다. 제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 경우 전자 메일을 보냅니다. 파트너 센터로 돌아가서 제품을 공개 (또는 개인 제품의 경우 비공개 사용자에 게 공개)에 게시 하기 위해 **live live** 를 선택 합니다.

### <a name="customer-experience-and-offer-management"></a>사용자 환경 및 제품 관리

고객은 제품을 배포 하는 경우 [Azure 위임 된 리소스 관리](../../lighthouse/concepts/azure-delegated-resource-management.md)를 위해 구독 또는 리소스 그룹을 위임할 수 있습니다. 이 프로세스에 대 한 자세한 내용은 [고객 온 보 딩 프로세스](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)를 참조 하세요.

언제든지 [제품의 업데이트된 버전을 게시](update-existing-offer.md)할 수 있습니다. 예를 들어 이전에 게시된 제품에 새 역할 정의를 추가하려고 할 수 있습니다. 이렇게 하면 제품을 이미 추가한 고객은 Azure Portal의 [**서비스 공급자**](../../lighthouse/how-to/view-manage-service-providers.md) 페이지에 업데이트를 사용할 수 있음을 알려 주는 아이콘이 표시됩니다. 각 고객은 변경 내용을 검토하고 새 버전으로 업데이트할지 여부를 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
- [Azure Lighthouse에 대 한 자세한 정보](../../lighthouse/overview.md)
