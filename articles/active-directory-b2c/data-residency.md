---
title: 지역 가용성 및 데이터 상주
titleSuffix: Azure AD B2C
description: 지역 가용성, 데이터 상주 및 Azure Active Directory B2C 미리 보기 테 넌 트에 대 한 정보입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 9cb7a97b3f57ee7ac10babc53ee2263d51838777
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92309675"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: 지역 가용성 및 데이터 상주

지역 가용성 및 데이터 상주는 나머지 Azure에서 Azure AD B2C에 다르게 적용되는 두 가지 매우 다른 개념입니다. 이 문서에서는 이러한 두 개념 간의 차이점을 설명 하 고 Azure와 Azure AD B2C에 적용 되는 방식을 비교 합니다.

Azure AD B2C는 **미국, 유럽 또는 아시아 태평양**의 **데이터 상주** 옵션을 사용 하 여 **전 세계에서 일반적으로 사용할 수 있습니다** .

[지역 가용성](#region-availability)은 서비스를 사용할 수 있는 위치를 나타냅니다.

[데이터 상주](#data-residency)는 사용자 데이터가 저장된 위치를 나타냅니다.

## <a name="region-availability"></a>지역 가용성

Azure AD B2C는 Azure 퍼블릭 클라우드를 통해 전 세계에서 사용할 수 있습니다.

이는 일반적으로 *데이터 상주*을 사용 하는 몇 가지 다른 Azure 서비스 *와 모델* 의 차이가 있습니다. Azure의 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 페이지 및 [Azure Active Directory B2C 가격](https://azure.microsoft.com/pricing/details/active-directory-b2c/)에서 이와 같은 예제를 확인할 수 있습니다.

## <a name="data-residency"></a>데이터 상주

Azure AD B2C는 미국, 유럽 또는 아시아 태평양 지역에 사용자 데이터를 저장 합니다.

데이터 상주는 [Azure AD B2C 테 넌 트를 만들](tutorial-create-tenant.md)때 선택 하는 국가/지역에 따라 결정 됩니다.

![국가 또는 지역을 선택 하는 테 넌 트 만들기 폼의 스크린샷](./media/data-residency/data-residency-b2c-tenant.png)

데이터는 **미국** 에 거주 하는 국가/지역에 있습니다.

> 미국 (US), 캐나다 (캐나다), 코스타리카 (CR), 도미니카 공화국 (DO), 엘살바도르 (SV), 과테말라 (GT), 멕시코 (MX), 파나마 (PA), 푸에르토리코 (PR) 및 트리니다드 & 토바고 (TT)

데이터는 다음 국가/지역에 대해 **유럽** 에 상주 합니다.

> 알제리 (AR-DZ), 오스트리아 (AT), 아제르바이잔 (AZ), 바레인 (BH), 벨로루시 (BY), 벨기에 (be), 불가리아 (BG), 크로아티아 (HR), 키프로스 (CY), 체코어 공화국 (CZ), 덴마크 (진한), 이집트 (예:), 에스토니아 (EE), 핀란드 (FT), 프랑스 (FR), 독일 (DE), 그리스 (GR), 헝가리 (HU-HU), 아이슬란드 (IS), 아일랜드 (IE), 이스라엘 (IL), 이탈리아 (IT), 요르단 (KW), 라트비아 (KZ), 케냐 (), 라트비아 (), 레바논 (KG), 리히텐슈타인 (LI), 리투아니아 (LT) , 룩셈부르크 (LU), 북부 마케도니아 (ML), 몰타 (MT), 몬테네그로 (ME), 모로코 (MA), 네덜란드 (NL), 나이지리아 (고), 노르웨이 (NO), 오만 (OM), 파키스탄 (PK), 폴란드 (PL), 포르투갈 (PT), 카타르 (QA), 루마니아 (RO), 러시아 (r u), 사우디아라비아 (SA), 세르비아 (RS), 슬로바키아 (가 나), 슬로베니아 (ST), 남아프리카 공화국 (ZA), 스페인 (ES), 스웨덴 (TN), 터키 (TR (TR), 우크라이나 (UA), 영국 아랍 (AE), 영국 (GB)

데이터는 다음 국가/지역에 대 한 **아시아 태평양** 에 있습니다.

> 아프가니스탄 (AF), 홍콩 특별 행정구 (HK), 인도 (IN), 인도네시아 (ID), 일본 (JP), 대한민국 (KR), 말레이시아 (내), 필리핀 (PH), 싱가포르 (SG), 스리랑카 (LK), 대만 (HY) 및 태국 (TH).

다음 국가/지역은 목록에 추가 되 고 있습니다. 이제 위의 국가/지역을 선택하여 Azure AD B2C를 사용할 수 있습니다.

> 아르헨티나, 오스트레일리아, 브라질, 칠레, 콜롬비아, 에콰도르, 이라크, 뉴질랜드, 파라과이, 페루, 우루과이 및 베네수엘라.

## <a name="remote-profile-solution"></a>원격 프로필 솔루션

[사용자 지정 정책을](custom-policy-overview.md)Azure AD B2C 사용 하면 [RESTful API 서비스](custom-policy-rest-api-intro.md)와 통합 하 여 원격 데이터베이스 (예: 마케팅 데이터베이스, CRM 시스템 또는 기간 업무 (lob) 응용 프로그램)에서 사용자 프로필을 저장 하 고 읽을 수 있습니다.  
- 등록 및 프로필 편집 흐름 중 Azure AD B2C는 사용자 지정 REST API를 호출 하 여 사용자 프로필을 원격 데이터 원본에 유지 합니다. 사용자의 자격 증명은 Azure AD B2C directory에 저장 됩니다. 
- 로그인 할 때 로컬 또는 소셜 계정으로 자격 증명을 확인 한 후 Azure AD B2C는 사용자의 고유 식별자를 사용자 기본 키 (전자 메일 주소 또는 사용자 objectId)로 보내는 REST API를 호출 합니다. REST API은 원격 데이터베이스에서 데이터를 읽고 사용자 프로필을 반환 합니다.  

등록, 프로필 편집 또는 로그인이 완료 되 면 응용 프로그램으로 반환 되는 액세스 토큰에 사용자 프로필이 포함 Azure AD B2C 합니다. 자세한 내용은 GitHub의 [Azure AD B2C 원격 프로필 샘플 솔루션](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) 을 참조 하세요.

## <a name="preview-tenant"></a>미리 보기 테넌트

Azure AD B2c의 미리 보기 기간 중에 B2C 테 넌 트를 만든 경우 테 넌 트 **형식이** **미리 보기 테 넌**트 라고 표시 될 수 있습니다.

이 경우 테 넌 트를 개발 및 테스트 목적 으로만 사용 해야 합니다. 프로덕션 응용 프로그램에 미리 보기 테 넌 트를 사용 하지 마세요.

Preview B2C 테 넌 트에서 프로덕션 규모 B2C 테 넌 트로의 **마이그레이션 경로가 없습니다** . 프로덕션 응용 프로그램에 대 한 새 B2C 테 넌 트를 만들어야 합니다.

미리 보기 B2C 테 넌 트를 삭제 하 고 동일한 도메인 이름으로 프로덕션 규모 B2C 테 넌 트를 만들 때 알려진 문제가 있습니다. *다른 도메인 이름으로 프로덕션 규모 B2C 테 넌 트를 만들어야*합니다.

![미리 보기 테 넌 트 형식의 스크린샷.](./media/data-residency/preview-b2c-tenant.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 테 넌 트를 만듭니다](tutorial-create-tenant.md).
