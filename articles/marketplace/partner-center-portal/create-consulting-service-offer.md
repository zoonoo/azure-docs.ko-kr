---
title: 파트너 센터 - Azure 마켓플레이스에서 컨설팅 서비스 제공 만들기
description: 파트너 센터를 사용하여 Azure 마켓플레이스 또는 AppSource에 컨설팅 서비스 오퍼를 게시하는 방법을 알아봅니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: f1354ce824bc4e57ab55846c714d4a0a73a46966
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873823"
---
# <a name="create-a-consulting-service-offer"></a>컨설팅 서비스 오퍼 만들기

> [!IMPORTANT]
> 컨설팅 서비스 오퍼의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전하고 있습니다. 오퍼가 마이그레이션될 때까지 [Azure 및 Dynamics 365 컨설팅 서비스 제공의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) 지침을 따라 Cloud Partner Portal에서 오퍼를 관리하십시오.

이 문서에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 또는 [AppSource](https://appsource.microsoft.com/)에 컨설팅 서비스 오퍼를 게시하는 방법을 설명합니다. 목록 컨설팅 서비스는 Microsoft [Dynamics 365](https://dynamics.microsoft.com/) 및 AppSource의 전원 플랫폼을 기반으로 합니다. 목록 컨설팅 서비스는 Azure 마켓플레이스에서 Microsoft Azure를 기반으로 합니다.

Azure 마켓플레이스 또는 AppSource 컨설팅 서비스에서 컨설팅 서비스를 만들려면 먼저 [파트너 센터에 게시자 계정이 있어야](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)하며 계정이 상용 마켓플레이스 프로그램에 등록되어야 합니다. 오퍼를 만들기 전에 [컨설팅 서비스 전제 조건의](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)필수 구성 조건을 검토하십시오.

## <a name="publishing-benefits"></a>게시 이점

상업 시장에 게시의 이점:

- Microsoft 브랜드를 사용하여 회사를 홍보합니다.
- 잠재적으로 Azure Marketplace를 통해 AppSource의 1억 명 이상의 Office 365 및 Dynamics 365 사용자 및 200,000개 이상의 조직에 도달할 수 있습니다.
- 이러한 마켓플레이스에서 고품질 잠재 고객을 받으십시오.
- Microsoft 현장 및 원격 판매 팀에서 서비스를 승격

## <a name="create-a-new-offer"></a>새 제안 만들기

위에서 설명한 요구 사항을 충족한 후 다음 단계에 따라 컨설팅 서비스 오퍼를 만듭니다.

1. [파트너 센터에](https://partner.microsoft.com)로그인한 다음 상단 메뉴에서 **대시보드를** 선택합니다.
2. 왼쪽 탐색 막대에서 **상업용 마켓플레이스를**선택한 다음 **개요를 선택합니다.**

    :::image type="content" source="media/cs-menu-overview.png" alt-text="상업 시장의 메뉴 설명":::

3. **+ 새 제안을**선택한 다음 컨설팅 **서비스를**선택합니다.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="새 오퍼를 만드는 단추를 보여 줍니다.":::

4. **오퍼 ID를**입력합니다. 이 식별자는 계정의 각 오퍼에 대한 고유 식별자입니다.

    - 이 ID는 마켓플레이스 오퍼의 웹 주소에 있는 고객에게 표시됩니다.
    - 소문자, 숫자, 대시 및 밑줄만 사용하지만 공백은 사용하지 않습니다. 길이는 50자로 제한됩니다. 예를 들어 **테스트-offer-1을**입력하면 오퍼 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`URL이 됩니다.
    - 을 **선택하면**오퍼 ID가 변경되지&#39;.

5. **오퍼 별칭을**입력합니다. 파트너 센터에서 쿠폰을 참조하는 데 사용되는 이름입니다.

    - 이 이름은 시장에서 사용되지&#39;없습니다. 고객에게 표시되는 오퍼 이름 및 기타 값과 는&#39;. 이 필드를 사용하여 내부적으로 오퍼를 식별하는 데 더 유용한 오퍼에 이름을 할당할 수 있습니다. 고객에게 표시되지 않습니다.
    - &#39;만들기를 선택한 후 t를 변경할 수 **있습니다.**

이 두 값을 입력한 후 **만들기를** 선택하여 **쿠폰 설정** 페이지를 계속합니다.

## <a name="offer-setup"></a>오퍼 설정

쿠폰 ID 및 쿠폰 별칭을 입력하면 파트너 센터에서 초안 제안을 만들고 **제안 설정** 페이지를 표시합니다. 다음 단계에 따라 쿠폰을 설정합니다.

### <a name="connect-lead-management"></a>잠재 고객 관리 연결

파트너 센터를 통해 오퍼를 시장에 게시할 때는 CRM(고객 관계 관리) 또는 마케팅 자동화 시스템에 _연결해야 합니다._ 이를 통해 다른 사람이 귀사에 관심을 표명하거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. **연결을** 선택하여 고객 잠재 고객을 보낼 위치를 지정합니다. 파트너 센터는 다음 시스템을 지원합니다.

    - 고객 참여를 위한 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > CRM 시스템이 위에 나열되지&#39;경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https Endpoint를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 사용하여 고객 잠재 고객 데이터를 저장한 다음 CRM 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시할 때 잠재 고객 대상에 쿠폰을 연결합니다.
3. 잠재 고객 대상에 대한 연결이 제대로 구성되어 있는지 확인합니다. 파트너 센터에 게시하면 연결의 유효성을 검사하고 테스트 리드를 보낼&#39;있습니다. 오퍼가 시작되기 전에 쿠폰을 미리 볼 때 미리 보기 환경에서 직접 쿠폰을 구입하여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객 에 대한 연결이 업데이트된 상태로 유지되므로 잠재 고객을 잃지 않도록&#39;.

다음은 몇 가지 추가 잠재 고객 관리 리소스입니다.

- [잠재 고객 관리 개요](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [잠재 고객 관리 FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [잠재 고객 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF(팝업 차단이 꺼져 있는지 확인)

다음 섹션인 속성으로 계속하기 전에 **초안 저장을** 선택합니다.

### <a name="properties"></a>속성

이 페이지에서는 컨설팅 서비스에서 제공하는 기본 제품을 가장 잘 다루고, 컨설팅 서비스 유형을 설정하고, 해당 제품을 선택할 수 있습니다.

1. 드롭다운 목록에서 **기본 제품을** 선택합니다.
2. 드롭다운 목록에서 **컨설팅 서비스 유형을** 선택합니다.

    - **평가** : 고객&#39;솔루션의 적용 가능성을 결정하고 비용 및 타이밍의 추정을 제공하기 위해 환경을 평가합니다.
    - **브리핑** : 프레임워크, 데모 및 고객 예제를 사용하여 고객의 관심을 유도하는 솔루션 또는 컨설팅 서비스에 대한 소개입니다.
    - **구현** : 완벽하게 작동하는 솔루션을 제공하는 완벽한 설치입니다. 2주 이내에 구현할 수 있는 솔루션으로 제한됩니다.
    - **개념 증명** : 솔루션이 고객 요구 사항을 충족하는지 여부를 결정하는 제한된 범위 구현입니다.
    - **워크숍** : 고객&#39;현장에서 진행되는 대화형 참여. 여기에는 고객&#39;데이터 또는 환경에 구축된 교육, 브리핑, 평가 또는 데모가 포함될 수 있습니다.

1. **Azure의**기본 제품을 선택한 경우 최대 3개의 **솔루션 영역을**선택합니다. 이렇게 하면 Azure 마켓플레이스의 고객이 제품을 더 쉽게 찾을 수 있습니다. Azure를 선택하지&#39;경우 이 단계를 건너뜁니다.
2. Azure _이외의_ 기본 제품을 선택한 경우 최대 3개의 **적용 가능한 제품을 선택합니다.** 이를 통해 AppSource의 고객이 제품을 더 쉽게 찾을 수 있습니다. 자세한 내용은 [Microsoft AppSource 컨설팅 서비스 목록](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) 지침(PDF)을 참조하십시오.
3. 쿠폰이 적용되는 최대 **6개의 인더스트리를** 선택합니다. 이렇게 하면 고객이 제품을 더 쉽게 찾을 수 있습니다.
4. 귀사가 획득한 **역량을** 최대 3개까지 추가하여 컨설팅 서비스 오퍼 목록에 표시할 수 있습니다. Azure 전문가 MSP&#39;s 및 Azure 네트워킹 MSP&#39;를 제외한 하나 이상의 역량이 필요합니다.

다음 섹션인 제안 목록으로 이동하기 전에 **초안 저장을** 선택합니다.

## <a name="offer-listing"></a>오퍼 리스팅

여기에서 마켓플레이스에 표시되는 쿠폰 세부 정보를 정의할&#39;있습니다. 여기에는 오퍼 이름, 설명, 이미지 등이 포함됩니다. 이 제안을 구성하는 동안 [Microsoft&#39;정책 페이지에](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) 자세히 설명된 정책을 따라야 합니다.

> [!NOTE]
> 제안 설명이 문구로 시작하는 경우 제공 세부 사항은 영어로 &quot;&#39;필요하지 않습니다,이 응용 프로그램은 [비 영어 언어]에서만 사용할 수 있습니다. &quot; 또한 오퍼 목록 세부 정보에 사용된 것과 다른 언어로 콘텐츠를 제공하는 유용한 링크를 제공하는&#39;&#39;.

### <a name="name"></a>속성

여기에 입력한 이름은 오퍼링의 제목으로 표시됩니다. 이 필드는 **오퍼를** 만들 때 쿠폰 별칭 상자에 입력한 텍스트로 미리 채워져 있습니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표권(상표 또는 저작권 기호포함).
- 50자 이상일&#39;있습니다.
- &#39;이모티콘을 포함 할 수 있습니다.

### <a name="search-results-summary"></a>검색 결과 요약

오퍼에 대한 간략한 설명을 제공합니다. 최대 100자까지 사용할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

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
  - 오퍼의 혜택을 누릴 수 있는 사용자 유형입니다.
  - 고객의 요구 또는 제안 주소 문제.
- 처음 몇 문장은 검색 결과에 표시될 수 있습니다.
- 제품을 판매하는 기능과 기능에 의존하지&#39;마십시오. 대신 오퍼가 제공하는 가치에 초점을 맞춥니다.
- 산업별 어휘 또는 혜택 기반 표현을 사용해 보십시오.

설명을 더욱 매력적으로 만들려면 고급 텍스트 편집기를 사용하여 설명의 서식을 지정합니다. 리치 텍스트 편집기를 사용하면 숫자, 글머리 기호, 굵은 글꼴, 기울임꼴 및 들여쓰기를 추가하여 설명을 더 읽기 쉽게 만들 수 있습니다.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="오퍼 설명을 작성하는 리치 텍스트 편집기를 보여 줍니다." border="false":::

### <a name="keywords"></a>키워드

기본 제품 및 컨설팅 서비스와 관련된 검색 키워드를 최대 3개까지 입력합니다. 이렇게 하면 쿠폰을 더 쉽게 찾을 수 있습니다.

### <a name="duration"></a>Duration

고객과의 이 참여의 예상 기간을 설정합니다.

### <a name="contact-information"></a>연락처 정보

**기본** 및 **보조 연락처의**이름, 전자 메일 및 전화 번호를 제공해야 합니다. 이 정보는 고객에게 표시되지&#39;. Microsoft에서 사용할 수 있으며 CSP(클라우드 솔루션 공급자) 파트너에게 제공될 수 있습니다.

### <a name="supporting-documents"></a>서류 지원

오퍼에 대한 지원 PDF 문서를 최대 3개(하나 이상)까지 추가합니다.

### <a name="marketplace-images"></a>마켓플레이스 이미지

오퍼와 함께 사용할 로고와 이미지를 제공합니다. 모든 이미지는 .png 형식이어야 합니다. 흐릿한 이미지는 거부됩니다.

#### <a name="store-logos"></a>스토어 로고

다음 픽셀 크기 각각에&#39;s 로고를 제공하십시오.

- **소형(48 x 48)**
- **라지 (216 x 216)**

모든 로고는 필수이며 마켓플레이스 목록의 다른 위치에서 사용됩니다.

#### <a name="screenshots-optional"></a>스크린샷(선택 사항)

쿠폰의 작동 방식을 보여주는 스크린샷을 최대 5개까지 추가합니다. 각 픽셀의 크기와 .png 형식은 1280 x 720 픽셀이어야 합니다.

#### <a name="videos-optional"></a>동영상(선택 사항)

쿠폰을 보여주는 동영상을 최대 4개까지 추가하세요. 비디오&#39;이름, 웹 주소(URL) 및 1280 x 720 픽셀 크기로 비디오의 축소판 .png 이미지를 입력합니다.

다음 섹션, 가격 및 가용성으로 계속하기 전에 **초안 저장을** 선택합니다.

## <a name="pricing-and-availability"></a>가격 책정 및 가용성

여기에서는 가격 책정, 시장 및 개인 키와 같은 요소를 정의합니다.

1. **시장**: 당신의 제안이 사용할 수 있습니다 시장을 설정합니다. 오퍼당 하나의 마켓만 선택할 수 있습니다.
    1. 미국 또는 캐나다 시장의 경우 주(또는 **도)를** **선택하여** 쿠폰을 사용할 수 있는 위치를 지정합니다.
2. **잠재고객 미리 보기**: 쿠폰의 비공개 타겟을 설정하는 데 사용되는 **숨기기 키를** 구성합니다.
3. **가격**: 쿠폰이 **무료** 또는 **유료** 제품인지 여부를 지정합니다.

    > [!NOTE]
    > 컨설팅 서비스는 리스팅전용입니다. 모든 거래는 상업 시장 외부에서 직접 발생합니다.

4. 유료 오퍼의 경우 **가격 및 통화와** 가격이 **고정또는** **예상가격인지**를 지정합니다. 예상되는 경우 설명에 가격에 영향을 주는 요인을 지정해야 합니다.
5. **초안 저장을**선택합니다.

## <a name="review-and-publish"></a>검토 및 게시

오퍼의 모든 필수 섹션을 완료한 후 제안을 제출하여 검토하고 게시할 수 있습니다.

1. 컨설팅 서비스 오퍼를 게시할 준비가 되면 **검토를 클릭하고 게시합니다.**
2. 최종 제출 페이지에서 세부 정보를 검토합니다.
3. 필요한 경우, 오퍼의 세부 사항에 대한 설명이 필요하다고 생각되면 인증 팀에 메모를 작성하십시오.
4. 준비가 되면 **제출을**선택합니다.
5. **오퍼 개요** 페이지에는 오퍼가 게시 단계에 있는지 표시됩니다.

각 게시 단계에서 오퍼가 예상되는 기간에 대한 자세한 내용은 [상업용 마켓플레이스 제품의 게시 상태 확인을](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)참조하십시오.

## <a name="update-your-existing-consulting-service-offers"></a>기존 컨설팅 서비스 오퍼 업데이트

- [상용 시장에서 기존 오퍼 업데이트](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
