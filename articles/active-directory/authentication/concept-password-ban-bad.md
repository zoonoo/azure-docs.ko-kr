---
title: Azure AD에서 동적으로 금지된 암호
description: Azure AD 동적으로 금지된 암호를 사용하여 환경에서 취약한 암호를 금지할 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7dcac665dadef7f3f192e7f0e359b6b7c244bde
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287029"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>조직에서 잘못된 암호 제거

업계의 주요 기업들은 여러 위치에서 동일한 암호를 사용하지 않아야 하고, Password123 같은 간단한 암호도 사용하지 말라고 조언합니다. 사용자가 규정을 준수하게 만들려면 조직에서 어떻게 해야 할까요? 사용자가 일반적인 암호 또는 최근에 데이터 보안 위반 사건에 연루된 것으로 알려진 암호를 사용하지 못하게 하려면 어떻게 해야 할까요?

## <a name="global-banned-password-list"></a>전역 금지 암호 목록

Microsoft는 항상 한 발 앞서 사이버 범죄에 대처하려고 합니다. Azure AD ID 보호 팀은 일반적으로 사용되는 보안 위험이 있는 암호를 지속적으로 살펴보고 있습니다. 그리고 소위 말하는 전역 금지 암호 목록에서 자주 보이는 암호를 차단합니다. 사이버 범죄자 역시 비슷한 전략을 공격에 사용하므로 Microsoft는 이 목록의 콘텐츠를 공개적으로 게시하지 않습니다. 이와 같이 취약한 암호는 Microsoft 고객에게 실질적인 위협으로 다가오기 전에 차단됩니다. 현재 보안 노력에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/operations/security-intelligence-report)를 참조하세요.

## <a name="custom-banned-password-list"></a>사용자 지정 금지 암호 목록

일부 조직은 Microsoft에서 사용자 금지 암호 목록이라고 부르는 전역 금지 암호 목록 위에 자체적인 사용자 지정을 추가하여 보안을 강화하려 합니다. Contoso 같은 대기업 고객은 브랜드 이름, 회사 관련 용어 또는 기타 항목의 변형을 차단하도록 선택할 수 있습니다.

사용자 지정 금지 암호 목록 및 온-프레미스 Active Directory 통합을 사용하는 기능은 Azure Portal을 통해 관리됩니다.

