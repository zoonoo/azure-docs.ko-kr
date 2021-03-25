---
title: 테스트 제안 만들기
description: Microsoft 파트너 센터의 상용 marketplace 프로그램에서 프로덕션 제품을 테스트 하기 위한 별도의 개발 제품을 만드는 방법입니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 58649e9a864e64ab5781cff3b663e190dac50cb6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050664"
---
# <a name="create-a-test-offer"></a>테스트 제안 만들기

프로덕션 제품의 별도 환경에서 개발 하려면 별도의 개발 (테스트 및 개발) 제품 및 별도의 프로덕션 (PROD) 제품을 만듭니다. 별도의 개발 제안을 사용 하는 이점에 대 한 자세한 내용은 [SaaS 제안 계획](plan-saas-offer.md#test-offer)을 참조 하세요.

대부분의 설정은 개발 및 PROD 제품에서 동일 하 게 구성 합니다. 예를 들어 공식 마케팅 언어와 자산 (예: 스크린샷 및 로고)은 동일 해야 합니다. 구성이 동일한 경우 개발 제품의 계획에서 PROD 제안의 계획에 대 한 필드를 복사 하 여 붙여 넣을 수 있습니다.

다음 섹션에서는 DEV와 PROD 제품의 구성 차이점에 대해 설명 합니다.

## <a name="offer-setup-page"></a>제품 설치 페이지

두 제품의 **별칭** 상자에 동일한 별칭을 사용 하는 것이 좋지만 DEV 제품의 별칭에 "_test"을 추가 하는 것이 좋습니다. 예를 들어, PROD 제품의 별칭이 "contososolution" 인 경우 DEV 제품의 별칭은 "contososolution_test"입니다. 이러한 방식으로 PROD 제품에서 개발 제품을 쉽게 식별할 수 있습니다.

**고객 리드** 섹션에서 및 Azure table을 사용 하 고 개발 제품을 위한 테스트 CRM 환경을 사용 합니다. PROD 제품에 의도 한 리드 관리 시스템을 사용 합니다.

## <a name="properties-page"></a>속성 페이지

이 페이지를 개발 및 PROD 제품에서 동일 하 게 구성 합니다.

## <a name="offer-listing-page"></a>제품 목록 페이지

이 페이지를 개발 및 PROD 제품에서 동일 하 게 구성 합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

개발자 제품에는 사용자를 포함 하 여 개발자와 테스터의 Azure Active Directory (AAD) 사용자 계정 이름 또는 Microsoft 계정 (MSA) 전자 메일 주소를 포함 합니다. AAD에서 사용자의 사용자 계정 이름은 사용자의 전자 메일과 다를 수 있습니다. 예를 들어 jane.doe@contoso.com 는 작동 하지 않지만는 janedoe@contoso.com 작동 합니다. 이러한 사용자는 개발 및 테스트 단계 중에 **미리 보기** 링크를 공유할 때 DEV 제품에 액세스할 수 있습니다.

PROD 제품에서 **Live Go 단추** 를 선택 하 여 제품을 라이브로 게시 하기 전에 제품의 유효성을 검사 하는 사용자의 Azure AD 사용자 계정 이름 또는 Microsoft 계정 메일을 포함 합니다.

## <a name="technical-configuration-page"></a>기술 구성 페이지

이 표에서는 개발 제안 및 PROD 제품에 대 한 설정 간의 차이점을 설명 합니다.

***표 1: 기술 구성 차이점***

| 설정 | 개발자 제안 | PROD 제안 |
| ------------ | ------------- | ------------- |
| 방문 페이지 URL | 개발/테스트 끝점을 입력 합니다. | 프로덕션 끝점을 입력 합니다. |
| 연결 웹후크 | 개발/테스트 끝점을 입력 합니다. | 프로덕션 끝점을 입력 합니다. |
| Azure Active Directory 테 넌 트 ID | 테스트 앱 등록 테 넌 트 ID (AAD 디렉터리 ID)를 입력 합니다. | 프로덕션 앱 등록 테 넌 트 ID를 입력 합니다. |
| Azure Active Directory 애플리케이션 ID | 테스트 앱 등록 응용 프로그램 ID (클라이언트 ID)를 입력 합니다. | 프로덕션 앱 등록 응용 프로그램 ID를 입력 합니다. |
||||

## <a name="plan-overview-page"></a>계획 개요 페이지

계획을 만들 때 **_test** 를 사용 하 여 개발 제품에 계획 id를 추가 하는 것을 제외 하 고 DEV 및 PROD 제품 모두에서 동일한 _계획 id_ 와 _계획 이름을_ 사용 하는 것이 좋습니다. 예를 들어, PROD 제안의 계획 ID가 "enterprise" 인 경우 DEV 제안의 계획 ID는 "enterprise_test" 여야 합니다. 이러한 방식으로 PROD 제품에서 개발 제품을 쉽게 식별할 수 있습니다. 제품에 가장 적합 한 가격 책정 모델 및 가격을 사용 하 여 PROD 제품에서 계획을 만듭니다.

### <a name="plan-listing"></a>플랜 목록

**계획 개요**  >  **계획 목록** 탭에서 DEV 및 PROD 계획에 동일한 계획 설명을 입력 합니다.

### <a name="pricing-and-availability-page"></a>가격 책정 및 가용성 페이지

이 섹션에서는 **계획 개요**  >  **가격 책정 및 가용성** 페이지를 완료 하는 방법에 대 한 지침을 제공 합니다.

#### <a name="markets"></a>시장

개발 및 PROD 제품에 대해 동일한 시장을 선택 합니다.

#### <a name="pricing"></a>가격 책정

개발자 제품을 사용 하 여 가격 책정 모델을 시험해 보세요. 가장 적합 한 가격 책정 모델 또는 모델을 확인 한 후에는 가격 책정 모델 및 가격을 사용 하 여 PROD 제품에서 계획을 만듭니다.

개발 제품은 계획의 가격이 0 또는 매우 낮은 요금제를 사용 해야 합니다. PROD 제품은 고객에 게 요금을 부과 하고자 하는 가격을 갖습니다.

> [!NOTE]
> SkimmingPurchases 미리 보기에서 만든 내용이 개발 및 PROD 제품 모두에 대해 처리 되는 경우에도 사용자에 게 알리는 정보입니다. 제품에 $100/mo 가격이 있으면 회사는 $100에 대 한 요금이 청구 됩니다. 이 경우 [지원 티켓](support.md) 을 열 수 있습니다. 그러면 전체 금액에 대해 지급를 발급 하 고 에이전시 요금을 부과 하지 않습니다.

#### <a name="pricing-model"></a>가격 책정 모델

개발 및 PROD 제품 계획에서 동일한 가격 책정 모델을 사용 합니다. 예를 들어, PROD 제안의 요금제가 월별 청구 기간을 사용 하 여 정액 인 경우 동일한 모델을 사용 하 여 DEV 제품에서 계획을 구성 합니다.

Marketplace 사용자 지정 측정기 차원을 포함 하 여 가격 책정 모델을 테스트 하는 데 필요한 비용을 줄이려면 개발 제안에서 PROD 제품 보다 낮은 가격으로 가격 책정 **및 가용성** 탭의 **가격 책정** 섹션을 구성 하는 것이 좋습니다. 다음은 DEV 제품의 계획에 대 한 가격을 설정할 때 수행할 수 있는 몇 가지 지침입니다.

***표 2: 가격 책정 지침***

| 가격 | 의견 |
| ------------ | ------------- |
| $0.00 | 총 트랜잭션 비용을 0으로 설정 하면 재정적 영향을 받지 않습니다. 이 가격을 사용 하 여 계량 Api를 호출 하거나 솔루션을 개발 하는 동안 제품에서 구매 계획을 테스트할 수 있습니다. |
| $0.01-$49.99 | 이 가격 범위를 사용 하 여 분석, 보고 및 구매 프로세스를 테스트 합니다. |
| $50.00 이상 | 이 가격 범위를 사용 하 여 지급를 테스트 합니다. 지불 일정에 대 한 자세한 내용은 [지급 일정 및 프로세스](/partner-center/payout-policy-details)를 참조 하세요. |
|||

테스트에 대 한 처리 요금을 부과 하지 않으려면 [지원 티켓](support.md)을 엽니다.

#### <a name="free-trial"></a>평가판

개발 제품에 대 한 무료 평가판을 사용 하도록 설정 하지 마세요.

## <a name="co-sell-with-microsoft-page"></a>Microsoft 페이지와 공동 판매

DEV 제품의 **Microsoft 탭으로 공동 판매** 를 구성 하지 마세요.

## <a name="resell-through-csps"></a>CSP를 통한 재판매

DEV 제품의 **재판매을 통해 csp** 탭을 구성 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 제품 테스트 및 게시](test-publish-saas-offer.md)
