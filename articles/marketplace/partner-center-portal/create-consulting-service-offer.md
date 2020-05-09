---
title: 컨설팅 서비스 제품 만들기-Microsoft 상업적 marketplace
description: 파트너 센터를 사용 하 여 Microsoft AppSource 또는 Azure Marketplace에 게 컨설팅 서비스 제품을 게시 하는 방법에 대해 알아봅니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 626dc5a7d1cd52182e68eb0d217b2ac4c653330e
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994343"
---
# <a name="create-a-consulting-service-offer"></a>컨설팅 서비스 제품 만들기

이 문서에서는 [Microsoft AppSource](https://appsource.microsoft.com/) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에 컨설팅 서비스 제품을 게시 하는 방법을 설명 합니다. AppSource의 Microsoft [Dynamics 365](https://dynamics.microsoft.com/) 및 Power Platform을 기반으로 하는 컨설팅 서비스 제품을 나열 합니다. Azure Marketplace에서 Microsoft Azure을 기반으로 하는 컨설팅 서비스 제공을 나열 합니다. 아직 수행 하지 않은 경우 시작 하기 전에 [파트너 센터에서 상업적 marketplace 계정을 만듭니다](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) . 계정이 상업적 marketplace 프로그램에 등록 되어 있는지 확인 합니다.

제품을 만들기 전에 [컨설팅 서비스 필수 구성](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)요소에서 필수 구성 요소를 검토 합니다.

## <a name="publishing-benefits"></a>게시 이점

상용 marketplace에 게시할 경우의 이점:

- Microsoft 브랜드를 사용 하 여 회사를 홍보 하세요.
- Azure Marketplace를 통해 AppSource 및 20만 조직에서 1억 개 이상의 Office 365 및 Dynamics 365 사용자에 게 도달할 수 있습니다.
- 이러한 마켓플레이스에서 고품질 리드를 받습니다.
- Microsoft 필드 및 전화 영업 팀이 서비스를 승격 합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인 합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택 합니다.
3. 개요 페이지에서 **+ 새로 만들기 제공** > **컨설팅 서비스**를 선택 합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-consulting-service.png)

>[!NOTE]
>제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 상점에 표시 됩니다. 변경을 수행한 후 항상 다시 게시 해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력 합니다. 계정의 각 제품에 대 한 고유 식별자입니다.

