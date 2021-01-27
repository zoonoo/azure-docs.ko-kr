---
title: Microsoft 상업적 marketplace에 대 한 관리 서비스 제안 계획
description: Microsoft 파트너 센터에서 상용 Marketplace 프로그램을 사용 하 여 Azure Marketplace에 대 한 새로운 관리 서비스 제품을 계획 하는 방법입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: 57a6883a2c4fff14507df27b55ac0f6621306bf5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879278"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace에 대 한 관리 서비스 제품을 계획 하는 방법

이 문서에서는 파트너 센터를 통해 Microsoft 상업적 marketplace에 관리 되는 서비스 제품을 게시 하기 위한 요구 사항을 소개 합니다.

관리 서비스는 Azure Lighthouse를 사용 하 여 교차 테 넌 트 및 다중 테 넌 트 관리를 가능 하 게 하는 제공 Azure Marketplace입니다. 자세히 알아보려면 [Azure Lighthouse?](../lighthouse/overview.md) 을 참조 하세요. 고객이 관리 서비스 제품을 구매할 때 하나 이상의 구독 또는 리소스 그룹을 위임할 수 있습니다.

## <a name="eligibility-requirements"></a>자격 요구 사항

관리 서비스를 게시 하려면 클라우드 플랫폼에서 골드 또는 실버 Microsoft 역량을 달성 해야 합니다. 이 역량은 고객에 대 한 전문 지식을 보여줍니다. 자세한 내용은 [Microsoft 파트너 네트워크](https://partner.microsoft.com/membership/competencies)역량을 참조 하세요.

제품은 Azure Marketplace에 게시할 수 있는 모든 [상용 marketplace 인증 정책을](/legal/marketplace/certification-policies) 충족 해야 합니다.

## <a name="customer-leads"></a>잠재 고객

제품을 CRM (고객 관계 관리) 시스템에 연결 하 여 고객 정보를 수집 해야 합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 제품을 찾은 제품 이름, ID 및 온라인 스토어와 함께 이러한 고객 세부 정보는 구성 된 CRM 시스템으로 전송 됩니다. 상업적 marketplace는 Azure 테이블을 사용 하거나 전원 자동화를 사용 하 여 HTTPS 끝점을 구성 하는 옵션을 비롯 하 여 다양 한 종류의 CRM 시스템을 지원 합니다.

제공을 만든 후 또는 후에 언제 든 지 CRM 연결을 추가 하거나 수정할 수 있습니다. 자세한 지침은 [상용 marketplace 제품의 고객 리드](partner-center-portal/commercial-marketplace-get-customer-leads.md)를 참조 하세요.

## <a name="legal-contracts"></a>법적 계약

파트너 센터의 속성 페이지에 제품 사용에 대 한 사용 **약관** 을 제공 하 라는 메시지가 표시 됩니다. 파트너 센터에서 직접 사용 약관을 입력 하거나 찾을 수 있는 URL을 제공할 수 있습니다. 고객은 제품을 구입 하기 전에 이러한 사용 약관에 동의 해야 합니다.

## <a name="offer-listing-details"></a>제안 목록 세부 정보

파트너 센터에서 관리 서비스 제품을 만들 때 텍스트, 이미지, 문서 및 기타 제품 정보를 입력 합니다. 이는 Azure Marketplace에서 제품을 검색할 때 고객이 보게 될 것입니다. 다음 예제를 참조하십시오.

:::image type="content" source="media/example-managed-service.png" alt-text="관리 서비스 제품이 Azure Marketplace에 표시 되는 방식을 보여 줍니다.":::

**호출 설명**

1. 로고
1. 이름
1. 간단한 설명
1. 범주
1. 법적 계약 및 개인 정보 취급 방침
1. 설명
1. 스크린샷/비디오
1. 유용한 링크

다음은 제품 목록이 Azure Portal 표시 되는 방법의 예입니다.

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="이 제품이 Azure Portal 표시 되는 방식을 보여 줍니다.":::

**호출 설명**

1. Name
2. 설명
3. 유용한 링크
4. 스크린샷/비디오

> [!NOTE]
> 제품이 영어 이외의 언어로 제공 되는 경우 제품 목록은 해당 언어에 포함 될 수 있지만 설명은 "이 서비스는 &lt; 제품 콘텐츠>의 언어로 제공 됩니다." 라는 영어로 시작 하거나 끝나야 합니다. 제품 목록 세부 정보에 사용 된 것과 다른 언어로 지원 문서를 제공할 수도 있습니다.

제품을 보다 쉽게 만들 수 있도록 이러한 항목 중 일부를 미리 준비 합니다. 별도로 언급 하지 않는 한 다음 항목이 필요 합니다.

**이름**: 상업적 marketplace에서 제품의 제목으로 표시 됩니다. 이름은 상표로 등록될 수 있습니다. 이는 사용자가 상표 및 저작권 기호인 경우를 제외 하 고는이 모 지를 포함할 수 없으며 50 자로 제한 되어야 합니다.

**검색 결과 요약**: 100 자 이하로 제품의 목적 또는 목표를 설명 합니다. 이 요약은 상용 marketplace에서 검색 결과를 나열 하는 데 사용 됩니다. 제목과 동일 하지 않아야 합니다. 상위 SEO 키워드를 포함 하는 것이 좋습니다.

**간단한 설명**: 제품에 대 한 간단한 설명을 제공 합니다 (최대 256 자). Azure Portal의 제품 목록에 표시 됩니다.

**설명**: 3000 자 이하로 제품을 설명 합니다. 이 설명은 상업적 marketplace 목록에 표시 됩니다. 가치 제안, 주요 혜택, 범주 또는 업계 연결 및 필요한 공개를 포함 하는 것이 좋습니다.

다음은 설명 작성에 대한 몇 가지 팁입니다.

* 처음 몇 문장에서 제품의 가치를 명확하게 설명합니다.
    * 제품의 혜택을 받는 사용자 유형입니다.
    * 고객이 요구 하거나 제품 주소를 발급 하는 것입니다.
* 처음 몇 문장은 검색 결과에 표시될 수 있다는 점에 유의하세요.
* 산업별 어휘를 사용 합니다.

HTML 태그를 사용 하 여 설명 서식을 지정할 수 있습니다. HTML 서식 지정에 대 한 자세한 내용은 [상용 marketplace 제품 설명에서 지원 되는 html 태그](./supported-html-tags.md)를 참조 하세요.

**개인 정보 취급 방침 링크**: 사이트에서 호스트 되는 개인 정보 취급 방침에 대 한 URL을 제공 합니다. 귀하는 귀하의 제품이 개인 정보 법률 및 규정을 준수 하 고 유효한 개인 정보 취급 방침을 제공 하는지 확인 해야 합니다.

**유용한 링크** (선택 사항): 제품에 대 한 추가 온라인 문서를 업로드 합니다.

**연락처 정보**: 회사에서 두 사람의 이름, 전자 메일 주소 및 전화 번호를 제공 합니다 (둘 중 하나). 지원 연락처와 엔지니어링 담당자입니다. 이 정보를 사용 하 여 귀하의 제품에 대 한 정보와 통신 합니다. 이 정보는 고객에 게 표시 되지 않지만, CSP (클라우드 솔루션 공급자) 파트너에 게 제공 될 수 있습니다.

**지원 url** (선택 사항): Azure Global customers 및/또는 Azure Government 고객에 대 한 지원 웹 사이트가 있는 경우 해당 url을 제공 합니다.

**Marketplace 미디어 – 로고**: 제품의 큰 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 중간 및 작은 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로 이러한 로고를 바꿀 수 있습니다.

* Azure Marketplace에 대 한 제품 목록에 표시 되는 긴 로고 (216 x 216에서 350 x 350 px)가 표시 됩니다.
* 새 리소스를 만들 때 중간 로고 (90 x 90 px)가 표시 됩니다.
* 작은 로고 (48 x 48 px)는 Azure Marketplace 검색 결과에 사용 됩니다.

로고에 대해서는 다음 지침을 따르세요.

* 이미지가 늘어나지 않았는지 확인 합니다.
* Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수를 제한합니다.
* Azure Portal 색은 흰색 및 검정입니다. 로고의 배경으로 사용 하지 마세요. 로고를 두드러지게 만드는 간단한 기본 색을 사용 하는 것이 좋습니다.
* 투명한 배경을 사용하는 경우 로고 및 텍스트는 흰색, 검은색 또는 파란색이 아니어야 합니다.
* 로고의 모양과 느낌은 평평 해야 합니다. 그라데이션을 피합니다. 로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 마십시오.

**Marketplace 미디어 – 스크린샷** (선택 사항): 제품의 작동 방식을 보여 주는 최대 5 개의 이미지를 추가 합니다. 모든 이미지는 1280 x 720 픽셀 크기 및의 크기 여야 합니다. PNG 형식입니다.

**Marketplace 미디어 – 비디오** (선택 사항): 제품을 설명 하는 최대 5 개의 비디오를 업로드 합니다. 비디오는 YouTube 또는 Vimeo에서 호스트 되 고 미리 보기 (1280 x 720 PNG 파일)가 있어야 합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

미리 보기 대상 사용자는 제품을 테스트 하기 위해 Azure Marketplace에 게시 하기 전에 해당 제품에 액세스할 수 있습니다. 파트너 센터의 **대상 미리 보기** 페이지에서 제한 된 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 개인 계획과 다릅니다. 비공개 요금제는 사용자가 선택 하는 특정 대상에만 사용할 수 있도록 하는 계획입니다. 이렇게 하면 사용자 지정 요금제를 특정 고객과 협상할 수 있습니다.

Microsoft 계정 (MSA) 또는 Azure Active Directory (Azure AD) 전자 메일 주소로 초대를 보낼 수 있습니다. 전자 메일 주소를 최대 10 개까지 수동으로 추가 하거나 .csv 파일을 사용 하 여 최대 20 개까지 가져오세요. 제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용 또는 업데이트를 테스트 하기 위한 미리 보기 대상을 계속 정의할 수 있습니다.

## <a name="plans-and-pricing"></a>플랜 및 가격 책정

관리 서비스 제공에는 하나 이상의 계획이 필요 합니다. 계획은 솔루션 범위, 제한 및 관련 가격 (해당 하는 경우)을 정의 합니다. 제품에 대 한 여러 요금제를 만들어 고객에 게 다른 기술 및 가격 옵션을 제공할 수 있습니다. 비공개 요금제를 비롯 한 계획에 대 한 일반적인 지침은 [상용 marketplace 제품에 대 한 계획 및 가격 책정](plans-pricing.md)을 참조 하세요.

관리 서비스는 하나의 가격 책정 모델만 지원 합니다. **BYOL (사용자 라이선스 보유)**. 즉, 고객에 게 직접 요금을 청구 하 고 Microsoft는 요금을 부과 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [관리되는 서비스 제품 만들기](./create-managed-service-offer.md)
* [목록에 제품 추가 모범 사례](./gtm-offer-listing-best-practices.md)