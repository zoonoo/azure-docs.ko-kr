---
title: 파트너 센터 - Azure 마켓플레이스에서 Azure 컨테이너 오퍼 만들기
description: 이 문서에서는 Azure Marketplace용 컨테이너 오퍼를 만들고 게시하는 방법에 대해 설명합니다.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266319"
---
# <a name="create-an-azure-container-offer"></a>Azure 컨테이너 오퍼 만들기

> [!IMPORTANT]
> Azure 컨테이너 오퍼의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 [포털의 컨테이너](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) 지침에 따라 오퍼를 관리하세요.

이 문서에서는 Azure Marketplace용 컨테이너 오퍼를 만들고 게시하는 방법에 대해 설명합니다. 시작하기 전에 [파트너 센터에서 상업용 마켓플레이스 계정을 만듭니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터에](https://partner.microsoft.com/dashboard/home)로그인한 다음 상단 메뉴에서 **대시보드**를 선택합니다.
2. 왼쪽 메뉴에서 **상업용 마켓플레이스를**선택한 다음 **개요를 선택합니다.**
3. **개요** 페이지에서 **+ 새 제안을**선택한 다음 Azure **컨테이너를**선택합니다. **새 오퍼** 대화 상자가 나타납니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="파트너 센터의 개요 페이지를 그림으로 보여 줍니다. 새로운 제안 버튼과 컨설팅 서비스가 강조 표시됩니다.":::

> [!TIP]
> 오퍼가 게시된 후 파트너 센터에서 편집한 편집은 제안을 다시 게시한 후에만 상점에 표시됩니다. 변경한 후 항상 다시 게시해야 합니다.

### <a name="offer-id-and-alias"></a>제공 ID 및 별칭

**오퍼 ID를**입력합니다. 이 식별자는 계정의 각 오퍼에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 오퍼의 웹 주소와 Azure Resource Manager 템플릿(해당하는 경우)에서 고객이 볼 수 있습니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈과 밑줄이 포함될 수 있지만 공백은 포함되지 않으며 50자로 제한됩니다. 예를 들어 **테스트-offer-1을**입력하면 제안 웹 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`주소가 됩니다.
- 을 **선택한**후에는 오퍼 ID를 변경할 수 없습니다.

**오퍼 별칭을** **입력합니다.** 파트너 센터에서 쿠폰을 참조하는 데 사용되는 이름입니다.

- 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 오퍼 이름 및 기타 값과 다릅니다.
- 는 **을**선택한 후에는 변경할 수 없습니다.

계속하기 전에 **만들기를** 선택합니다.

## <a name="offer-overview"></a>제품 개요

**제안 개요** 페이지에는 이 제안을 게시하는 데 필요한 단계(완료및 예정)와 각 단계를 완료하는 데 걸리는 시간이 시각적으로 표시됩니다.

이 페이지에는 오퍼의 현재 상태에 따라 다른 링크가 표시됩니다. 다음은 그 예입니다.

- 제안이 초안인 경우 - [초안 오퍼 삭제](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 오퍼가 실시간 인 경우 - [오퍼 판매 중지](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 제안이 미리 보기인 경우 - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 게시자 로그아웃을 완료하지 않은 경우 - [게시 취소](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>오퍼 설정

다음 단계에 따라 쿠폰을 설정합니다.

### <a name="connect-lead-management--optional"></a>리드 관리 연결 – 선택 사항

파트너 센터를 통해 오퍼를 마켓플레이스에 게시할 때 CRM(고객 관계 관리) 시스템에 연결할 수 있습니다. 이렇게 하면 다른 사람이 귀사에 관심을 표명하거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. 고객 잠재 **고객을 보낼 잠재 고객 대상을 선택합니다.** 파트너 센터는 다음 CRM 시스템을 지원합니다.

- 고객 참여를 위한 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> CRM 시스템이 위에 나열되지 않은 경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https Endpoint를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 사용하여 고객 잠재 고객 데이터를 저장한 다음 CRM 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시할 때 잠재 고객 대상에 쿠폰을 연결합니다.
3. 잠재 고객 대상에 대한 연결이 제대로 구성되어 있는지 확인합니다. 파트너 센터에 게시한 후 연결의 유효성을 검사하고 테스트 리드를 보냅니다. 오퍼가 시작되기 전에 쿠폰을 미리 볼 때 미리 보기 환경에서 직접 쿠폰을 구입하여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객을 잃지 않도록 잠재 고객 대상에 대한 연결이 업데이트된 상태로 유지되는지 확인합니다.

다음은 몇 가지 추가 잠재 고객 관리 리소스입니다.

- [잠재 고객 관리 개요](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [잠재 고객 관리 FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [잠재 고객 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF(팝업 차단이 꺼져 있는지 확인)

다음 섹션인 속성으로 계속하기 전에 **초안 저장을** 선택합니다.

### <a name="properties"></a>속성

이 페이지에서는 마켓플레이스에서 쿠폰을 그룹화하는 데 사용되는 카테고리와 쿠폰을 지원하는 법적 계약을 정의할 수 있습니다.

#### <a name="category"></a>범주

최소 1개 및 최대 5개 범주를 선택합니다. 이러한 카테고리는 적절한 마켓플레이스 검색 영역에 쿠폰을 배치하는 데 사용되며 쿠폰 세부정보 페이지에 표시됩니다. 쿠폰 설명에서 쿠폰이 이러한 범주를 지원하는 방법을 설명합니다. 컨테이너는 **컨테이너** 아래에 나타나고 **컨테이너 이미지** 범주 아래에 나타납니다.

#### <a name="legal"></a>법적 정보

오퍼에 대한 이용 약관을 제공해야 합니다. 옵션에는

- Microsoft 상용 시장에 대한 표준 계약을 사용합니다.
- 사용자 고유의 이용 약관을 제공합니다.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>마이크로소프트 상용 시장에 대 한 표준 계약

우리는 상업 시장에서 거래를 용이하게하는 데 도움이 표준 계약 템플릿을 제공합니다. 고객이 한 번만 확인하고 수락하면 되는 표준 계약에 따라 솔루션을 제공하도록 선택할 수 있습니다. 사용자 지정 이용 약관을 만들지 않으려는 경우 이 옵션을 사용할 수 있습니다.

표준 계약에 대한 자세한 내용은 [Microsoft 상용 시장에 대한 표준 계약을](https://docs.microsoft.com/azure/marketplace/standard-contract)참조하십시오. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) PDF를 다운로드할 수도 있습니다(팝업 차단이 꺼져 있는지 확인).

표준 계약을 사용하려면 **Microsoft의 상용 마켓플레이스에 대한 표준 계약 사용** 확인란을 선택한 다음 **수락을**클릭합니다.

> [!NOTE]
> Microsoft 상용 마켓플레이스의 표준 계약을 사용하여 오퍼를 게시한 후에는 사용자 지정 이용 약관을 사용할 수 없습니다. 표준 계약에 따라 또는 자체 이용 약관에 따라 솔루션을 제공합니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Microsoft의 상용 마켓플레이스 에 대한 표준 계약 사용 확인란을 보여 줍니다.":::

##### <a name="your-own-terms-and-conditions"></a>사용자 고유의 이용 약관

사용자 고유의 사용자 지정 이용 약관을 제공하려면 **이용 약관** 상자에 입력하십시오. 이 상자에 텍스트 문자를 무제한으로 입력할 수 있습니다. 고객은 제품을 사용해 본 전에 이 약관에 동의해야 합니다.

다음 섹션인 제안 목록으로 이동하기 전에 **초안 저장을** 선택합니다.

## <a name="offer-listing"></a>오퍼 리스팅

이 페이지에서는 상용 마켓플레이스에 표시되는 쿠폰 세부 정보를 정의할 수 있습니다. 여기에는 제품 이름, 설명 및 이미지가 포함됩니다.

> [!NOTE]
> 오퍼 설명이 문구로 시작되는 경우 제공 세부 정보는 영어로 할 필요가 없습니다,"이 응용 프로그램은 [비 영어 언어]에서만 사용할 수 있습니다." 또한 쿠폰 목록 세부 정보에 사용된 것과 다른 언어로 콘텐츠를 제공하는 유용한 링크를 제공하는 것도 괜찮습니다.

### <a name="name"></a>속성

여기에 입력한 이름은 쿠폰 의 제목으로 표시됩니다. 이 필드는 **오퍼를** 만들 때 쿠폰 별칭 상자에 입력한 텍스트로 미리 채워져 있습니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표가 될 수 있습니다(상표 및 저작권 기호포함).
- 50자 를 초과할 수 없습니다.
- 이모티콘을 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

오퍼에 대한 간략한 설명입니다. 최대 100자까지 사용할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

### <a name="long-summary"></a>긴 요약

오퍼에 대한 자세한 설명입니다. 최대 256자까지 사용할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

### <a name="description"></a>Description

최대 3,000자까지 오퍼에 대한 더 긴 설명을 제공합니다. 이는 마켓플레이스 목록 개요의 고객에게 표시됩니다.

설명에 다음 중 하나 이상을 포함하십시오.

- 오디퍼가 제공하는 가치와 주요 이점
- 범주 또는 산업 협회 또는 둘 다
- 인앱 구매 기회
- 필요한 모든 공개

다음은 설명을 작성하기 위한 몇 가지 팁입니다.

- 설명의 처음 몇 문장에서 오퍼의 가치를 명확하게 설명합니다. 다음 항목을 포함합니다.
  - 제품에 대한 설명입니다.
  - 오퍼의 혜택을 누릴 수 있는 사용자 유형
  - 고객의 요구 또는 제안 주소 문제.
- 처음 몇 문장은 검색 결과에 표시될 수 있습니다.
- 제품을 판매하는 기능과 기능에 의존하지 마십시오. 대신 오퍼가 제공하는 가치에 초점을 맞춥니다.
- 산업별 어휘 또는 혜택 기반 표현을 사용해 보십시오.

쿠폰 **설명을** 더욱 매력적으로 만들려면 고급 텍스트 편집기를 사용하여 설명의 서식을 지정합니다. 번호 매기기, 글머리 기호, 굵은 기울임꼴 및 들여쓰기를 사용하여 설명을 더 읽기 쉽게 만들 수 있습니다.

:::image type="content" source="media/text-editor2.png" alt-text="리치 텍스트 편집기입니다." border="false" :::

- 이 드롭다운을 사용하여 단락 스타일을 텍스트에 적용합니다.

    :::image type="content" source="media/text-editor3.png" alt-text="는 가재 텍스트 편집기에서 텍스트 스타일 컨트롤을 보여 줍니다." border="false":::

- 이러한 아이콘을 사용하여 텍스트에 번호 매기기 또는 글머리 기호를 적용합니다.

     :::image type="content" source="media/text-editor4.png" alt-text="전체 텍스트 편집기에서 글머리 기호 및 숫자 목록 컨트롤을 보여 줍니다." border="false":::

- 이러한 아이콘을 사용하여 텍스트에 대한 들여쓰기를 추가하거나 제거합니다.

    :::image type="content" source="media/text-editor5.png" alt-text="는 하위 텍스트 편집기에서 들여쓰기 컨트롤을 보여 줍니다." border="false":::

#### <a name="privacy-policy-link"></a>개인정보 처리방침 링크

조직의 개인 정보 보호 정책의 웹 주소를 입력합니다. 귀하는 귀하의 제안이 개인 정보 보호 법률 및 규정을 준수하는지 확인할 책임이 있습니다. 또한 웹사이트에 유효한 개인정보 보호정책을 게시할 책임이 있습니다.

#### <a name="useful-links"></a>유용한 링크

오퍼에 대한 추가 온라인 문서를 제공합니다. 최대 25개의 링크를 추가할 수 있습니다. 링크를 추가하려면 **+ 링크 추가를** 선택한 다음 다음 필드를 완료합니다.

- **제목** - 고객은 쿠폰의 세부정보 페이지에서 이를 볼 수 있습니다.
- **링크(URL)** – 고객이 온라인 문서를 볼 수 있도록 링크를 입력합니다. 링크는 http:// 또는 https:// 시작해야 합니다.

### <a name="contact-information"></a>연락처 정보

**지원 담당자** 및 **엔지니어링**연락처의 이름, 전자 메일 및 전화 번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있습니다. 또한 CSP(클라우드 솔루션 공급자) 파트너에게도 제공될 수 있습니다.

- 지원 담당자(필수): 일반 지원 질문의 경우.
- 엔지니어링 담당자(필수): 기술 적 질문 및 인증 문제의 경우.
- CSP 프로그램 연락처(선택 사항): CSP 프로그램과 관련된 리셀러 질문의 경우.

지원 **연락처** 섹션에서 파트너가 글로벌 Azure, Azure 정부 또는 둘 다에서 서비스를 사용할 수 있는지 여부에 따라 오퍼에 대한 지원을 찾을 수 있는 **지원 웹 사이트를** 제공합니다.

**CSP 프로그램 연락처** 섹션에서 CSP 파트너가 오퍼에 대한 마케팅 자료를 찾을 수 있는 링크(CSP **프로그램 마케팅 자료)를** 제공합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 리스팅 리소스

오퍼 리스팅 을 만드는 방법에 대해 자세히 알아보려면 [쿠폰 목록 모범 사례를](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices) 참조하세요.

### <a name="marketplace-images"></a>마켓플레이스 이미지

오퍼와 함께 사용할 로고와 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다. 흐릿한 이미지는 거부됩니다.

#### <a name="store-logos"></a>스토어 로고

 다음 네 픽셀 크기 각각에 오퍼 로고의 PNG 파일을 제공합니다.

- **소형(48** x 48)
- **미디엄** (90 X 90)
- **라지** (216 x 216)
- **Wide** 와이드(255× 115)

네 개의 로고가 모두 필요하며 마켓플레이스 목록의 다른 위치에서 사용됩니다.

#### <a name="screenshots-optional"></a>스크린샷(선택 사항)

쿠폰의 작동 방식을 보여주는 스크린샷을 최대 5개까지 추가합니다. 각각 크기와 PNG 형식의 1280 x 720 픽셀이어야 합니다.

#### <a name="videos-optional"></a>동영상(선택 사항)

쿠폰을 보여주는 동영상을 최대 5개까지 추가할 수 있습니다. 1280 x 720 픽셀 크기로 비디오 의 이름, 웹 주소 및 동영상의 축소판 PNG 이미지를 입력합니다.

#### <a name="offer-examples"></a>제안 예제

다음 예제에서는 오퍼 목록 필드가 오퍼의 여러 위치에 표시되는 방식을 보여 주며

Azure 마켓플레이스의 **제안 목록** 페이지를 보여 주며, 다음을 보여 드립니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Azure 마켓플레이스의 제안 목록 페이지를 보여 줍니다." :::

Azure 마켓플레이스의 검색 결과를 보여 주며, 다음을 보여 주며,

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Azure 마켓플레이스의 검색 결과를 보여 줍니다.":::

Azure 포털에서 **제안 목록** 페이지를 보여 드립니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Azure 포털에서 제안 목록 페이지를 보여 줍니다.":::

Azure 포털의 검색 결과를 보여 주며 다음과 같은 결과를 보여 드립니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Azure 포털의 검색 결과를 보여 줍니다.":::

## <a name="preview"></a>미리 보기

미리 보기 탭에서 제품을 라이브로 게시하기 전에 쿠폰의 유효성을 검사하기 위해 제한된 **미리보기 잠재고객을** 선택할 수 있습니다.

> [!IMPORTANT]
> **미리 보기에서**쿠폰을 본 후 **라이브 로이동을** 선택하여 대중에게 쿠폰을 게시해야 합니다.

Azure 구독 ID GUID를 사용하여 미리 보기 대상을 지정하고 각 멤버쉽에 대한 선택적 설명을 지정합니다. 이러한 필드 중 어느 것도 고객이 볼 수 없습니다.

> [!NOTE]
> Azure 포털의 구독 페이지에서 Azure 구독 ID를 찾을 수 있습니다.

개별적으로(최대 10개) 또는 CSV 파일(최대 100개)을 업로드하여 하나 이상의 Azure 구독 ID를 추가합니다. 이러한 구독 아이디를 추가하면 쿠폰이 라이브로 게시되기 전에 쿠폰을 미리 볼 수 있는 사람을 결정할 수 있습니다. 쿠폰이 이미 라이브 인 경우 미리 보기 대상을 선택하여 오퍼의 변경 내용 또는 업데이트를 테스트할 수 있습니다.

> [!NOTE]
> 미리 보기 대상은 비공개 대상과 다릅니다. **미리 보기** 대상은 마켓플레이스에 게재되기 전에 모든 오퍼 플랜을 보고 확인할 수 있으며, 여기에는 **비공개** 잠재고객에게만 게시되는 플랜(가용성 탭에 설정됨)이 포함됩니다.

계속하기 전에 **초안 저장을** 선택합니다.

### <a name="plan-overview"></a>계획 개요

이 탭을 사용하면 동일한 오퍼 내에서 다양한 요금제 옵션을 제공할 수 있습니다. 이러한 계획은 이전에 SCO 또는 재고 유지 단위로 언급되었습니다. 계획은 전역 클라우드, 정부 클라우드 및 계획에서 참조하는 이미지와 같이 사용할 수 있는 클라우드에 따라 다를 수 있습니다. 상용 마켓플레이스에 쿠폰을 나열하려면 하나 이상의 요금제 설정을 해야 합니다.

계획을 만든 후 **계획 개요** 탭에 다음이 표시됩니다.

- 계획 이름
- 가격 책정 모델
- 클라우드 가용성(글로벌 또는 정부)
- 현재 게시 상태
- 사용 가능한 모든 작업

계획 개요에서 사용할 수 있는 작업은 계획의 현재 상태에 따라 다릅니다. 해당 기능은 아래와 같습니다.

- **초안 삭제** – 계획 상태가 초안인 경우.
- **판매 중단 계획** - 계획 상태가 실시간으로 게시된 경우.

#### <a name="create-new-plan"></a>새 계획 만들기

**새 계획 만들기를 선택합니다.** **새 계획** 대화 상자가 나타납니다.

계획 **ID** 상자에서 이 오퍼의 각 계획에 대한 고유한 계획 식별자를 만듭니다. 이 ID는 제품의 웹 주소에 있는 고객에게 표시됩니다. 소문자와 숫자, 대시 또는 밑줄만 사용하고 최대 50자만 사용합니다.

> [!NOTE]
> 계획 ID **만들기를**선택한 후에는 변경할 수 없습니다.

계획 **이름** 상자에 이 계획의 이름을 입력합니다. 고객은 오퍼 내에서 선택할 플랜을 결정할 때 이 이름을 볼 수 있습니다. 이 오퍼의 각 계획에 대해 고유한 이름을 만듭니다. 예를 들어 Windows Server **2016** 및 Windows Server **2019**계획과 함께 **Windows** Server의 오퍼 이름을 사용할 수 있습니다.

### <a name="plan-setup"></a>계획 설정

이 탭을 사용하면 계획이 사용할 수 있는 클라우드를 선택할 수 있습니다. 이 탭의 답변은 다른 탭에 표시되는 필드에 영향을 줍니다.

#### <a name="cloud-availability"></a>클라우드 가용성

계획은 하나 이상의 클라우드에서 사용할 수 있어야 합니다.

상용 마켓플레이스를 사용하는 모든 글로벌 Azure 지역의 고객이 계획을 사용할 수 있도록 **Azure Global** 옵션을 선택합니다. 자세한 내용은 [지리적 가용성 및 통화 지원을](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)참조하십시오.

Azure [**정부 클라우드**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) 옵션을 선택하여 솔루션을 여기에 표시합니다. 이 클라우드는 미국 연방, 주, 지방 또는 부족 정부 기관뿐만 아니라 서비스를 제공할 자격이 있는 파트너의 고객에 대한 제어된 액세스 권한이 있는 정부 커뮤니티 클라우드입니다. 퍼블리셔는 이 클라우드 커뮤니티에 대한 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure 정부는 물리적으로 격리된 데이터 센터 및 네트워크를 사용합니다(미국에만 있음).

Azure 정부에 [게시하기](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) 전에 결과가 다를 수 있으므로 해당 영역 내에서 솔루션을 테스트하고 확인합니다. 솔루션을 만들고 테스트하려면 [Microsoft Azure 정부 평가판에서](https://azure.microsoft.com/global-infrastructure/government/request/)평가판 계정을 요청합니다.

> [!NOTE]
> 계획이 게시되고 특정 클라우드에서 사용할 수 있게 되면 해당 클라우드를 제거할 수 없습니다.

#### <a name="azure-government-cloud-certifications"></a>Azure 정부 클라우드 인증

이 옵션은 **Azure 정부 클라우드가** 클라우드 가용성 에서 선택된 경우에만 볼 수 **있습니다.**

Azure 정부 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 및 CJIS.

이러한 프로그램에 대한 인증을 표시하려면 해당 프로그램을 설명하는 링크를 최대 100개까지 제공할 수 있습니다. 이들은 직접 또는 자신의 웹 사이트에 프로그램에 목록에 대한 링크가 될 수 있습니다. 이러한 링크는 Azure 정부 고객에게만 표시됩니다.

## <a name="plan-listing"></a>계획 목록

이 탭에는 현재 오퍼 내의 각 다른 계획에 대한 특정 정보가 표시됩니다.

### <a name="plan-name"></a>계획 이름

이 이름은 계획을 만들 때 제공한 이름으로 미리 채워져 있습니다. 필요에 따라 이 이름을 변경할 수 있습니다. 최대 50자까지 가능합니다. 이 이름은 Azure Marketplace 및 Azure 포털에서 이 계획의 제목으로 나타납니다. 계획을 사용할 준비가 된 후 기본 모듈 이름으로 사용됩니다.

### <a name="plan-summary"></a>계획 요약

소프트웨어 계획에 대한 간략한 요약(제안이 아님). 이 요약은 Azure Marketplace 검색 결과에 표시되며 최대 100자까지 포함될 수 있습니다.

### <a name="plan-description"></a>계획 설명

이 소프트웨어 계획이 고유한 이유와 제품 내 의 계획 간의 차이점에 대해 설명합니다. 제안을 설명하지 말고 계획만 설명하십시오. 이 설명은 Azure 마켓플레이스 및 제안 **목록** 페이지의 Azure 포털에 표시됩니다. 계획 요약에서 제공한 내용과 동일한 콘텐츠일 수 있으며 최대 2,000자까지 포함할 수 있습니다.

이 필드를 완료한 후 **저장을** 선택합니다.

#### <a name="plan-examples"></a>계획 예제

다음 예제에서는 계획 목록 필드가 다른 보기에 표시되는 방식을 보여 주습니다.

다음은 계획 세부 정보를 볼 때 Azure 마켓플레이스의 필드입니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Azure Marketplace에서 계획 세부 정보를 볼 때 표시되는 필드를 보여 줍니다.":::

다음은 Azure 포털에 대한 계획 세부 정보입니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Azure 포털에 대한 계획 세부 정보를 보여 줍니다.":::

## <a name="plan-availability"></a>계획 가용성

고객이 마켓플레이스에서 검색, 탐색 또는 구매할 수 없도록 게시된 쿠폰을 숨기려면 **가용성** 탭에서 **요금제 숨기기** 확인란을 선택합니다.

이 필드는 다음과 같은 경우에 사용됩니다.

- 이 오퍼는 다른 응용 프로그램을 참조할 때 간접적으로 사용하기 위한 것입니다.
- 이 제안은 개별적으로 구매해서는 안됩니다.
- 이 계획은 초기 테스트에 사용되었으며 더 이상 관련이 없습니다.
- 이 플랜은 임시 또는 계절 적 제안에 사용되었으며 더 이상 제공되지 않아야합니다.

## <a name="technical-configuration"></a>기술 구성

컨테이너 이미지는 개인 Azure [컨테이너 레지스트리에서](https://azure.microsoft.com/services/container-registry/)호스팅되어야 합니다. 기술 **구성** 탭에서 Azure 컨테이너 레지스트리 내의 컨테이너 이미지 리포지토리에 대한 참조 정보를 제공합니다.

오퍼가 게시되면 컨테이너 이미지가 특정 공용 컨테이너 레지스트리의 Azure Marketplace에 복사됩니다. 컨테이너 이미지를 사용하는 모든 요청은 개인 컨테이너 레지스트리가 아닌 Azure Marketplace 공용 컨테이너 레지스트리에서 제공됩니다. 자세한 내용은 [Azure 컨테이너 기술 자산 준비를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)참조하십시오.

### <a name="image-repository-details"></a>이미지 리포지토리 세부 정보

**이미지 리포지토리 세부 정보** 탭에 다음 정보를 제공합니다.

**Azure 구독 ID** - 사용량이 보고되고 컨테이너 이미지가 포함된 Azure 컨테이너 레지스트리에 대한 서비스가 청구되는 구독 ID를 제공합니다. Azure 포털의 구독 페이지에서 이 ID를 찾을 수 [있습니다.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)

**Azure 리소스 그룹 이름** - 컨테이너 이미지와 함께 Azure 컨테이너 레지스트리를 포함하는 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) 이름을 제공합니다. 리소스 그룹은 구독 ID(위)에서 액세스할 수 있어야 합니다. Azure 포털에서 리소스 [그룹](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) 페이지에서 이름을 찾을 수 있습니다.

**Azure 컨테이너 레지스트리 이름** - 컨테이너 이미지가 있는 [Azure 컨테이너 레지스트리의](https://docs.microsoft.com/azure/container-registry/container-registry-intro) 이름을 제공합니다. 컨테이너 레지스트리는 이전에 제공한 Azure 리소스 그룹에 있어야 합니다. 전체 로그인 서버 이름이 아닌 레지스트리 이름만 포함합니다. 이름에서 **azurecr.io** 생략해야 합니다. Azure 포털의 컨테이너 레지스트리 [페이지에서](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) 레지스트리 이름을 찾을 수 있습니다.

**Azure 컨테이너 레지스트리의 관리자 사용자 이름** - 컨테이너 이미지가 있는 Azure 컨테이너 레지스트리와 연결된 [관리자 사용자 이름을](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) 제공합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름과 암호가 필요합니다. 관리자 사용자 이름과 암호를 얻으려면 CLI(Azure 명령줄 인터페이스)를 사용하여 **관리자 지원** 속성을 **True로** 설정합니다. 선택적으로 관리 **사용자를** Azure 포털에서 **사용 하도록** 설정할 수 있습니다.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="컨테이너 레지스트리 업데이트 대화 상자를 보여 줍니다.":::

**Azure 컨테이너 레지스트리의 암호** - Azure 컨테이너 레지스트리와 연결되고 컨테이너 이미지가 있는 관리자 사용자 이름에 대한 암호를 제공합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름과 암호가 필요합니다. **컨테이너 레지스트리** > **액세스 키로** 이동하거나 [표시 명령을](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)사용하여 Azure CLI를 사용하여 Azure 포털에서 암호를 얻을 수 있습니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="액세스 키 메뉴를 보여 줍니다.":::

**Azure 컨테이너 레지스트리 내의 리포지토리 이름**입니다. 이미지가 있는 Azure 컨테이너 레지스트리 리포지토리의 이름을 제공합니다. 레지스트리에 이미지를 푸시할 때 리포지토리의 이름을 포함합니다. [컨테이너 레지스트리](https://azure.microsoft.com/services/container-registry/) > **리포지토리** 페이지로 이동하여 리포지토리의 이름을 찾을 수 있습니다. 자세한 내용은 [Azure 포털의 컨테이너 레지스트리 리포지토리 보기를](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)참조하십시오.

> [!NOTE]
> 이름을 설정한 후에는 변경할 수 없습니다. 계정의 각 오퍼에 대해 고유한 이름을 사용합니다.

### <a name="image-tags-for-new-versions-of-your-offer"></a>새로운 버전의 오퍼에 대한 이미지 태그

고객은 업데이트를 게시할 때 Azure Marketplace에서 업데이트를 자동으로 받을 수 있어야 합니다. 업데이트하지 않으려면 특정 버전의 이미지를 유지할 수 있어야 합니다. 이미지를 업데이트할 때마다 새 이미지 태그를 추가하여 이 작업을 수행할 수 있습니다.

### <a name="image-tag"></a>이미지 태그

이 **필드에는** 지원되는 모든 플랫폼에서 이미지의 최신 버전을 가리키는 최신 태그가 포함되어야 합니다. 또한 버전 태그를 포함해야 합니다(예: xx가 숫자인 xx.xx.xx로 시작). 고객은 [매니페스트 태그를](https://github.com/estesp/manifest-tool) 사용하여 여러 플랫폼을 타겟팅해야 합니다. 매니페스트 태그에서 참조하는 모든 태그는 업로드할 수 있도록 추가되어야 합니다.

모든 매니페스트 태그(최신 태그 제외)는 X, **-** Y 및 Z가 정수인 X.Y 또는 X.Y.Z-로 시작해야 합니다. 예를 들어 **최신** 태그가 1.0.1-linux-x64, 1.0.1-linux-arm32 및 1.0.1-windows-arm32를 가리키는 경우 이 6개의 태그를 이 필드에 추가해야 합니다. 자세한 내용은 [Azure 컨테이너 기술 자산 준비를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)참조하십시오.

> [!NOTE]
> 테스트 중에 이미지를 식별할 수 있도록 이미지에 테스트 태그를 추가해야 합니다.

## <a name="review-and-publish"></a>검토 및 게시

오퍼의 모든 필수 섹션을 완료한 후 제출하여 검토하고 게시할 수 있습니다.

포털의 오른쪽 상단 모서리에서 **검토 및** **게시를**선택합니다.

리뷰 페이지에서 다음을 수행할 수 있습니다.

- 오퍼의 각 섹션에 대한 완료 상태를 참조하십시오. 오퍼의 모든 섹션이 완료로 표시될 때까지 게시할 수 없습니다.
  - **시작되지** 않음 - 시작되지 않았으며 완료해야 합니다.
  - **불완전** - 수정해야 하거나 추가 정보를 제공해야 하는 오류가 있습니다. 도움말은 이 문서의 이전 섹션을 참조하십시오.
  - **완료** - 오류 없이 필요한 모든 데이터를 포함합니다. 오퍼를 제출하려면 먼저 오퍼의 모든 섹션이 완료되어야 합니다.
- 제품 테스트가 올바르게 테스트되었는지 확인하기 위해 인증 팀에 테스트 지침을 제공합니다. 또한 제안을 이해하는 데 도움이 되는 추가 메모를 제공하십시오.

게시를 위한 제안을 제출하려면 **게시를**선택합니다.

제안의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일이 전송됩니다.

오퍼를 공개(또는 비공개 오퍼의 경우 개인 대상에게 게시하려면 파트너 센터로 이동 **라이브)를**선택합니다.

## <a name="next-step"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
