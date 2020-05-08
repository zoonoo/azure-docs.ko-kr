---
title: Microsoft 상업적 marketplace에서 Power BI 앱 제안 만들기
description: Microsoft AppSource에 대 한 Power BI 앱 제품을 만들고 게시 하는 방법을 알아봅니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 17a6b7e46341ee0231ac88b849495cfc7e46ad88
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839150"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>Microsoft AppSource에 대 한 Power BI 앱 만들기

이 문서에서는 Microsoft [Appsource](https://appsource.microsoft.com/)에 Power BI app 제품을 만들고 게시 하는 방법을 설명 합니다.

아직 수행 하지 않은 경우 시작 하기 전에 [파트너 센터에서 상업적 Marketplace 계정을 만듭니다](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) . 계정이 상업적 marketplace 프로그램에 등록 되어 있는지 확인 합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인 합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택 합니다.
3. 개요 페이지에서 **+ 새 제품** > **Power BI 서비스 앱**을 선택 합니다.

   ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-pbi-app.png)

> [!NOTE]
> 제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 상점에 표시 됩니다. 변경을 수행한 후 항상 다시 게시 해야 합니다.

> [!IMPORTANT]
> **Power BI 서비스 앱** 이 표시 되거나 사용 하도록 설정 되지 않은 경우 계정에이 제안 유형을 만들 수 있는 권한이 없습니다. 개발자 계정 등록을 포함 하 여이 제품 유형에 대 한 모든 [요구 사항을](create-power-bi-app-overview.md) 충족 했는지 확인 하세요.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력 합니다. 계정의 각 제품에 대 한 고유 식별자입니다.

