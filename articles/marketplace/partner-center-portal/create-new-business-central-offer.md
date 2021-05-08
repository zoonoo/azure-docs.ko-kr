---
title: Microsoft AppSource에서 Dynamics 365 Business Central 제안 만들기
description: Microsoft AppSource에서 Dynamics 365 for Business Central 제안을 만드는 방법입니다. AppSource에서 또는 CSP(클라우드 솔루션 공급자) 프로그램을 통해 제안을 나열하거나 판매합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 001f7453c29e7a8525fb88a96dd9a867468460e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501436"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Dynamics 365 for Business Central 제안 만들기

이 문서에서는 새 Dynamics 365 Business Central 제안을 만드는 방법을 설명합니다. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central)은 ERP(전사적 자원 관리) 서비스로 재무, 운영, 공급 체인, CRM, 프로젝트 관리 및 전자 상거래를 포함하여 광범위한 비즈니스 프로세스를 처리합니다. 프리미엄 패키지는 클래식 배포 모델 및 제조도 지원합니다. Dynamics 365 Business Central의 모든 제품은 인증 프로세스를 통과해야 합니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만드세요](../create-account.md)(계정이 없는 경우). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

>[!NOTE]
> 제안이 게시된 이후 편집한 내용은 게시를 위해 제안을 다시 제출한 후에만 파트너 센터 및 온라인 스토어에서 업데이트됩니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Dynamics 365 business central** 을 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>새 제안

**제품 ID** 를 입력합니다. 이 값은 계정의 각 제안에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
- 게시자 ID와 결합된 제안 ID의 길이는 40자 미만이어야 합니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없습니다. 예를 들어 게시자 ID가 `testpublisherid`이고 **test-offer-1** 을 입력한 경우 제안 웹 주소는 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 이 ID를 변경할 수 없습니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
- **만들기** 를 선택한 후에는 이 이름을 변경할 수 없습니다.

**만들기** 를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="alias"></a>Alias

