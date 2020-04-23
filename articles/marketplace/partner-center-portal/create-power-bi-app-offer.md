---
title: Power BI 앱 오퍼 만들기 - Azure 마켓플레이스
description: Power BI 앱 오퍼를 만들고 Microsoft AppSource에 게시하는 방법을 알아봅니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867592"
---
# <a name="create-a-power-bi-app"></a>Power BI 앱 만들기

> [!IMPORTANT]
> Power BI 앱 의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 포털에 대한 [Power BI 앱 오퍼 만들기의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) 지침을 따라 오퍼를 관리하세요.

이 문서에서는 Power BI 앱 오퍼를 만들고 Microsoft [AppSource](https://appsource.microsoft.com/)에 게시하는 방법을 설명합니다.

Power BI 앱 오퍼를 만들려면 먼저 파트너 센터에 상용 마켓플레이스 계정이 있어야 합니다. 아직 만들지 않은 경우 파트너 센터 계정에서 [상업용 마켓플레이스 계정 만들기를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) 참조하세요.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터에 로그인합니다.](https://partner.microsoft.com/dashboard/home)
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요를**선택합니다.

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="상업 시장 메뉴 개요" border="false":::

3. 선택 **+ 새로운 제공** > **전원 BI 응용 프로그램**. **새 오퍼** 대화 상자가 나타납니다.

> [!IMPORTANT]
> Power **BI 앱** 옵션이 표시되지&#39;없거나&#39;활성화되지 않은 경우 계정에 이 쿠폰 유형을 만들 수&#39;권한이 없습니다. 개발자 계정 등록을 포함하여 이 제안 유형에 대한 모든 [요구 사항을](create-power-bi-app-overview.md) 충족했는지&#39;확인하십시오.

### <a name="offer-id-and-alias"></a>제공 ID 및 별칭

**오퍼 ID를**입력합니다. 이 식별자는 계정의 각 오퍼에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 오퍼의 웹 주소및 Azure Resource Manager 템플릿(해당하는 경우)의 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈과 밑줄이 포함될 수 있지만 공백은 포함되지 않으며 50자로 제한됩니다. 예를 들어 **테스트-offer-1을** 입력하면 제안 웹 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`주소가 됩니다.

- 을 **선택한**후에는 오퍼 ID를 변경할 수 없습니다.

**오퍼 별칭을**입력합니다. 파트너 센터에서 쿠폰을 참조하는 데 사용되는 이름입니다.

- 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 오퍼 이름 및 기타 값과 다릅니다.
- 는 **을**선택한 후에는 변경할 수 없습니다.

이 두 값을 입력한 후 **만들기를** 선택하여 쿠폰 개요 페이지로 계속 합니다.

## <a name="offer-overview"></a>제품 개요

**제안 개요** 페이지에는 이 제안을 게시하는 데 필요한 단계(완료및 예정)와 각 단계를 완료하는 데 걸리는 시간이 시각적으로 표시됩니다.

여기에는 선택한 선택에 따라 이 오퍼에서 작업을 수행하는 링크가 포함되어 있습니다. 예를 들어:

- 제안이 초안인 경우 - [초안 오퍼 삭제](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 오퍼가 실시간 인 경우 - [오퍼 판매 중지](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 제안이 미리 보기인 경우 - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 게시자 로그아웃을 완료하지 않은 경우 - [게시 취소](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>오퍼 설정

다음 단계에 따라 쿠폰을 설정합니다.

### <a name="connect-lead-management"></a>잠재 고객 관리 연결

파트너 센터를 통해 오퍼를 시장에 게시할 때는 CRM(고객 관계 관리) 시스템에 연결해야 합니다. 이렇게 하면 다른 사람이 귀사에 관심을 표명하거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. 잠재 고객을 보내려는 잠재 고객 대상을 선택합니다. 파트너 센터는 다음 CRM 시스템을 지원합니다.

    - 고객 참여를 위한 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > CRM 시스템이 위에 나열되지 않은 경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https Endpoint를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 사용하여 고객 잠재 고객 데이터를 저장합니다. 그런 다음 데이터를 CRM 시스템으로 내보냅니다.

2. 파트너 센터에 게시할 때 잠재 고객 대상에 쿠폰을 연결합니다.
3. 잠재 고객 대상에 대한 연결이 제대로 구성되어 있는지 확인합니다. 파트너 센터에 게시한 후 연결의 유효성을 검사하고 테스트 리드를 보냅니다. 오퍼가 시작되기 전에 쿠폰을 미리 볼 때 미리 보기 환경에서 직접 쿠폰을 구입하여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객을 잃지 않도록 잠재 고객 대상에 대한 연결이 업데이트된 상태로 유지되는지 확인합니다.

다음은 몇 가지 추가 잠재 고객 관리 리소스입니다.

- [잠재 고객 관리 개요](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [잠재 고객 관리 FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [잠재 고객 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF(팝업 차단이 꺼져 있는지 확인)

다음 섹션인 속성으로 계속하기 전에 **초안 저장을** 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 마켓플레이스에서 쿠폰을 그룹화하는 데 사용되는 카테고리 및 산업, 앱 버전 및 쿠폰을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="category"></a>범주

최소 범주를 1개, 최대 3개 범주를 선택합니다. 이러한 카테고리는 적절한 마켓플레이스 검색 영역에 쿠폰을 배치하는 데 사용되며 쿠폰 세부정보 페이지에 표시됩니다. 쿠폰 설명에서 쿠폰이 이러한 범주를 지원하는 방법을 설명합니다.

### <a name="industry"></a>산업

선택적으로, 각 산업에서 최대 2개의 산업과 2개의 수직을 선택하십시오. 카테고리는 쿠폰을 표시하는 데 사용되지만 산업 및 업종은 검색 필터에 사용되며 스토어프론트에 적용됩니다. 오퍼가 특정 산업 및/또는 업종을 타겟팅하는 경우 제품 설명을 사용하여 선택한 산업 또는 업종을 지원하는 방법을 설명합니다. 오퍼가 산업별&#39;않은 경우 이 섹션을 비워 둡니다.

> [!NOTE]
> 우리는 제안 발견 경험을 개선하기 위해 새로운 산업과 업종을 소개하기 위해 노력함에 따라 일부 산업 또는 업종은 아직 Storefront에서 보이지 않을 수 있습니다. (*)로 표시된 산업 및 수직은 향후 날짜에 사용할 수 있습니다. 게시된 모든 오퍼는 키워드 검색을 통해 검색할 수 있습니다.
<p>&nbsp;

| **산업** | **하위 산업** |
| --- | --- |
| *자동차 | *자동차 |
| 농업 | *기타 - 세분화되지 않은 |
| 배포 | * 도매<br>소포 및 포장물 배송 |
| 교육 | *고등<br> 교육* 초등 및 중등 교육 / K-12<br>*도서관 및 박물관 |
| 금융 서비스 | *은행 및<br> 자본 시장* 보험 |
| 정부 | *국방 및 정보 (국가 및 공공<br> 안보라고하는 데 사용)* 공공 안전 과 정의<br>*민간 정부 |
| 헬스케어(건강이라고 불리기) | *건강 지불자<br> * 의료 제공자<br>*제약 |
| 제조 및 자원(제조라고 함) | *화학 및 농약<br> * 이산 제조<br>*에너지 |
| 소매 및 소비재(소매품이라고 함) | *소비재<br> * 소매점 |
| * 미디어 및 통신 (미디어 및 엔터테인먼트라고하는 데 사용) | *미디어 및<br> 엔터테인먼트* 통신 |
| 전문 서비스 | *법률<br> 적* 파트너 전문 서비스 |
| * 건축 및 건설 (건축 공학이라고하는 데 사용) | *기타 - 세분화되지 않은 |
| *환대 및 여행 | *호텔 및<br> 레저* 여행 및 교통<br>*레스토랑 및 음식 서비스 |
| *기타 공공부문 산업 | *임업 및<br> 낚시* 비영리 |
| *부동산 | *기타 - 세분화되지 않은 |

### <a name="legal"></a>법적 정보

#### <a name="terms-and-conditions"></a>사용 약관

사용자 지정 이용 약관을 제공하려면 **이용 약관** 상자에 최대 10,000자까지 입력합니다. 이용 약관에 더 긴 설명이 필요한 경우 찾을 수 있는 위치에 대한 단일 웹 링크를 입력합니다. 고객에게 활성 링크로 표시됩니다.

고객은 제품을 사용해 본 전에 이 약관에 동의해야 합니다.

다음 섹션인 제안 목록으로 이동하기 전에 **초안 저장을** 선택합니다.

## <a name="offer-listing"></a>오퍼 리스팅

여기에서 마켓플레이스에 표시되는 쿠폰 세부 정보를 정의합니다. 여기에는 오퍼 이름, 설명, 이미지 등이 포함됩니다.

### <a name="language"></a>언어

쿠폰이 나열될 언어를 선택합니다. 현재 **영어(미국)가** 유일한 옵션입니다.

각 언어/시장에 대한 마켓플레이스 세부 정보(예: 쿠폰 이름, 설명 및 이미지)를 정의합니다. 이 정보를 제공하려면 언어/시장 이름을 선택합니다.

> [!NOTE]
> 제안 설명이 문구로 시작하는 경우 제공 세부 사항은 &quot;영어로 할 필요가 없습니다,이 응용 프로그램은 [비 영어 언어]에서만 사용할 수 있습니다. &quot; 또한 오퍼 목록에 사용된 것과 다른 언어로 콘텐츠를 제공하는 유용한 링크를 제공하는&#39;&#39;.

### <a name="name"></a>이름

여기에 입력한 이름은 쿠폰 의 제목으로 표시됩니다. 이 필드는 **오퍼를** 만들 때 쿠폰 별칭 상자에 입력한 텍스트로 미리 채워져 있습니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표권(상표 또는 저작권 기호포함).
- 50자 를 초과할 수 없습니다.
- 이모티콘을 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

오퍼에 대한 간략한 설명을 제공합니다. 최대 100자까지 사용할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

### <a name="description"></a>설명

최대 3,000자까지 오퍼에 대한 더 긴 설명을 제공합니다. 이는 마켓플레이스 목록 개요의 고객에게 표시됩니다.

설명에 다음 중 하나 이상을 포함하십시오.

- 오디에서 제공하는 가치와 주요 이점.
- 범주 또는 산업 협회 또는 둘 다.
- 인앱 구매 기회.
- 필요한 모든 공개.

다음은 설명을 작성하기 위한 몇 가지 팁입니다.

- 설명의 처음 몇 문장에서 오퍼의 가치를 명확하게 설명합니다. 다음 항목을 포함합니다.
  - 제품에 대한 설명입니다.
  - 오퍼의 혜택을 누릴 수 있는 사용자 유형입니다.
  - 고객의 요구 또는 제안 주소 문제.
- 처음 몇 문장은 검색 결과에 표시될 수 있습니다.
- 제품을 판매하는 기능과 기능에 의존하지 마십시오. 대신 오퍼가 제공하는 가치에 초점을 맞춥니다.
- 산업별 어휘 또는 혜택 기반 표현을 사용해 보십시오.

오퍼 **설명을** 보다 매력적으로 만들려면 리치 텍스트 편집기를 사용하여 설명의 서식을 지정합니다. 리치 텍스트 편집기를 사용하면 숫자, 글머리 기호, 굵은 글꼴, 기울임꼴 및 들여쓰기를 추가하여 설명을 더 읽기 쉽게 만들 수 있습니다.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="풍부한 텍스트 편집기" border="false":::

### <a name="search-keywords"></a>검색 키워드

고객이 마켓플레이스에서 쿠폰을 찾을 수 있도록 최대 3개의 선택적 검색키워드를 입력하세요. 최상의 결과를 얻으려면 설명에 이러한 키워드를 사용하세요.

### <a name="helpprivacy-web-addresses"></a>도움말/개인 정보 웹 주소

고객이 제품을 더 잘 이해할 수 있도록 링크를 제공합니다.

#### <a name="help-link"></a>도움말 링크

고객이 오퍼에 대해 자세히 알아볼 수 있는 웹 주소를 입력합니다.

#### <a name="privacy-policy-url"></a>개인정보 처리방침 URL

조직의 개인 정보 보호 정책에 웹 주소를 입력합니다. 귀하는 귀하의 제안이 개인 정보 보호 법률 및 규정을 준수하는지 확인할 책임이 있습니다. 또한 웹사이트에 유효한 개인정보 보호정책을 게시할 책임이 있습니다.

### <a name="contact-information"></a>연락처 정보

**지원 담당자** 및 **엔지니어링**연락처의 이름, 전자 메일 및 전화 번호를 제공해야 합니다. 이 정보는 고객에게 표시되지&#39;. Microsoft에서 사용할 수 있으며 CSP(클라우드 솔루션 공급자) 파트너에게 제공될 수 있습니다.

- 지원 담당자(필수): 일반 지원 질문의 경우.
- 엔지니어링 담당자(필수): 기술 적 질문 및 인증 문제의 경우.
- CSP 프로그램 연락처(선택 사항): CSP 프로그램과 관련된 리셀러 질문의 경우.

지원 **연락처** 섹션에서 파트너가 오퍼에 대한 지원을 찾을 수 있는 **지원 웹 사이트의** 웹 주소를 제공합니다.

### <a name="supporting-documents"></a>서류 지원

PDF 형식으로 하나 이상의 최대 3개의 관련 마케팅 문서를 제공합니다. 예를 들어 백서, 브로셔, 체크리스트 또는 프레젠테이션을 예로 들 수 있습니다.

### <a name="marketplace-images"></a>마켓플레이스 이미지

오퍼와 함께 사용할 로고와 이미지를 제공합니다. 모든 이미지는 .png 형식이어야 합니다. 흐릿한 이미지는 거부됩니다.

>[!Note]
>파일을 업로드하는 데 문제가 있는 경우 로컬 네트워크가 파트너 https://upload.xboxlive.com 센터에서 사용하는 서비스를 차단하지 않는지 확인합니다.

#### <a name="store-logos"></a>스토어 로고

**작은** (48 x 48 픽셀)과 **대형** (216 x 216 픽셀)의 두 가지 크기로&#39;제품의 .png 파일을 제공합니다.

두 로고가 모두 필요하며 마켓플레이스 목록의 다른 위치에서 사용됩니다.

#### <a name="screenshots"></a>스크린샷

쿠폰의 작동 방식을 보여주는 스크린샷을 최소 1개에서 최대 5개까지 추가합니다. 각 픽셀의 크기와 .png 형식은 1280 x 720 픽셀이어야 합니다.

#### <a name="videos-optional"></a>동영상(선택 사항)

쿠폰을 보여주는 동영상을 최대 5개까지 추가할 수 있습니다. 비디오&#39;이름, 웹 주소 및 1280 x 720 픽셀 크기로 비디오의 축소판 .png 이미지를 입력합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 리스팅 리소스

오퍼 목록을 만드는 방법에 대해 자세히 알아보려면 [제안 목록 모범 사례를](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)참조하세요.

## <a name="technical-configuration"></a>기술 구성

Power BI 서비스에서 앱을 프로덕션 으로 승격하고 고객이 앱을 설치할 수 있는 Power BI 앱 설치 관리자 링크를 제공합니다. 자세한 내용은 [Power BI에서 대시보드 및 보고서가 있는 앱 게시를](https://docs.microsoft.com/power-bi/service-create-distribute-apps)참조하십시오.

## <a name="supplemental-content"></a>추가 콘텐츠

오퍼의 유효성을 검증하는 데 도움이 되는 추가 정보를 제공하세요. 이 정보는 고객에게 표시되지&#39;마켓플레이스에 게시되지 않습니다.

### <a name="validation-assets"></a>유효성 검사 자산

선택적으로 Microsoft 유효성 검사 팀이 앱을 구성, 연결 및 테스트하는 데 도움이 되는 지침(최대 3,000자)을 추가합니다. 데이터 연결 옵션을 테스트하는 데 사용할 수 있는 일반적인 구성 설정, 계정, 매개 변수 또는 기타 정보를 포함합니다. 이 정보는 유효성 검사 팀에서만 볼 수 있으며 유효성 검사 목적으로만 사용됩니다.

## <a name="review-and-publish"></a>검토 및 게시

오퍼의 모든 필수 섹션을 완료한&#39;후 제안서를 제출하여 검토하고 게시할 수 있습니다.

포털의 오른쪽 상단 모서리에서 **검토 및 게시를**선택합니다.

리뷰 페이지에서 다음을 수행할 수 있습니다.

- 오퍼의 각 섹션에 대한 완료 상태를 참조하십시오. 오퍼의 모든 섹션이 완료로 표시될 때까지 게시할 수&#39;.
  - **시작되지 않음** - 섹션이 시작되지 않았으며 완료해야 합니다.
  - **불완전** - 섹션에 수정해야 하거나 추가 정보를 제공해야 하는 오류가 있습니다. 지침은 이 문서의 위쪽 섹션을 참조하십시오.
  - **완료** - 섹션에 필요한 모든 데이터가 있으며 오류가 없습니다. 오퍼를 제출하려면 먼저 오퍼의 모든 섹션이 완료되어야 합니다.
- 앱이 올바르게 테스트되었는지 확인하기 위해 인증 팀에 테스트 지침을 제공합니다. 또한 제안을 이해하는 데 도움이 되는 추가 메모를 제공하십시오.

게시를 위한 제안을 제출하려면 **게시를**선택합니다.

제안의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일이 전송됩니다. 오퍼를 공개(또는 비공개 오퍼의 경우 개인 대상에게 게시하려면 파트너 센터로 이동 **라이브)를**선택합니다.