- 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿에 대 한 웹 주소의 고객에 게 표시 됩니다 (해당 하는 경우).
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50 자로 제한 됩니다. 예를 들어 여기에 **테스트-1** 을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`입니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력 합니다. 파트너 센터의 제안에 사용 되는 이름입니다.

- 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다.
- **만들기**를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

**만들기** 를 선택 하 여 제품을 생성 하 고 계속 합니다.

## <a name="offer-overview"></a>제품 개요

이 페이지에서는이 제품을 게시 하는 데 필요한 단계를 시각적으로 표시 하 고 (완료 된 시간과 예정 된) 각 단계를 완료 하는 데 걸리는 시간을 표시 합니다.

사용자가 선택한 항목에 따라이 제품에 대 한 작업을 수행할 수 있는 링크가 포함 되어 있습니다. 예를 들어:

- 제안이 초안 [삭제 제안](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) 인 경우
- 제품이 라이브 인 경우 [제품 판매를 중지](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) 합니다.
- 제품이 미리 보기 상태 이면 [라이브로 전환](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval) 합니다.
- 게시자 로그 아웃을 완료 하지 않은 경우 [게시 취소](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>제품 설정

### <a name="connect-lead-management"></a>리드 관리 연결

파트너 센터를 사용 하 여 marketplace에 제품을 게시할 때 CRM (고객 관계 관리) 시스템에 연결 해야 합니다. 이를 통해 사용자가에 관심을 표시 하거나 제품을 사용 하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. 잠재 고객을 보내려는 잠재 고객 대상을 선택합니다. 파트너 센터는 다음과 같은 CRM 시스템을 지원 합니다.

    - 고객 Engagement의 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > CRM 시스템이 위에 나열 되지 않은 경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https 끝점](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 을 사용 하 여 고객 리드 데이터를 저장 합니다. 그런 다음 CRM 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시할 때 제품을 잠재 고객 대상에 연결 합니다.
3. 잠재 고객 대상에 대 한 연결이 제대로 구성 되어 있는지 확인 합니다. 파트너 센터에서 게시 한 후에는 연결의 유효성을 검사 하 고 테스트 리드를 전송 합니다. 제품을 라이브 상태로 전환 하는 동안 미리 보기 환경에서 제품을 직접 구매 하 여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객이 손실 되지 않도록 잠재 고객 대상에 대 한 연결이 업데이트 된 상태로 유지 되는지 확인 합니다.

몇 가지 추가 리드 관리 리소스는 다음과 같습니다.

- [리드 관리 개요](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [잠재 고객 관리 FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [리드 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (팝업 차단이 해제 되어 있는지 확인)

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="properties"></a>속성

이 페이지에서는 marketplace에서 제품을 그룹화 하는 데 사용 되는 범주와 산업, 앱 버전 및 제품을 지 원하는 법적 계약을 정의할 수 있습니다.

### <a name="category"></a>범주

최소 1 자에서 최대 3 개의 범주를 선택 합니다. 이러한 범주는 제품을 적절 한 마켓플레이스 검색 영역에 저장 하는 데 사용 되며 제품 세부 정보 페이지에 표시 됩니다. 제품 설명에서 제품이 이러한 범주를 어떻게 지원 하는지 설명 합니다.

### <a name="industry"></a>산업

필요에 따라 각 업계에서 최대 2 개의 산업 및 두 개의 감축할를 선택 합니다. 제품을 표시 하는 데 범주가 사용 되는 반면, 산업 및 감축할는 검색 필터에 사용 되며 Storefront에 적용 됩니다. 제품이 특정 산업 및/또는 세로를 대상으로 하는 경우 제품 설명을 사용 하 여 제품에서 선택 된 산업 또는 감축할를 지 원하는 방법을 설명 합니다. 제품이 특정 업계에 국한 되지 않는 경우이 섹션을 비워 둡니다.

> [!NOTE]
> 새로운 산업 및 감축할를 도입 하 여 제품 검색 환경을 개선 하기 위해 노력 하 고 있으므로 일부 산업 또는 감축할는 아직 Storefront에 표시 되지 않을 수 있습니다. (*)로 표시 된 산업 및 감축할는 미래의 날짜에 제공 될 예정입니다. 게시 된 모든 제안은 키워드 검색을 통해 검색할 수 있습니다.
<p>&nbsp;

| **산업** | **Subindustry** |
| --- | --- |
| * 자동차 | * 자동차 |
| 농업 | * 기타-분할 되지 않음 |
| 배포 | * 도매<br>Parcel 및 패키지 전달 |
| 교육 | *높은 교육<br> * 기본 및 보조 교육/K-12<br>* 라이브러리 및 Museums |
| 금융 서비스 | *은행 및 자본 시장<br> * 회사 |
| 정부 | *방어 및 인텔리전스 (국가 및 공용 보안 이라고 함)<br> * 공용 안전 성과<br>* Civilian 정부 |
| 의료 (상태를 호출 하는 데 사용 됨) | *상태 보험사<br> * 상태 제공자<br>* Pharmaceuticals |
| 제조 및 리소스 (제조를 호출 하는 데 사용 됨) | *화학 및 Agrochemical<br> * 불연속 제조<br>* 에너지 |
| 소매 및 소비자 상품 (소매점 이라고 함) | *소비자 상품<br> * 업체 |
| * 미디어 및 통신 (미디어 및 엔터테인먼트를 호출 하는 데 사용 됨) | *미디어 및 엔터테인먼트<br> * 통신 |
| 전문 서비스 | *법적<br> * 파트너 전문가 서비스 |
| * 아키텍처 및 생성 (아키텍처 엔지니어링 이라고 함) | * 기타-분할 되지 않음 |
| * 숙박 및 여행 | *호텔 및 레저<br> * 여행 및 교통<br>* 식당 및 음식 서비스 |
| * 기타 공공 부문 산업 | *임업 및 낚<br> 싯* 비영리 조직 |
| * 부동산 | * 기타-분할 되지 않음 |

### <a name="legal"></a>법적 정보

#### <a name="terms-and-conditions"></a>사용 약관

사용자 고유의 사용자 지정 사용 약관을 제공 하려면 사용 **약관** 상자에 최대 1만 자까지 입력 합니다. 사용 약관에 더 긴 설명이 필요한 경우 찾을 수 있는 위치에 대 한 단일 웹 링크를 입력 합니다. 고객이 활성 링크로 표시 됩니다.

고객은 이러한 약관에 동의 해야 제품을 사용해 볼 수 있습니다.

다음 섹션인 제품 목록으로 계속 진행 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="offer-listing"></a>제품 목록

여기에서 marketplace에 표시 되는 제품 세부 정보를 정의 합니다. 여기에는 제품 이름, 설명, 이미지 등이 포함 됩니다.

### <a name="language"></a>언어

제품을 나열할 언어를 선택 합니다. 현재 **영어 (미국)** 만 사용할 수 있는 옵션입니다.

각 언어/시장에 대해 마켓플레이스 세부 정보 (예: 제품 이름, 설명 및 이미지)를 정의 합니다. 이 정보를 제공 하려면 언어/시장 이름을 선택 합니다.

> [!NOTE]
> 제품 설명이 "영어가 아닌 언어 에서만 사용할 수 있습니다."로 시작 하는 경우 제품 세부 정보는 영어로 지정할 필요가 없습니다. 또한 제품 목록에 사용 된 것과 다른 언어로 콘텐츠를 제공 하는 유용한 링크도 제공 합니다.

### <a name="name"></a>속성

여기에 입력 하는 이름은 제품의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 상자에 입력 한 텍스트로 미리 채워집니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다.
- 50 자를 초과할 수 없습니다.
- Emojis를 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

제품에 대 한 간단한 설명을 제공 합니다. 이 길이는 최대 100 자까지 가능 하며 marketplace 검색 결과에 사용 됩니다.

### <a name="description"></a>설명

제품에 대 한 더 긴 설명을 최대 3000 자까지 제공 합니다. 이는 marketplace 목록 개요에서 고객에 게 표시 됩니다.

설명에 다음 중 하나 이상을 포함 합니다.

- 제품에서 제공 하는 가치 및 주요 이점입니다.
- 범주 또는 업계 연결 또는 둘 다.
- 앱 내 구매 기회.
- 모든 필수 항목입니다.

설명 작성에 대 한 몇 가지 팁은 다음과 같습니다.

- 설명의 처음 몇 문장에서 제품의 가치를 명확 하 게 설명 합니다. 다음 항목을 포함 합니다.
  - 제품에 대한 설명입니다.
  - 제품을 활용 하는 사용자의 유형입니다.
  - 고객이 요구 하거나 제품 주소를 발급 합니다.
- 검색 결과에는 처음 몇 개의 문장이 표시 될 수 있습니다.
- 제품을 판매 하기 위한 기능과 기능을 사용 하지 마세요. 대신 제품에서 제공 하는 가치에 집중 하세요.
- 산업별 어휘 또는 혜택 기반 표현을 사용해 보세요.

제품 설명을 보다 유용 하 게 사용 하려면 서식 있는 텍스트 편집기를 사용 하 여 서식 지정을 적용 합니다.

![서식 있는 텍스트 편집기 사용](./media/rich-text-editor.png)

| <center>텍스트 형식 변경 | <center>글머리 기호 또는 번호 매기기 추가 | <center>텍스트 들여쓰기 추가 또는 제거 |
| --- | --- | --- |
| <center>![서식 있는 텍스트 편집기를 사용 하 여 텍스트 형식 변경](./media/text-editor3.png) |  <center>![서식 있는 텍스트 편집기를 사용 하 여 목록 추가](./media/text-editor4.png) |  <center>![서식 있는 텍스트 편집기를 사용 하 여 들여쓰기](./media/text-editor5.png) |

### <a name="search-keywords"></a>검색 키워드

고객이 marketplace에서 제품을 찾는 데 도움이 되는 선택적 검색 키워드를 최대 3 개까지 입력 합니다. 최상의 결과를 위해 설명에 이러한 키워드를 사용 합니다.

### <a name="helpprivacy-web-addresses"></a>도움말/개인 정보 웹 주소

고객이 제품을 보다 잘 이해할 수 있도록 도와주는 링크를 제공 합니다.

#### <a name="help-link"></a>도움말 링크

고객이 제품에 대해 자세히 알아볼 수 있는 웹 주소를 입력 합니다.

#### <a name="privacy-policy-url"></a>개인 정보 취급 방침 URL

조직의 개인 정보 취급 방침에 대 한 웹 주소를 입력 합니다. 귀하는 귀하의 제품이 개인 정보 법률 및 규정을 준수 하는지 확인 해야 합니다. 웹 사이트에 유효한 개인 정보 취급 방침을 게시 하는 작업도 담당 합니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처** 와 **엔지니어링 연락처**의 이름, 전자 메일 및 전화 번호를 제공 해야 합니다. 이 정보는 고객에 게 표시 되지 않습니다. Microsoft에서 사용할 수 있으며, CSP (클라우드 솔루션 공급자) 파트너에 게 제공 될 수 있습니다.

- 지원 담당자 (필수): 일반 지원 질문의 경우
- 엔지니어링 연락처 (필수): 기술 관련 질문 및 인증 문제.
- CSP 프로그램 연락처 (선택 사항): CSP 프로그램과 관련 된 재판매인 질문의 경우

**지원 담당자** 섹션에서 파트너가 제품에 대 한 지원을 찾을 수 있는 **지원 웹 사이트** 의 웹 주소를 제공 합니다.

### <a name="supporting-documents"></a>지원 문서

하나 이상의 관련 마케팅 문서를 PDF 형식으로 제공 합니다. 예를 들어 백서, 브로슈어, 검사 목록 또는 프레젠테이션이 있습니다.

### <a name="marketplace-images"></a>Marketplace 이미지

제품에 사용할 로고 및 이미지를 제공 합니다. 모든 이미지는 .png 형식 이어야 합니다. 흐린 이미지는 거부 됩니다.

>[!NOTE]
>파일을 업로드 하는 데 문제가 있는 경우 로컬 네트워크가 파트너 센터에서 사용 하는 https://upload.xboxlive.com 서비스를 차단 하지 않는지 확인 합니다.

#### <a name="store-logos"></a>매장 로고

제품 로고의 .png 파일을 두 픽셀 크기로 제공 합니다.
- **작음** (48 x 48)
- **큼** (216 x 216)

두 로고가 모두 필요 하며 marketplace 목록의 다른 위치에서 사용 됩니다.

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 스크린샷 하나 이상 및 최대 5 개를 추가 합니다. 각각 1280 x 720 픽셀 크기와 .png 형식 이어야 합니다.

#### <a name="videos-optional"></a>비디오 (선택 사항)

제품을 설명 하는 비디오를 최대 5 개까지 추가 합니다. 비디오의 이름, 웹 주소 및 비디오의 축소판 그림 이미지를 1280 x 720 픽셀 크기로 입력 합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열 하는 추가 마켓플레이스

제품 목록을 만드는 방법에 대 한 자세한 내용은 [제품 목록 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)를 참조 하세요.

## <a name="technical-configuration"></a>기술 구성

Power BI Service의 앱을 프로덕션으로 승격 하 고 고객이 앱을 설치할 수 있도록 하는 Power BI 앱 설치 관리자 링크를 제공 합니다. 자세한 내용은 [Power BI에서 대시보드 및 보고서를 사용 하 여 앱 게시](https://docs.microsoft.com/power-bi/service-create-distribute-apps)를 참조 하세요.

## <a name="supplemental-content"></a>추가 콘텐츠

유효성을 검사 하는 데 도움이 되도록 제품에 대 한 추가 정보를 제공 합니다. 이 정보는 고객에 게 표시 되지 않거나 marketplace에 게시 됩니다.

### <a name="validation-assets"></a>유효성 검사 자산

필요에 따라 Microsoft 유효성 검사 팀이 앱을 구성, 연결 및 테스트 하는 데 도움이 되는 지침 (최대 3000 자)을 추가 합니다. 데이터 연결 옵션을 테스트 하는 데 사용할 수 있는 일반적인 구성 설정, 계정, 매개 변수 또는 기타 정보를 포함 합니다. 이 정보는 유효성 검사 팀에만 표시 되 고 유효성 검사 목적 으로만 사용 됩니다.

## <a name="review-and-publish"></a>검토 및 게시

제품의 필수 섹션을 모두 완료 한 후에는 제품을 제출 하 여 검토 하 고 게시할 수 있습니다.

포털의 오른쪽 위 모서리에서 **검토 및 게시**를 선택 합니다.

검토 페이지에서 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다. 제품의 모든 섹션이 완료로 표시 될 때까지 게시할 수 없습니다.
  - **시작 되지 않음** -섹션이 시작 되지 않았으며 완료 해야 합니다.
  - **불완전** -섹션에 수정 해야 하는 오류가 있거나 추가 정보를 제공 해야 합니다. 지침은이 문서의 앞부분에 나오는 섹션을 참조 하세요.
  - **완료** -섹션에 필요한 모든 데이터가 있고 오류가 없습니다. 제품을 제출 하려면 먼저 제품의 모든 섹션을 완료 해야 합니다.
- 인증 팀에 테스트 지침을 제공 하 여 앱이 제대로 테스트 되었는지 확인 합니다. 또한 제품을 이해 하는 데 유용한 보충 정보를 제공 합니다.

게시를 위해 제품을 제출 하려면 **게시**를 선택 합니다.

제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 시기를 알려 주는 전자 메일을 보내 드리겠습니다. 공개에 제품을 게시 하려면 (또는 개인 제품이 개인 사용자에 게 제공 하는 경우) 파트너 센터로 이동 하 여 [ **라이브]** 를 선택 합니다.