파트너 센터 내에서만 이 제안을 참조하는 데 사용할 설명이 포함된 이름을 입력합니다. 이 이름(제안을 만들 때 입력한 내용으로 미리 채워져 있음)은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제안 이름과는 다릅니다. 나중에 제안 이름을 업데이트하려면 [제안 목록](#offer-listing) 페이지로 이동합니다.

### <a name="setup-details"></a>설정 정보

제안에 적용되는 **패키지 유형** 을 선택합니다.

- **추가 기능** 앱은 Dynamics 365 Business Central의 환경 및 기존 기능을 확장합니다. 자세한 내용은 [추가 기능 앱](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)을 참조하세요.
- **Connect** 앱은 Dynamics 365 Business Central과 타사 솔루션 또는 서비스 간에 지점 간 연결을 설정해야 하는 시나리오에서 사용할 수 있습니다. 자세한 내용은 [Connect 앱](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)을 참조하세요.

**잠재 고객이 이 목록 제안과 어떻게 상호 작용하기를 원하나요?** 에 대해서는 이 제안에 사용하려는 옵션을 선택합니다.

- **지금 받기(평가판)** – 고객에게 평가판 제안을 나열합니다.
- **평가판(목록)** – 평가판 링크와 함께 고객에게 제안을 나열합니다. 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다.

    > [!NOTE]
    > 평가판 링크를 통해 애플리케이션에서 수신하는 토큰은 앱에서 자동으로 계정을 만드는 Azure AD(Active Directory)를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용하는 인증에 Microsoft 계정이 지원되지 않습니다.

- **연락처** – CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

### <a name="test-drive"></a>시험 사용

시험 사용은 "구입 전 체험" 옵션을 제공하여 잠재적인 고객에게 제품을 선보임으로써 구매 전환율을 높이고 적절한 잠재 고객을 창출할 수 있는 유용한 방법입니다. 자세히 알아보려면 [시험 사용이란?](../what-is-test-drive.md)에서 시작합니다.

일정 기간 동안 시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

### <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [잠재 고객 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조하세요.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 Marketplace에서 제품을 그룹화하는 데 사용되는 범주와 산업, 앱 버전 및 제품을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="categories"></a>범주

범주와 하위 범주를 선택하여 적절한 마켓플레이스 검색 영역에 제안을 배치합니다. 제품 설명에서 제품이 해당 범주를 지원하는 방법을 설명해야 합니다. 선택:

- 주 범주와 보조 범주를 포함하여 하나 이상에서 최대 2개의 범주(선택 사항).
- 주 및/또는 보조 범주 각각에 대해 최대 2개의 하위 범주. 제안에 적용되는 하위 범주가 없으면 **해당 없음** 을 선택합니다.

[제안 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 범주와 하위 범주의 전체 목록을 확인하세요.

### <a name="industries"></a>산업

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>앱 버전

제품의 버전 번호를 입력합니다. 이 버전은 제품의 세부 정보 페이지에 표시됩니다.

### <a name="terms-and-conditions"></a>사용 약관

사용자 고유의 법률적 사용 약관을 여기에 제공합니다. 사용 약관을 확인할 수 있는 주소를 제공할 수도 있습니다. 고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="offer-listing"></a>제품 목록

이 페이지에서는 제안 이름, 설명, 링크, 연락처와 같은 제안 세부 정보를 정의합니다.

> [!NOTE]
> 제안 목록 세부 정보는 한 가지 언어로만 제공합니다. "이 애플리케이션은 [영어가 아닌 언어]로만 제공됩니다."라는 문구로 시작하는 경우 영어로 작성되지 않아도 됩니다. 또한 제안 목록 콘텐츠에 사용되는 언어 이외의 언어로 콘텐츠를 제공하는 ‘유용한 링크 URL’을 제공할 수 있습니다.

다음은 Microsoft AppSource에서 제안 정보가 표시되는 방식의 예입니다(나열된 가격은 예시일 뿐이며, 실제 비용을 반영하지 않습니다).

:::image type="content" source="media/example-d365-business-central.png" alt-text="Microsoft AppSource에서 제안이 표시되는 방식":::

### <a name="call-out-descriptions"></a>설명선 설명

1. 로고
2. 제품
3. 범주
4. 지원 주소(링크)
5. 사용 약관
6. 개인 정보 보호 정책
7. Offer name
8. 요약
9. Description
10. 스크린샷/비디오

### <a name="marketplace-details"></a>마켓플레이스 세부 정보

여기에 입력하는 **이름** 은 고객에게 제안 목록의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 에 입력한 텍스트로 미리 채워져 있지만 이 값을 변경할 수 있습니다. 이 이름은 상표로 등록될 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50자를 초과할 수 없으며 이모지는 포함할 수 없습니다.

**검색 결과 요약** 에 제안에 대한 간단한 설명을 최대 100자까지 제공합니다. 이 설명은 Marketplace 검색 결과에서 사용할 수 있습니다.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

필요에 따라 고객이 마켓플레이스에서 제안을 찾는 데 도움이 되는 **검색 키워드** 를 최대 3개까지 입력할 수 있습니다. 최상의 결과를 얻기 위해 이 키워드를 설명에도 사용합니다.

**앱과 연동되는 제품** 을 고객에게 알려 주려면 제품 이름을 최대 3개까지 입력합니다.

### <a name="helpprivacy-urls"></a>도움말/개인 정보 보호 URL

고객이 제안에 대해 자세히 알아볼 수 있는 **앱 도움말 링크**(URL)를 입력합니다. 도움말 URL은 지원 URL과 같을 수 없습니다.

조직의 개인정보처리방침에 대한 **개인정보처리방침 링크**(URL)를 입력합니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공하는 것은 게시자의 책임입니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처** 및 **엔지니어링 연락처** 의 이름, 메일 및 전화번호를 제공합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에게 제공될 수 있습니다.

**지원 연락처** 섹션에서 CSP 파트너가 제품에 대한 지원을 찾을 수 있는 **지원 URL** 을 제공합니다. 지원 URL은 도움말 URL과 같을 수 없습니다.

### <a name="supporting-documents"></a>지원 문서

PDF 형식의 백서, 브로슈어, 검사 목록, 프레젠테이션 등의 관련 마케팅 문서를 하나 이상(3개까지) 제공합니다.

### <a name="marketplace-media"></a>마켓플레이스 미디어

고객에게 제안을 표시할 때 사용할 로고 및 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 `https://upload.xboxlive.com` 서비스를 차단하지 않는지 확인하세요.

#### <a name="logos"></a>로고

**큰** 로고의 경우 PNG 파일을 제공합니다. 파트너 센터는 이 초기 파일을 사용하여 다른 필요한 크기를 만듭니다. 필요에 따라 크기가 조정된 이미지를 원하는 이미지로 나중에 바꿀 수 있습니다.

이러한 로고는 목록의 여러 위치에서 사용됩니다.

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 스크린샷을 추가합니다. 3개 이상의 스크린샷이 필요하며 최대 5개까지 추가할 수 있습니다. 모든 스크린샷은 1280x720 픽셀이고 PNG 형식이어야 합니다.

#### <a name="videos"></a>동영상

필요에 따라 제품을 설명하는 비디오를 4개까지 추가할 수 있습니다. 비디오는 외부 사이트에서 호스트되어야 합니다. 각 비디오에 대해 비디오의 이름, 주소 및 비디오의 썸네일 이미지(1280x720 픽셀)를 입력합니다.

더 많은 마켓플레이스 목록 리소스는 [마켓플레이스 제안 목록 모범 사례](../gtm-offer-listing-best-practices.md)를 참조하세요.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="availability"></a>가용성

이 페이지에서는 제품을 제공하는 위치 및 방법을 정의할 수 있습니다.

### <a name="markets"></a>시장

제안을 사용할 수 있는 지역/국가를 지정하려면 **Edit markets**(지역/국가 편집)를 선택하여 **Market selection**(지역/국가 선택) 팝업 창을 표시합니다.

지역/국가를 하나 이상 선택합니다. **모두 선택** 을 선택하여 가능한 모든 지역/국가에서 제안을 사용할 수 있도록 하거나, 원하는 특정 지역/국가만 선택합니다. 완료되면 **저장** 을 선택합니다.

여기에서 선택한 항목은 신규 취득에만 적용됩니다. 사용자가 특정 지역/국가에 앱을 이미 보유하고 있고 나중에 해당 지역/국가를 제거하는 경우 해당 지역/국가에 제안이 이미 있는 사용자는 계속 사용할 수 있지만 해당 지역/국가의 신규 고객은 제안을 얻을 수 없습니다.

> [!IMPORTANT]
> 이러한 요구 사항이 여기 또는 파트너 센터에 나열되지 않은 경우에도 현지 법적 요건을 충족할 책임은 게시자에게 있습니다. 모든 지역/국가를 선택하는 경우에도 현지 법률, 제한 또는 기타 요인으로 인해 특정 제안이 일부 국가 및 지역에 나열되지 않을 수 있습니다.

### <a name="preview-audience"></a>미리 보기 대상 그룹

제품을 광범위한 Marketplace 제품에 라이브로 게시하기 전에 먼저 제한된 **미리 보기 대상 그룹** 에서 사용할 수 있도록 해야 합니다. 여기에서 **숨기기 키**(소문자 및/또는 숫자만 사용하는 임의의 문자열)를 입력합니다. 미리 보기 대상 그룹의 멤버는 이 숨기기 키를 토큰으로 사용하여 Marketplace에서 제품의 미리 보기를 볼 수 있습니다.

그런 다음, 제품을 출시하고 미리 보기 제한을 제거할 준비가 되면 **숨기기 키** 를 제거하고 다시 게시합니다.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="technical-configuration"></a>기술 구성

이 페이지에서는 제품에 연결하는 데 사용되는 기술 세부 정보를 정의합니다. 이 연결을 통해 최종 고객을 위한 제품을 프로비저닝할 수 있습니다.

제안에 대해 제출된 확장은 [Technical Validation Checklist](/dynamics365/business-central/dev-itpro/developer/devenv-checklist-submission)(기술 유효성 검사 검사 목록)에 지정된 요구 사항을 충족해야 합니다.

### <a name="file-upload"></a>파일 업로드

이전에 **추가 기능** 을 선택한 경우 여기에서 종속성이 있는 모든 확장에 대한 패키지 파일과 함께 제안의 패키지 파일을 업로드합니다.

#### <a name="extension-package-file"></a>확장 패키지 파일

제품에 대한 확장 패키지 파일(.app) 파일을 업로드합니다.

#### <a name="library-extension-package-file"></a>라이브러리 확장 패키지 파일

제품을 마켓플레이스에 게시되지 않는 다른 확장과 함께 설치해야 하는 경우에 필요합니다. 이 경우 해당 .app 파일을 여기에 업로드합니다.

>[!NOTE]
>종속성 패키지 파일은 더 이상 사용되지 않습니다. 대신 라이브러리 확장 패키지 파일을 업로드하세요.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="supplemental-content"></a>추가 콘텐츠

이 페이지에서는 제안의 유효성을 검사하는 데 도움이 되는 추가 정보를 제공할 수 있습니다. 이 정보는 고객에게 표시되거나 마켓플레이스에 게시되지는 않습니다.

### <a name="target-release"></a>대상 릴리스

솔루션이 대상으로 하는 Microsoft Dynamics Business Central 릴리스인 **현재**, **다음 주** 또는 **다음 부** 를 표시합니다. 이 정보를 사용하여 Microsoft에서 솔루션을 적절하게 테스트할 수 있습니다.

### <a name="supported-editions"></a>지원되는 버전

제품에 Premium 버전의 Microsoft Dynamics 365 Business Central이 필요한 경우 **Premium** 만 선택합니다. 그렇지 않으면 **Essentials** 및 **Premium** 을 모두 선택합니다.

### <a name="key-usage-scenario"></a>주요 사용 시나리오

제품의 주요 사용 시나리오를 나열하는 PDF 파일을 업로드합니다. Microsoft가 마켓플레이스에서 제품을 승인하기 전에 게시자의 유효성 검사 팀에서 여기에 나열된 모든 시나리오를 확인할 수 있습니다.

### <a name="test-accounts"></a>테스트 계정

인증 팀이 제품을 제대로 검토하기 위해 테스트 계정이 필요한 경우 **테스트 계정** 정보를 사용하여 .pdf, .doc 또는 .docx 파일을 업로드합니다.

### <a name="app-tests-automation"></a>앱 테스트 자동화

제품이 추가 기능 앱인 경우 **앱 테스트 자동화** 파일(.app)을 업로드해야 합니다. 이 파일은 Connect 앱에는 적용되지 않습니다.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제안의 필수 섹션을 모두 완료한 후에는 포털의 오른쪽 위에서 **검토 및 게시** 를 선택합니다.

이 제품을 처음 게시하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에서 완료 상태를 확인합니다.
  - **시작되지 않음** – 섹션이 작업되지 않았으므로 완료해야 합니다.
  - **완료되지 않음** – 섹션에 수정해야 하는 오류가 있거나 추가 정보가 필요합니다. 섹션으로 돌아가서 업데이트합니다.
  - **완료됨** – 섹션이 완료되고 필요한 모든 데이터가 입력되었으며 오류가 없습니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료됨 상태여야 합니다.
- **인증 참고 사항** 섹션에서 앱을 이해하는 데 도움이 되는 추가 정보뿐만 아니라 앱이 올바르게 테스트되는지 확인하는 테스트 지침을 인증 팀에 제공합니다.
- **제출** 을 선택하여 게시할 제품을 제출합니다. 제안의 미리 보기 버전을 검토하고 승인할 수 있게 되면 메일을 보내드립니다. 제안을 공개 게시하려면 파트너 센터로 돌아가서 **Go-live**(게시)를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
