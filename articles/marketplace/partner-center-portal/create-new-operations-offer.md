---
title: Microsoft AppSource에서 운영 제안의 Dynamics 365을 만듭니다.
description: Microsoft AppSource에서 운영 제안의 Dynamics 365을 만드는 방법입니다. AppSource 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 제품을 나열 하거나 판매 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: bcb7f2d008c54981223b4839f9a750e6ee9ac5cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97355151"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 제품 만들기

이 문서에서는 운영 제안의 새 Dynamics 365을 만드는 방법을 설명 합니다. [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations)는 고급 재무, 운영, 제조 및 공급망 관리를 지원하는 ERP(전사적 자원 관리)입니다. Dynamics 365 for Operations에 대한 모든 제품은 인증 프로세스를 통과해야 합니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만듭니다](create-account.md)(아직 만들지 않은 경우). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

>[!NOTE]
> 제품을 게시 한 후에는 게시에 대 한 제품을 다시 제출 하 고 파트너 센터 및 온라인 스토어 에서만 제품을 편집할 수 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Dynamics 365 for Operations** 를 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-dynamics-365-operations.png)

## <a name="new-offer"></a>새 제안

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
- 게시자 ID와 결합 된 제품 ID의 길이는 40 자 미만 이어야 합니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없습니다. 예를 들어 게시자 ID가이 `testpublisherid` 고 **테스트-1-1** 을 입력 하는 경우 제품 웹 주소는가 됩니다 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- **만들기** 를 선택한 후에는이 ID를 변경할 수 없습니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
- **만들기** 를 선택한 후에는이 이름을 변경할 수 없습니다.

**만들기** 를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="alias"></a>Alias

