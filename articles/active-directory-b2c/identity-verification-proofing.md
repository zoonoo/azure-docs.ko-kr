---
title: Azure AD B2C에 대한 ID 교정 및 검증
titleSuffix: Azure AD B2C
description: ID 교정 및 검증 솔루션을 제공하기 위해 Azure AD B2C와 통합하는 파트너에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/23/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 536c63fec9958823191f3b01ad2142022832ef23
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124386"
---
# <a name="identity-verification-and-proofing-partners"></a>ID 검증 및 교정 파트너

Azure AD B2C 파트너를 통해 고객은 계정 등록 또는 액세스를 허용하기 전에 최종 사용자의 ID 검증 및 교정 ID 검증 및 교정을 통해 문서, 지식 기반 정보 및 이용량을 확인할 수 있습니다.

흐름을 설명하는 개략적인 아키텍처 다이어그램입니다.

![ID 교정 흐름을 보여 주는 다이어그램](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft는 다음과 같은 ISV 파트너와 협력합니다.

| ISV 파트너 | 설명 및 통합 연습 |
|:-------------------------|:--------------|
|![Experian 로고의 스크린샷](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md)은 사기 행위를 방지하기 위해 사용자 특성을 기반으로 위험 평가를 수행하는 ID 검증 및 교정 공급자입니다. |
|![IDology 로고의 스크린샷](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md)는 ID 검증 솔루션, 사기 행위 방지 솔루션, 규정 준수 솔루션 등을 제공하는 ID 검증 및 교정 공급자입니다.|
|![Jumio 로고의 스크린샷](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md)는 ID 확인 서비스이며, 이 서비스를 사용하면 실시간 자동화 ID를 확인하고 고객 데이터를 보호할 수 있습니다. |
| ![LexisNexis 로고의 스크린샷](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md)는 사용자 ID를 확인하고 사용자의 디바이스에 따라 포괄적인 위험 평가를 제공하는 프로파일링 및 ID 유효성 검사 공급자입니다. |
| ![Onfido 로고의 스크린샷](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md)는 문서 ID 및 얼굴 인식 검증 솔루션이며 회사가 실시간으로 *고객 확인* 및 ID 요구 사항을 충족할 수 있도록 합니다.  |

## <a name="additional-information"></a>추가 정보

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

## <a name="next-steps"></a>다음 단계

언급된 테이블에서 파트너를 선택하여 파트너의 솔루션을 Azure AD B2C와 통합하는 방법을 알아봅니다.