![Azure Portal의 인증 방법에서 사용자 지정 금지 암호 목록을 수정할 수 있습니다.](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>온-프레미스 하이브리드 시나리오

클라우드 전용 계정을 보호하면 도움이 되지만, 많은 조직에서는 온-프레미스 Windows Server Active Directory를 비롯한 하이브리드 시나리오를 유지하고 있습니다. Windows Server Active Directory 에이전트 온-프레미스와 기존 인프라에 금지 된 암호 목록을 확장에 대 한 Azure AD 암호 보호를 설치 하는 것이 가능 합니다. 이제 온-프레미스에서 암호를 변경, 설정 또는 다시 설정하는 사용자와 관리자는 클라우드 전용 사용자와 동일한 암호 정책을 준수해야 합니다.

## <a name="how-are-passwords-evaluated"></a>암호 평가 방법

사용자가 암호를 변경하거나 재설정할 때마다 새 암호를 전역 및 사용자 지정 금지된 암호 목록(구성되어 있는 경우)과 대조해 유효성을 검사하는 방식으로 암호의 보안 수준과 복잡성을 확인합니다.

사용자 암호에 금지된 암호가 포함되어 있더라도 전체 암호의 보안 수준이 충분히 높으면 해당 암호를 사용할 수 있습니다. 암호를 새로 구성하면 다음 단계가 진행되어 암호의 전반적인 보안 수준을 평가함으로써 암호를 수락할지 아니면 거부할지가 결정됩니다.

### <a name="step-1-normalization"></a>1단계: 정규화

먼저 새 암호에 대해 정규화 프로세스가 진행됩니다. 이 과정을 통해 소수의 금지된 암호 세트를 취약할 가능성이 있는 대규모 암호 세트에 매핑할 수 있습니다.

정규화는 두 부분으로 진행됩니다.  먼저 대문자가 모두 소문자로 변경됩니다.  그런 후에 다음과 같이 일반 문자 대체가 수행됩니다.  

| 원래 문자  | 대체되는 문자 |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

예: "blank"라는 암호가 금지된 상태에서 사용자가 암호를 "Bl@nK"로 변경하려 한다고 가정해 보겠습니다. "Bl@nk"는 구체적으로 금지되어 있지는 않지만 정규화 프로세스에서는 이 암호를 금지된 암호인 "blank"로 변환합니다.

### <a name="step-2-check-if-password-is-considered-banned"></a>2단계: 암호가 금지된 항목으로 간주되는지 확인

#### <a name="fuzzy-matching-behavior"></a>유사 일치 동작

유사 일치는 정규화된 암호가 전역 또는 사용자 지정 금지된 암호 목록에 있는 암호를 포함하는지를 확인하는 데 사용됩니다. 일치 프로세스는 편집 거리 1 비교를 기준으로 합니다.  

예: "abcdef"라는 암호가 금지된 상태에서 사용자가 암호를 다음 중 하나로 변경하려 한다고 가정해 보겠습니다.

'abcdeg'    *(마지막 문자가 'f'에서 'g'로 변경됨)* 'abcdefg'   *(끝에 'g'가 추가됨)* 'abcde'     *(맨 끝의 'f'가 삭제됨)*

위의 각 암호는 금지된 암호인 "abcdef"와 구체적으로 일치하지는 않습니다. 그러나 각 예는 금지된 토큰 'abcdef'와 편집 거리 1 이내에 있으므로 모두 "abcdef"와 일치하는 암호로 간주됩니다.

#### <a name="substring-matching-on-specific-terms"></a>부분 문자열 일치(특정 용어)

부분 문자열 일치는 정규화된 암호에서 사용자 이름/성 및 테넌트 이름을 확인하는 데 사용됩니다. Active Directory 도메인 컨트롤러에서 암호 유효성을 검사할 때는 테넌트 이름 일치가 수행되지 않습니다.

예: John Doe 사용자가 암호를 "J0hn123fb"로 재설정하려 한다고 가정해 보겠습니다. 정규화 후에 이 암호는 "john123fb"가 됩니다. 그러므로 부분 문자열 일치에서는 해당 암호에 사용자 이름인 "John"이 포함되어 있음이 확인됩니다. "J0hn123fb"는 금지된 암호 목록에 구체적으로 포함되어 있지는 않지만 부분 문자열 확인에서 암호의 "John"이 확인되었으므로 이 암호는 거부됩니다.

#### <a name="score-calculation"></a>점수 계산

다음 단계에서는 사용자의 정규화된 새 암호에서 금지된 암호의 모든 인스턴스를 확인합니다. 그렇다면

1. 사용자의 암호에서 확인되는 금지된 각 암호가 1점으로 계산됩니다.
2. 나머지 각 고유 문자도 1점으로 계산됩니다.
3. 합산 점수가 5점 이상인 암호만 수락됩니다.

다음 두 예제에서는 Contoso가 Azure AD 암호 보호를 사용 중이며 사용자 지정 목록에 "contoso"가 포함되어 있다고 가정하겠습니다. 그리고 전역 목록에는 "blank"가 포함되어 있다고 가정합니다.

예제: 사용자가 암호 "C0ntos0Blank12"로 변경합니다.

정규화 후에 이 암호는 "contosoblank12"가 됩니다. 일치 프로세스에서는 이 암호에 금지된 암호 2개(contoso, blank)가 포함되어 있음이 확인됩니다. 따라서 이 암호의 점수는

[contoso] + [공백] [1] + [2] = 4 포인트 + 5 지점에서이 암호는 작으므로, 거부 됩니다.

예제: 사용자가 암호 "ContoS0Bl@nkf9!"로 변경합니다.

정규화 후에 이 암호는 "contosoblankf9!"가 됩니다. 일치 프로세스에서는 이 암호에 금지된 암호 2개(contoso, blank)가 포함되어 있음이 확인됩니다. 따라서 이 암호의 점수는

[contoso] + [blank] + [f] + [9] + [!] = 5점입니다. 이 암호는 점수가 5점 이상이므로 수락됩니다.

   > [!IMPORTANT]
   > 금지된 암호 알고리즘과 전역 목록은 지속적인 보안 분석 및 연구를 토대로 Azure에서 언제든지 변경할 수 있으며 실제로 변경됩니다. 온-프레미스 DC 에이전트 서비스의 경우에는 DC 에이전트 소프트웨어를 다시 설치해야 업데이트된 알고리즘이 적용됩니다.

## <a name="license-requirements"></a>라이선스 요구 사항

|   | 전역 금지 암호 목록을 사용하여 Azure AD 암호 보호 | 사용자 지정 금지 암호 목록을 사용하여 Azure AD 암호 보호|
| --- | --- | --- |
| 클라우드 전용 사용자 | Azure AD Free | Azure AD Basic |
| 온-프레미스 Windows Server Active Directory에서 동기화된 사용자 | Azure AD Premium P1 또는 P2 | Azure AD Premium P1 또는 P2 |

비용을 비롯한 추가 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

사용자가 암호를 금지된 문자열로 재설정하려고 하면 다음과 같은 오류 메시지가 표시됩니다.

암호에 쉽게 추측할 수 있는 단어, 구 또는 패턴이 포함되어 있습니다. 다른 암호로 다시 시도하세요.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 금지 암호 목록 구성](howto-password-ban-bad.md)
* [온-프레미스에서 Azure AD 암호 보호 에이전트 사용](howto-password-ban-bad-on-premises-deploy.md)
