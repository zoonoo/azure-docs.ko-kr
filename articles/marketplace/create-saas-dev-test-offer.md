---
title: 테스트 상품 만들기
description: Microsoft 파트너 센터의 상업용 Marketplace 프로그램에서 프로덕션 제품을 테스트하기 위한 별도의 개발 제품을 만드는 방법입니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 318eebcca3053be2164a1fc52905f36f26b11a6d
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331127"
---
# <a name="create-a-test-offer"></a>테스트 상품 만들기

프로덕션 제품의 별도 환경에서 개발하려면 별도의 테스트/개발(DEV) 제품 및 프로덕션(PROD) 제품을 만듭니다. 별도의 DEV 제품을 사용하는 이점에 대한 자세한 내용은 [SaaS 제품 계획](plan-saas-offer.md#test-offer)을 참조하세요.

대부분의 설정은 DEV 및 PROD 제품에서 동일하게 구성합니다. 예를 들어 공식 마케팅 언어와 자산(예: 스크린샷 및 로고)은 동일해야 합니다. 구성이 동일한 경우 DEV 제품의 계획에서 PROD 제품 계획에 대한 필드를 복사하여 붙여넣을 수 있습니다.

다음 섹션에서는 DEV와 PROD 제품의 구성 차이점에 대해 설명합니다.

## <a name="offer-setup-page"></a>제품 설치 페이지

두 제품의 **별칭** 상자에 동일한 별칭을 사용하는 것이 좋지만 DEV 제품의 별칭에 "_test"을 추가하는 것이 좋습니다. 예를 들어, PROD 제품의 별칭이 "contososolution"인 경우 DEV 제품의 별칭은 "contososolution_test"입니다. 이러한 방식으로 PROD 제품에서 DEV 제품을 쉽게 식별할 수 있습니다.

**잠재 고객** 섹션에서 DEV 제품에 대한 Azure 테이블 또는 테스트 CRM 환경을 사용합니다. PROD 제품에 대하여 의도하는 잠재 고객 관리 시스템을 사용합니다.

## <a name="properties-page"></a>속성 페이지

이 페이지를 DEV 및 PROD 제품에서 동일하게 구성합니다.

## <a name="offer-listing-page"></a>제품 목록 페이지

이 페이지를 DEV 및 PROD 제품에서 동일하게 구성합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

DEV 제품에는 자신의 것을 포함하여 개발자 및 테스터의 Azure Active Directory(AAD) 사용자 계정 이름 또는 Microsoft 계정(MSA) 전자 메일 주소를 포함합니다. AAD에서 사용자의 사용자 계정 이름은 해당 사용자의 전자 메일과 다를 수 있습니다. 예를 들어 jane.doe@contoso.com은 적합하지 않지만 janedoe@contoso.com은 적합합니다. 지정된 사용자는 개발 및 테스트 단계 중에 **미리 보기** 링크를 공유할 때 DEV 제품에 액세스할 수 있습니다.

**Go Live 단추** 를 선택하여 제품을 라이브로 게시하기 전에 PROD 제품에 제품을 validate하는 사용자의 Azure AD 사용자 계정 이름 또는 Microsoft 계정 전자 메일을 포함합니다.

## <a name="technical-configuration-page"></a>기술 구성 페이지

이 표에서는 DEV 제품 및 PROD 제품에 대한 설정 간의 차이점을 설명합니다.

***표 1: 기술 구성 차이점***

| 설정 | DEV 제품 | PROD 제품 |
| ------------ | ------------- | ------------- |
| 방문 페이지 URL | 개발/테스트 엔드포인트를 입력합니다. | 프로덕션 엔드포인트를 입력합니다. |
| 연결 웹후크 | 개발/테스트 엔드포인트를 입력합니다. | 프로덕션 엔드포인트를 입력합니다. |
| Azure Active Directory 테넌트 ID | 테스트 앱 등록 테넌트 ID(AAD 디렉터리 ID)를 입력합니다. | 프로덕션 앱 등록 테넌트 ID를 입력합니다. |
| Azure Active Directory 애플리케이션 ID | 테스트 앱 등록 애플리케이션 ID(클라이언트 ID)를 입력합니다. | 프로덕션 앱 등록 애플리케이션 ID를 입력합니다. |
||||

## <a name="plan-overview-page"></a>계획 개요 페이지

계획을 만들 때 **_test** 를 사용하여 DEV 제품에 계획 ID를 추가하는 것 외에는 모든 DEV 및 PROD 제품에서 동일한 _계획 ID_ 와 _계획 이름_ 을 사용하는 것이 좋습니다. 예를 들어, PROD 제품의 계획 ID가 "enterprise" 인 경우 DEV 제품의 계획 ID는 "enterprise_test"로 해야 합니다. 이러한 방식으로 PROD 제품에서 DEV 제품을 쉽게 식별할 수 있습니다. 제품에 가장 적합한 가격 책정 모델 및 가격을 사용하여 PROD 제품에서 계획을 만듭니다.

### <a name="plan-listing"></a>플랜 목록

**계획 개요** > **계획 목록** 탭에서 DEV 및 PROD 계획에 동일한 계획 설명을 입력합니다.

### <a name="pricing-and-availability-page"></a>가격 책정 및 가용성 페이지

이 섹션에서는 **계획 개요** > **가격 책정 및 가용성** 페이지를 완료하는 방법에 대한 참고 자료를 제공합니다.

#### <a name="markets"></a>시장

DEV 및 PROD 제품에 대해 동일한 시장을 선택합니다.

#### <a name="pricing"></a>가격 책정

DEV 제품을 사용하여 가격 책정 모델을 실험해 보세요. 가장 적합한 가격 책정 모델 또는 모델을 확인한 후에는 가격 책정 모델 및 가격을 사용하여 PROD 제품에서 계획을 만듭니다.

DEV 제품은 평가판 또는 저가형 플랜으로 제공되어야 합니다. PROD 제품은 고객에게 요금을 부과하고자 하는 가격을 갖습니다.

> [!IMPORTANT]
> 미리 보기에서의 구매는 모든 DEV 및 PROD 제품에 대해 처리됩니다. 월 $100짜리 제품이라면 회사에 $100가 청구됩니다. 이 경우 [지원 티켓](support.md)을 열 수 있습니다. 그러면 전체 금액에 대해 지급액을 산정하고 매장 서비스 요금을 부과하지 않습니다.

#### <a name="pricing-model"></a>가격 책정 모델

DEV와 PROD 제품 계획에서는 동일한 가격 책정 모델을 사용합니다. 예를 들어, PROD 제품의 계획이 월별 청구 기간을 사용하여 정액인 경우 동일한 모델을 사용하여 DEV 제품에서 계획을 구성합니다.

Marketplace 사용자 지정 측정기 차원을 포함하여 가격 책정 모델을 테스트하는 데 필요한 비용을 줄이려면 PROD 제품보다 DEV 제품에서 낮은 가격으로 **가격 책정 및 가용성** 탭의 **가격 책정** 섹션을 구성하는 것이 좋습니다. 다음은 DEV 제품 계획에 대한 가격을 설정할 때 수행할 수 있는 몇 가지 지침입니다.

***표 2: 가격 책정 참고 자료***

| 가격 | 의견 |
| ------------ | ------------- |
| $0.00 | 총 트랜잭션 비용을 0으로 설정하면 재정적 영향을 받지 않습니다. 이 가격을 사용하여 계량 API를 호출하거나 솔루션을 개발하는 동안 제품에서 구매 계획을 테스트할 수 있습니다. |
| $0.01 - $49.99 | 이 가격 범위를 사용하여 분석, 보고 및 구매 프로세스를 테스트합니다. |
| $50.00 - $100.00 | 이 가격 범위를 사용하여 지급액을 테스트합니다. 지불 일정에 대한 자세한 내용은 [지급 일정 및 프로세스](/partner-center/payout-policy-details)를 참조하세요. |
|||

> [!IMPORTANT]
>  테스트에 대한 매장 서비스 요금을 부과하지 않으려면 테스트 구매 후 7일 이내에 [지원 티켓](support.md)을 엽니다.

#### <a name="free-trial"></a>평가판

DEV 제품에 대한 평가판을 사용하도록 설정하지 마세요.

## <a name="co-sell-with-microsoft-page"></a>Microsoft와 공동 판매 페이지

DEV 제품의 **Microsoft 탭으로 공동 판매** 를 구성하지 마세요.

## <a name="resell-through-csps"></a>CSP를 통한 재판매

DEV 제품의 **CSP 탭을 통한 재판매** 를 구성하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 제품 테스트 및 게시](test-publish-saas-offer.md)