파트너 센터 내 에서만이 제품을 참조 하는 데 사용할 설명이 포함 된 이름을 입력 합니다. 이 이름 (제품을 만들 때 입력 한 내용을 미리 입력 함)은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름과는 다릅니다. 제품 이름을 나중에 업데이트 하려면 [제품 목록](#offer-listing) 페이지로 이동 합니다.

### <a name="setup-details"></a>설정 정보

**잠재 고객이이 제품을 나열 하는 것과 상호 작용할** 수 있도록 하려면이 제안에 사용할 옵션을 선택 합니다.

- **지금 받기 (무료)** – 고객에 게 제품을 무료로 나열 합니다.
- **무료 평가판 (목록)** – 무료 평가판 링크를 사용 하 여 고객에 게 제품을 나열 합니다. 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다.

    > [!NOTE]
    > 평가판 링크를 통해 애플리케이션에서 수신하는 토큰은 앱에서 자동으로 계정을 만드는 Azure AD(Active Directory)를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용하는 인증에 Microsoft 계정이 지원되지 않습니다.

- **문의처** – CRM (고객 관계 관리) 시스템을 연결 하 여 고객 연락처 정보를 수집 합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

### <a name="test-drive"></a>시험 사용

시험 사용은 "구입 전 체험" 옵션을 제공하여 잠재적인 고객에게 제품을 선보임으로써 구매 전환율을 높이고 적절한 잠재 고객을 창출할 수 있는 유용한 방법입니다. 자세히 알아보려면 [테스트 드라이브인 무엇 인가요?](../what-is-test-drive.md)를 시작 합니다.

일정 기간 동안 시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

### <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [잠재 고객 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조하세요.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 Marketplace에서 제품을 그룹화하는 데 사용되는 범주와 산업, 앱 버전 및 제품을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="categories"></a>범주

범주 및 하위 범주를 선택 하 여 제품을 적절 한 마켓플레이스 검색 영역에 넣습니다. 제품 설명에서 제품이 해당 범주를 지원하는 방법을 설명해야 합니다. 선택:

- 기본 및 보조 범주 (선택 사항)를 포함 하 여 적어도 하나 이상의 범주입니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 제안에 적용 되는 하위 범주가 없으면 **해당 없음** 을 선택 합니다.

[제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 범주 및 하위 범주의 전체 목록을 참조 하세요.

### <a name="industries"></a>산업

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>앱 버전

제품의 버전 번호를 입력합니다. 이 버전은 제품의 세부 정보 페이지에 표시됩니다.

### <a name="terms-and-conditions"></a>사용 약관

사용자 고유의 법률적 사용 약관을 여기에 제공합니다. 사용 약관을 확인할 수 있는 주소를 제공할 수도 있습니다. 고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="offer-listing"></a>제품 목록

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details such as offer name, description, and images for each language/market. Select the language/market name to provide this info.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> 제품 목록 세부 정보를 한 언어로만 제공 합니다. "이 애플리케이션은 [영어가 아닌 언어]로만 제공됩니다."라는 문구로 시작하는 경우 영어로 작성되지 않아도 됩니다. 또한 제품 목록 콘텐츠에 사용 되는 것 이외의 언어로 콘텐츠를 제공 하는 *데 유용한 링크 URL* 을 제공 하는 것도 허용 됩니다.

다음은 Microsoft AppSource에서 제공 정보를 표시 하는 방법의 예입니다. 나열 된 가격은 예를 들기 위한 것 이며 실제 비용을 반영 하지는 않습니다.
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="이 제품이 Microsoft AppSource 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 로고
2. 제품
3. 범주
4. 산업
5. 지원 주소 (링크)
6. 사용 약관
7. 개인 정보 보호 정책
8. Offer name
9. 설명
10. 스크린샷/비디오

### <a name="marketplace-details"></a>마켓플레이스 세부 정보

여기에 입력 하는 **이름은** 고객에 게 제품 목록의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 에 입력한 텍스트로 미리 채워져 있지만 이 값을 변경할 수 있습니다. 이 이름은 상표로 등록될 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50자를 초과할 수 없으며 이모지는 포함할 수 없습니다.

**검색 결과 요약** 에 대해 최대 100 자의 제품에 대 한 간단한 설명을 제공 합니다. 이 설명은 Marketplace 검색 결과에서 사용할 수 있습니다.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

필요에 따라 고객이 marketplace에서 제품을 찾을 수 있도록 최대 3 개의 **검색 키워드** 를 입력할 수 있습니다. 최상의 결과를 얻기 위해 이 키워드를 설명에도 사용합니다.

**앱이 작동 하는 제품** 을 고객에 게 알릴 수 있도록 하려면 최대 3 개의 제품 이름을 입력 합니다.

### <a name="helpprivacy-urls"></a>도움말/개인 정보 보호 URL

고객이 제품에 대해 자세히 알아볼 수 있는 앱 (URL) **에 대 한 도움말 링크** 를 입력 합니다. 도움말 URL은 지원 URL과 같을 수 없습니다.

조직의 개인 정보 보호 정책에 대 한 **개인 정보 취급 방침 링크** (URL)를 입력 합니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공하는 것은 게시자의 책임입니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처** 및 **엔지니어링 연락처** 의 이름, 메일 및 전화번호를 제공합니다. 이 정보는 고객에 게 표시 되지 않지만 Microsoft에서 제공 되며 CSP 파트너에 게 제공 될 수 있습니다.

**지원 연락처** 섹션에서 CSP 파트너가 제품에 대한 지원을 찾을 수 있는 **지원 URL** 을 제공합니다. 지원 URL은 도움말 URL과 같을 수 없습니다.

### <a name="supporting-documents"></a>지원 문서

PDF 형식의 백서, 브로슈어, 검사 목록, 프레젠테이션 등의 관련 마케팅 문서를 하나 이상(3개까지) 제공합니다.

### <a name="marketplace-media"></a>마켓플레이스 미디어

고객에 게 제품을 표시할 때 사용할 로고 및 이미지를 제공 합니다. 모든 이미지는 PNG 형식이어야 합니다.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인하세요.

#### <a name="logos"></a>로고

**큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 다른 필요한 크기를 만듭니다. 필요에 따라 나중에 다른 이미지로이를 바꿀 수 있습니다.

이러한 로고는 목록의 다른 위치에서 사용 됩니다.

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 스크린샷을 추가합니다. 스크린샷은 하나 이상 필요하며 최대 5 개까지 추가할 수 있습니다. 모든 스크린샷은 1280 x 720 픽셀 및 PNG 형식 이어야 합니다.

#### <a name="videos"></a>동영상

필요에 따라 제품을 설명하는 비디오를 4개까지 추가할 수 있습니다. 비디오는 외부 사이트에서 호스팅되어야 합니다. 각 항목에 대해 비디오의 이름, 주소 및 비디오의 미리 보기 이미지 (1280 x 720 픽셀)를 입력 합니다.

리소스를 나열하는 추가 마켓플레이스는 [마켓플레이스 제품 목록에 대한 모범 사례](../gtm-offer-listing-best-practices.md)를 참조하세요.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="availability"></a>가용성

이 페이지에서는 제품을 제공하는 위치 및 방법을 정의할 수 있습니다.

### <a name="markets"></a>시장

제품을 사용할 수 있는 시장을 지정 하려면 시장 **편집** 을 선택 하 여 **시장 선택** 팝업 창을 표시 합니다.

하나 이상의 시장을 선택 합니다. **모두 선택** 을 선택 하 여 가능한 모든 시장에서 제품을 사용할 수 있도록 하거나 원하는 특정 시장을 선택 합니다. 완료되면 **저장** 을 선택합니다.

여기에서 선택한 항목은 새로운 합병에만 적용 됩니다. 사용자가 특정 시장에 앱을 이미 보유 하 고 있으며 나중에 해당 시장을 제거 하는 경우 해당 시장에서 제품이 이미 있는 사용자는 계속 사용할 수 있지만 해당 시장의 새 고객이 제품을 얻을 수는 없습니다.

> [!IMPORTANT]
> 이러한 요구 사항이 여기 또는 파트너 센터에 나열되지 않은 경우에도 현지 법적 요건을 충족할 책임은 게시자에게 있습니다. 모든 시장, 지역 법률, 제한 또는 기타 요인을 선택 하더라도 일부 국가 및 지역에는 특정 제품이 나열 되지 않을 수 있습니다.

### <a name="preview-audience"></a>미리 보기 대상 그룹

제품을 광범위한 Marketplace 제품에 라이브로 게시하기 전에 먼저 제한된 **미리 보기 대상 그룹** 에서 사용할 수 있도록 해야 합니다. 여기에서 **숨기기 키**(소문자 및/또는 숫자만 사용하는 임의의 문자열)를 입력합니다. 미리 보기 대상 그룹의 멤버는 이 숨기기 키를 토큰으로 사용하여 Marketplace에서 제품의 미리 보기를 볼 수 있습니다.

그런 다음, 제품을 출시하고 미리 보기 제한을 제거할 준비가 되면 **숨기기 키** 를 제거하고 다시 게시합니다.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="technical-configuration"></a>기술 구성

이 페이지에서는 제품에 연결하는 데 사용되는 기술 세부 정보를 정의합니다. 이 연결을 통해 최종 고객을 위한 제품을 프로비저닝할 수 있습니다.

### <a name="solution-identifier"></a>솔루션 식별자

솔루션에 대한 솔루션 식별자(GUID)를 제공합니다.

솔루션 식별자를 찾으려면:

1. Microsoft Dynamics LCS(수명 주기 서비스)에서 **솔루션 관리** 를 선택합니다.
2. 솔루션을 선택한 다음 **패키지 개요** 에서 **솔루션 식별자** 를 찾습니다. 식별자가 비어 있으면 **편집** 을 선택하고 패키지를 다시 게시한 후 다시 시도하세요.

### <a name="release-version"></a>릴리스 버전

이 솔루션이 작동하는 Dynamics 365 for Finance and Operations의 버전을 선택합니다.

계속하기 전에 **초안 저장** 을 선택합니다.

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing.
-->
## <a name="supplemental-content"></a>추가 콘텐츠

이 페이지에서는 제품의 유효성을 검사 하는 데 도움이 되는 추가 정보를 제공할 수 있습니다. 이 정보는 고객에게 표시되거나 Marketplace에 게시되지는 않습니다.

### <a name="validation-assets"></a>유효성 검사 자산

이 섹션에서 [CAR(사용자 지정 분석 보고서)](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report)을 업로드합니다. 이 보고서는 미리 정의된 모범 사례 규칙 집합을 기반으로 사용자 지정 및 확장 모델을 분석하여 생성됩니다.

이 파일은 .xls 또는 .xlsx 형식이어야 합니다. 두 개 이상의 보고서가 있는 경우 모든 보고서를 포함하는 .zip 파일을 업로드할 수 있습니다.

### <a name="does-solution-include-localizations"></a>솔루션에 지역화 버전이 포함되어 있나요?

솔루션에서 로컬 표준 및 정책을 사용하도록 설정하는 경우 **예** 를 선택합니다(예: 다른 국가/지역에 필요한 다른 급여 규칙을 수용하는 경우). 그렇지 않은 경우 **아니요** 를 선택합니다.

### <a name="does-solution-enable-translations"></a>솔루션에서 번역이 가능한가요?

솔루션의 텍스트를 다른 언어로 번역할 수 있는 경우 **예** 로 응답합니다. 그렇지 않은 경우 **아니요** 를 선택합니다.

계속하기 전에 **초안 저장** 을 선택합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제품의 모든 필수 섹션을 완료 한 후에는 포털의 오른쪽 위 모서리에서 **검토 및 게시** 를 선택 합니다.

이 제품을 처음 게시하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에서 완료 상태를 확인합니다.
    - **시작 되지 않음** – 섹션이 작업 되지 않았으므로 완료 해야 합니다.
    - **불완전** – 섹션에 수정 해야 하는 오류가 있거나 추가 정보가 필요 합니다. 섹션으로 돌아가서 업데이트합니다.
    - **Complete** – 섹션이 완료 되 고 필요한 모든 데이터가 제공 되었으며 오류가 발생 하지 않습니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료됨 상태여야 합니다.
- **인증 참고 사항** 섹션에서 앱을 이해하는 데 도움이 되는 추가 정보뿐만 아니라 앱이 올바르게 테스트되는지 확인하는 테스트 지침을 인증 팀에 제공합니다. 테스트 지침 및 첫 번째 게시를 완료 하는 방법에 대 한 자세한 내용은 [Appsource Dynamics 365 재무 및 작업 기능 유효성 검사](../dynamics-365-finance-operations-functional-validation.md)를 참조 하세요.
- **제출** 을 선택하여 게시할 제품을 제출합니다. 제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 경우 전자 메일을 보냅니다. 파트너 센터로 돌아가서 제품을 공용으로 게시 하려면 **라이브** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)