- 이 ID는 marketplace 제품의 웹 주소에 있는 고객에 게 표시 됩니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50 자로 제한 됩니다. 예를 들어 **테스트-제품-1**을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`입니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력 합니다. 파트너 센터의 제안에 사용 되는 이름입니다.

- 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다.
- **만들기**를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

**만들기** 를 선택 하 여 제품을 생성 하 고 계속 합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="connect-lead-management"></a>리드 관리 연결

파트너 센터를 사용 하 여 marketplace에 제품을 게시할 때 CRM (고객 관계 관리) 또는 마케팅 자동화 시스템에 연결 _해야_ 합니다. 이를 통해 사용자가에 관심을 표시 하거나 제품을 사용 하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. **연결** 을 선택 하 여 고객 리드를 보낼 위치를 지정 합니다. 파트너 센터는 다음과 같은 시스템을 지원 합니다.

    - 고객 Engagement의 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > CRM 시스템이 위에 나열 되지 않은 경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https 끝점](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 을 사용 하 여 고객 리드 데이터를 저장 한 다음 crm 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시할 때 제품을 잠재 고객 대상에 연결 합니다.
3. 잠재 고객 대상에 대 한 연결이 제대로 구성 되어 있는지 확인 합니다. 파트너 센터에서 게시 한 후에는 연결의 유효성을 검사 하 고 테스트 리드를 전송 합니다. 제품을 라이브 상태로 전환 하는 동안 미리 보기 환경에서 제품을 직접 구매 하 여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객이 손실 되지 않도록 잠재 고객 대상에 대 한 연결이 업데이트 된 상태로 유지 되는지 확인 합니다.

몇 가지 추가 리드 관리 리소스는 다음과 같습니다.

- [리드 관리 개요](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [잠재 고객 관리 FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [리드 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (팝업 차단이 해제 되어 있는지 확인)

계속 하기 전에 **초안 저장** 을 선택 합니다.

### <a name="properties"></a>속성

이 페이지에서는 컨설팅 서비스에서 제공 하는 주요 제품을 설정 하 고, 컨설팅 서비스 유형을 설정 하 고, 적용 가능한 제품을 선택할 수 있습니다.

1. 드롭다운 목록에서 **기본 제품** 을 선택 합니다.
2. 드롭다운 목록에서 **컨설팅 서비스 유형을** 선택 합니다.

    - **평가**: 솔루션의 적용 가능성을 확인 하 고 예상 비용 및 타이밍을 제공 하기 위한 고객 환경 평가입니다.
    - **브리핑**: 프레임 워크, 데모 및 고객 예제를 사용 하 여 고객의 관심을 끌기 위한 솔루션 또는 컨설팅 서비스에 대 한 소개입니다.
    - **구현:** 완벽하게 작동하는 솔루션으로 이어지는 완전한 설치입니다. 2 주 이내에 구현할 수 있는 솔루션으로 제한 합니다.
    - **개념 증명:** 솔루션이 고객의 요구 사항을 충족하는지 확인하기 위한 제한된 범위 구현입니다.
    - **워크숍**: 고객의 온-프레미스에서 수행 되는 대화형 참여입니다. 고객의 데이터 또는 환경을 기반으로 하는 교육, 브리핑, 평가 또는 데모가 포함 될 수 있습니다.

3. **Azure**의 기본 제품을 선택한 경우 최대 3 개의 **솔루션 영역**을 선택 합니다. 이를 통해 Azure Marketplace 고객이 제품을 보다 쉽게 찾을 수 있습니다. Azure를 선택 하지 않은 경우이 단계를 건너뜁니다.
4. Azure _이외의 기본 제품을_ 선택한 경우 최대 3 개의 **해당 제품**을 선택 합니다. 이를 통해 AppSource의 고객이 제품을 보다 쉽게 찾을 수 있습니다. 자세한 내용은 [Microsoft AppSource 컨설팅 서비스 목록 지침](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF)을 참조 하세요.
5. 제품이 적용 되는 최대 6 개의 **산업** 을 선택 합니다. 이를 통해 고객이 제품을 보다 쉽게 찾을 수 있습니다.
6. 회사에서 컨설팅 서비스 제품 목록에 표시할 수 있는 **최대 3 개의 역량을 추가** 합니다. Azure 전문가 MSPs 및 Azure 네트워킹 MSPs를 제외 하 고 하나 이상의 역량을 요구 합니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="offer-listing"></a>제품 목록

여기에서 marketplace에 표시 되는 제품 세부 정보를 정의 합니다. 여기에는 제품 이름, 설명, 이미지 등이 포함 됩니다. 이 제품을 구성 하는 동안 [상업적 marketplace 인증 정책 페이지](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) 에 자세히 설명 된 정책을 따라야 합니다.

> [!NOTE]
> 제안 설명이 구로 시작 하는 경우 제품 세부 정보는 영어로 지정할 필요가 없습니다. &quot;이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다. &quot; 또한 제품 목록 세부 정보에 사용 된 것과 다른 언어로 콘텐츠를 제공 하는 유용한 링크도 제공 합니다.

### <a name="name"></a>속성

여기에 입력 하는 이름은 제품의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 상자에 입력 한 텍스트로 미리 채워집니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다.
- 50 자를 초과할 수 없습니다.
- Emojis를 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

제품에 대 한 간단한 설명을 제공 합니다. 이 길이는 최대 100 자까지 가능 하며 marketplace 검색 결과에 사용 됩니다.

### <a name="description"></a>Description

제품에 대 한 더 긴 설명을 최대 3000 자까지 제공 합니다. 이는 marketplace 목록 개요에서 고객에 게 표시 됩니다.

설명에 다음 중 하나 이상을 포함 합니다.

- 제품에서 제공 하는 가치 및 주요 이점
- 범주 또는 업계 연결 또는 둘 다
- 앱 내 구매 기회
- 필요한 모든 공개

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

### <a name="keywords"></a>키워드

주 제품 및 컨설팅 서비스와 관련 된 검색 키워드를 최대 3 개까지 입력 합니다. 이를 통해 제품을 보다 쉽게 찾을 수 있습니다.

### <a name="duration"></a>Duration

고객에 게이 engagement의 예상 기간을 설정 합니다.

### <a name="contact-information"></a>연락처 정보

**기본** 및 **보조 연락처**의 이름, 전자 메일 및 전화 번호를 제공 해야 합니다. 이 정보는 고객에 게 표시 되지 않습니다. Microsoft에서 사용할 수 있으며, CSP (클라우드 솔루션 공급자) 파트너에 게 제공 될 수 있습니다.

### <a name="supporting-documents"></a>지원 문서

제품에 대해 지원 되는 PDF 문서를 최대 3 개 (하나 이상) 추가 합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

제품에 사용할 로고 및 이미지를 제공 합니다. 모든 이미지는 .png 형식 이어야 합니다. 흐린 이미지는 거부 됩니다.

>[!Note]
>파일을 업로드 하는 데 문제가 있는 경우 로컬 네트워크가 파트너 센터에서 사용 하는 https://upload.xboxlive.com 서비스를 차단 하지 않는지 확인 합니다.

#### <a name="store-logos"></a>매장 로고

다음 픽셀 크기에 따라 제품 로고의 PNG 파일을 제공 합니다.

- **작음 (48 x 48)**
- **큼 (216 x 216)**

모든 로고가 필요 하며 marketplace 목록의 다른 위치에서 사용 됩니다.

#### <a name="screenshots-optional"></a>스크린샷 (선택 사항)

제품의 작동 방식을 보여 주는 최대 5 개의 스크린샷을 추가 합니다. 각각 1280 x 720 픽셀 크기 및 PNG 형식 이어야 합니다.

#### <a name="videos-optional"></a>비디오 (선택 사항)

제품을 설명 하는 비디오를 최대 4 개까지 추가 합니다. 비디오의 이름, 해당 웹 주소 (URL) 및 비디오의 미리 보기 PNG 이미지를 1280 x 720 픽셀 크기로 입력 합니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="pricing-and-availability"></a>가격 책정 및 가용성

가격 책정, 시장 및 개인 키와 같은 요소를 정의 합니다.

1. **시장**: 제품을 사용할 수 있는 시장을 설정 합니다. 제품 당 하나의 시장을 선택할 수 있습니다.
    1. 미국 또는 캐나다 시장에서 **주 편집** (또는 시/ **도)을**선택 하 여 제품을 사용할 수 있는 위치를 지정 합니다.
2. **사용자 미리 보기**: 제품의 개인 사용자를 설정 하는 데 사용 되는 **숨김 키** 를 구성 합니다.
3. **가격 책정**: **무료** 또는 **유료** 제품 인지 여부를 지정 합니다.

    > [!NOTE]
    > 컨설팅 서비스 제공은 목록에만 해당 합니다. 모든 트랜잭션은 상업적 marketplace 외부에서 직접 수행 됩니다.

4. 유료 제품의 경우 **가격과 통화** 와 가격의 **고정** **여부를 지정 합니다.** 예상 되는 경우 가격에 영향을 주는 요소를 설명에 지정 해야 합니다.
5. 계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="review-and-publish"></a>검토 및 게시

제품의 필수 섹션을 모두 완료 한 후에는 제품을 제출 하 여 검토 하 고 게시할 수 있습니다.

1. 컨설팅 서비스 제품을 게시할 준비가 되 면 **검토 및 게시**를 클릭 합니다.
2. 최종 제출 페이지에서 세부 정보를 검토 합니다.
3. 필요한 경우 제품의 세부 정보에 대 한 설명이 필요 하다 고 생각 되 면 인증 팀에 메모를 작성 합니다.
4. 준비가 되 면 **제출**을 선택 합니다.
5. **제품 개요** 페이지에는 제품의 게시 단계가 나와 있습니다.

각 게시 단계에서 제품이 제공 될 것으로 예측할 수 있는 시간에 대 한 자세한 내용은 [상용 Marketplace 제품의 게시 상태 확인](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)을 참조 하세요.

## <a name="update-your-existing-consulting-service-offers"></a>기존 컨설팅 서비스 제품 업데이트

- [상업적 marketplace에서 기존 제품 업데이트](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
