---
title: Microsoft AppSource에서 Power BI 앱 제품 계획 가이드(Azure Marketplace)
description: 이 문서에서는 Microsoft AppSource에서 Power BI 앱 제품에 대한 계획 가이드(Azure Marketplace)를 제공합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/26/2021
ms.openlocfilehash: 7f21e3b7e95bf4d735d394a9dfebe04023218faa
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061239"
---
# <a name="plan-a-power-bi-app-offer"></a>Power BI 앱 제품 계획

이 문서에서는 Power BI 앱을 Microsoft [AppSource](https://appsource.microsoft.com/)에 게시하기 위해 준비하거나 완료해야 하는 콘텐츠와 요구 사항을 다룹니다. Power BI 앱에는 데이터 세트, 보고서 및 대시보드를 포함하는 사용자 지정 가능한 콘텐츠가 있습니다. AppSource를 사용하는 다른 Power BI 플랫폼과 앱을 사용하고, 개발자가 허용하는 조정 및 사용자 지정을 수행하고, 자신의 데이터에 연결할 수 있습니다.

시작하기 전에 템플릿, 팁 및 샘플을 제공하는 다음 링크를 검토하세요.

- [Power BI 앱 만들기](/power-bi/service-template-apps-create)
- [Power BI 앱 작성을 위한 팁](/power-bi/service-template-apps-tips)
- [샘플](/power-bi/service-template-apps-samples)


## <a name="publishing-benefits"></a>게시 이점

상업용 마켓플레이스에 게시할 경우의 이점:

- Microsoft 브랜드를 활용하여 회사를 홍보할 수 있습니다.
- 잠재적으로 AppSource의 Microsoft 365 및 Dynamics 365 사용자는 1억 명이 넘고, Azure Marketplace를 사용하는 조직은 200,000곳이 넘습니다.
- 이러한 마켓플레이스에서 우량 잠재 고객을 확보할 수 있습니다.
- Microsoft 현장 팀 및 통신 판매 팀을 통해 서비스를 홍보할 수 있습니다.

## <a name="overview"></a>개요

:::image type="content" source="media/power-bi/power-bi-app-publishing-steps.png" alt-text="Power BI 앱 제품을 게시하는 단계 개요" border="false":::

지금 제품을 만들 준비가 되었다면 아래의 [다음 단계](#next-steps)를 참조하세요. 그렇지 않은 경우 제품 생성 프로세스를 시작하기 전에 제대로 준비되었는지 계속 살펴봅니다.

다음 몇 가지 항목에서 다루는 주요 게시 단계는 아래와 같습니다.

1. Power BI에서 애플리케이션을 만듭니다. 제품에 대한 주요 기술 자산인 패키지 설치 링크를 받게 됩니다. 파트너 센터에서 제품을 만들기 전에 사전 프로덕션으로 테스트 패키지를 보냅니다. 자세한 내용은 [Power BI 앱이란?](/power-bi/service-template-apps-overview)을 참조하세요.
2. 공식 이름, 설명 및 로고 같은 마케팅 자료를 추가합니다.
3. 사용 약관, 개인 정보 취급 방침, 지원 정책 및 사용자 도움말 같은 제품의 법률 및 지원 문서를 포함시킵니다.
4. 제품 만들기 - 파트너 센터를 사용하여 제품 설명, 마케팅 자료, 법적 정보, 지원 정보 및 자산 사양을 포함해 제품에 대한 세부 정보를 편집합니다.
5. 게시를 위해 제출합니다.
6. 파트너 센터에서 프로세스를 모니터링합니다. 여기서 AppSource 온보딩 팀이 앱을 테스트하고, 유효성을 검사 및 인증합니다.
7. 인증된 후에는 해당 테스트 환경에서 앱을 검토하고 릴리스합니다. 그러면 AppSource에 나열됩니다(“준비 완료”).
8. Power BI에서 패키지를 프로덕션으로 보냅니다. 자세한 내용은 [Power BI 앱 릴리스 관리](/power-bi/service-template-apps-create#manage-the-template-app-release)를 참조하세요.

## <a name="requirements"></a>요구 사항

상업용 마켓플레이스에 게시하려면 Power BI 앱 제품이 다음 기술 및 비즈니스 요구 사항을 충족해야 합니다.

### <a name="technical-requirements"></a>기술 요구 사항

필요한 주요 기술 자산은 [Power BI 앱](/power-bi/connect-data/service-template-apps-overview)입니다. 이것은 기본 데이터 세트, 보고서 또는 대시보드의 모음입니다. 또한 이전에 [콘텐츠 팩](/power-bi/service-organizational-content-pack-introduction)으로 알려진 선택적 연결 서비스 및 포함된 데이터 세트도 있습니다. 이 유형의 앱을 개발하는 방법에 대한 자세한 내용은 [Power BI 앱이란?](/power-bi/connect-data/service-template-apps-overview)을 참조하세요.

#### <a name="get-an-installation-web-address"></a>설치 웹 주소 가져오기

[Power BI](https://powerbi.microsoft.com/) 환경 내에서만 Power BI 앱을 빌드할 수 있습니다.

1. [Power BI Pro 라이선스](/power-bi/service-admin-purchasing-power-bi-pro)로 로그인합니다.
2. Power BI에서 앱을 만들고 테스트합니다.
3. 앱 설치 웹 주소를 받으면 파트너 센터의 **기술 구성** 페이지에 추가합니다.

Power BI에서 앱을 만들고 테스트한 후에는 애플리케이션 설치 웹 주소를 저장합니다. [Power BI 앱 제품을 만들려면](power-bi-app-offer-setup.md) 필요하기 때문입니다.

### <a name="business-requirements"></a>비즈니스 요구 사항

비즈니스 요구 사항에는 절차, 계약 및 법적 의무가 포함됩니다. 다음이 필요합니다.

- 등록된 상업용 마켓플레이스 게시자여야 합니다. 등록하지 않은 경우 [파트너 센터에서 상업용 마켓플레이스 계정 만들기](create-account.md)의 단계를 따릅니다.
- AppSource에 나열되는 제품에 대한 조건을 충족하는 콘텐츠를 제공합니다. 자세한 내용은 [AppSource에 나열할 앱이 있나요? 이렇게 하세요](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)를 참조하세요.
- [Microsoft 개인 정보 취급 방침](https://privacy.microsoft.com/privacystatement)에 동의하고 준수합니다.

## <a name="licensing-options"></a>라이선싱 옵션

Power BI 앱 제품에 제공되는 유일한 라이선스 옵션은 다음과 같습니다.

| 라이선싱 옵션 | 트랜잭션 프로세스 |
| --- | --- |
| 지금 받기(무료) | 고객에게 평가판으로 제공하는 제품을 나열합니다. |
|

\* 게시자는 주문, 처리, 계량, 청구, 송장, 결제, 수금을 포함하지만 이에 제한되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원합니다.

## <a name="customer-leads"></a>잠재 고객

파트너 센터를 통해 제품을 상업용 Marketplace에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 시험 사용을 하도록 설정하려면 CRM에 연결해야 합니다. 그러지 않으면 CRM에 연결하는 것은 선택 사항입니다. 파트너 센터는 Azure 테이블, Dynamics 365 Customer Engagement, HTTPS 엔드포인트, Marketo 및 Salesforce를 지원합니다.

## <a name="legal-contracts"></a>법적 계약

고객이 제품을 사용해 보기 전에 수락해야 하는 사용 약관 또는 찾을 수 있는 링크가 필요합니다.

## <a name="offer-listing-details"></a>제품 목록 세부 정보

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다”라는 문구로 시작하는 경우 제품 목록 내용을 영어로 작성하지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 관련 항목 중 일부는 미리 준비합니다. 명시된 경우를 제외하고 모두 필요합니다.

- **이름** - 이 이름은 상업용 Marketplace에서 제품 목록의 제목으로 표시됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약** - 줄 바꿈 없이 100자 이하의 한 문장으로 제품의 목적 또는 기능을 설명합니다. 이 요약문은 상업용 Marketplace 목록 검색 결과에 사용됩니다.
- **설명** - 이 설명은 상업용 Marketplace 목록 개요에 표시됩니다. 가치 제안, 주요 이점, 대상 사용자 베이스, 범주 또는 산업 협회, 앱 내 구매 기회, 필요한 정보를 공개하고 자세한 정보를 볼 수 있는 링크를 포함하는 것이 좋습니다. 이 텍스트 상자에는 보다 설득력 있는 설명을 도와주는 다양한 텍스트 편집기 컨트롤이 들어 있습니다. 필요에 따라 서식을 지정하기 위해 HTML 태그를 사용합니다.
- **검색 키워드**(선택 사항) - 고객이 제품을 찾는 데 사용할 수 있는 최대 3개의 검색 키워드입니다. 제품 **이름** 및 **설명** 은 포함할 필요가 없습니다. 해당 텍스트는 검색에 자동으로 포함됩니다.
- **앱이 함께 작동하는 제품**(선택 사항) - 제품에서 사용하는 최대 3개 제품의 이름입니다.
- **도움말/개인 정보 취급 방침 링크** – 회사 도움말과 개인 정보 취급 방침에 대한 URL입니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인할 책임은 본인에게 있습니다.
- **연락처 정보**
  - **고객 지원팀 연락처** - 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 이메일입니다. 고객 지원팀 웹 사이트의 URL도 포함해야 합니다.
  - **엔지니어링 연락처** - 제품에 문제가 있을 때 Microsoft에서 직접 사용할 수 있는 이름, 전화 번호 및 이메일입니다. 이 연락처 정보는 상업용 Marketplace에 표시되지 않습니다.
  - **지원 문서**(선택 사항) - 백서, 브로슈어, 검사 목록 또는 PowerPoint 프레젠테이션과 같은 고객 관련 문서를 PDF 형식으로 3개까지 제공할 수 있습니다.
- **미디어**
    - **로고** - **큰** 로고에 대한 PNG 파일입니다. 파트너 센터는 이를 사용하여 기타 필요한 로고 크기를 만듭니다. 원한다면 나중에 다른 이미지로 바꿀 수 있습니다.
    - **스크린샷** - 제품의 작동 방식을 보여 주는 1~5개의 스크린샷입니다. 이미지는 PNG 형식에 1280 x 720 픽셀이며 캡션을 포함해야 합니다.
    - **동영상**(선택 사항) - 제품을 설명하는 동영상을 4개까지 추가할 수 있습니다. 이름, YouTube 또는 Vimeo URL 및 1280 x 720 픽셀 PNG 썸네일을 포함합니다.

> [!Note]
> 제품은 상업용 Marketplace에 게시되는 일반 [상업용 Marketplace 인증 정책을](/legal/marketplace/certification-policies#100-general) 충족해야 합니다.

## <a name="additional-sales-opportunities"></a>추가 영업 기회

Microsoft에서 지원하는 마케팅 및 판매 채널을 옵트인하도록 선택할 수 있습니다. 파트너 센터에서 제품을 만들 때 프로세스 마지막 부분에서 다음과 같은 두 개의 탭이 표시됩니다.

- **Microsoft와 공동 판매** - Microsoft 영업 팀은 고객의 요구 사항을 평가할 때 IP 공동 판매 적합 솔루션을 고려합니다. 공동 판매 자격에 대한 자세한 내용은 [공동 판매 상태의 요구 사항](/legal/marketplace/certification-policies)을 참조하세요. 제품 평가 준비에 대한 자세한 내용은 [파트너 센터의 공동 판매 옵션](./co-sell-configure.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Power BI 앱 제품 만들기](power-bi-app-offer-setup.md)
