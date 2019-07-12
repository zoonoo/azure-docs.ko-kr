---
title: Azure Active Directory-암호를 동적으로 금지
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
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703070"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>조직에서 잘못된 암호 제거

업계 최고 리더 들을 사용 하면 복잡 한 확인을 하지 간단 하 게 "Password123"와 같은 여러 위치에서 동일한 암호를 사용할 필요가 알려줍니다. 어떻게 조직 보장할 수는 사용자에 게 모범 사례 지침에 따라는? 어떻게 되었는지 확인할 수는 사용자가 취약 한 암호에 취약 한 암호 또는 심지어 변형을 사용 하지 않는?

강력한 암호에는 초기 단계 사용자에 게 지침을 제공 하는 것입니다. 이 항목에 대한 Microsoft의 현재 지침은 다음 링크에서 찾을 수 있습니다.

[Microsoft 암호 지침](https://www.microsoft.com/research/publication/password-guidance)

훌륭한 지침 것이 중요 하지만 사용한 경우라도 많은 사용자가 취약 한 암호를 선택 하면 결국 여전히 알 수는 있습니다. 검색 및 차단 하는 알려진된 취약 한 암호와 해당 변형을 뿐만 아니라 필요에 따라 조직에 관련 된 추가 취약 한 용어를 차단 하 여 조직의 보호 하는 azure AD 암호 보호 합니다.

현재 보안 노력에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/operations/security-intelligence-report)를 참조하세요.

## <a name="global-banned-password-list"></a>전역 금지 암호 목록

Azure AD Id 보호 팀은 Azure AD 보안 원격 분석 데이터, 자주 사용 되는 약하거나 손상 된 암호 또는 보다 구체적으로, 약한 기본 자주 사용 되는 용어를 기준으로 취약 한 암호를 지속적으로 분석 합니다. 이러한 취약 한 용어를 찾으면 전역 금지 된 암호 목록에 추가 됩니다. 전역 금지 된 암호 목록 내용의 모든 외부 데이터 원본에 기반 하지 않는 합니다. 전역 금지 된 암호 목록은 Azure AD 보안 원격 분석 및 분석의 결과 완전히을 기반으로 합니다.

새 암호를 변경 하거나 모든 테 넌 트의 모든 사용자에 대 한 Azure AD에서 재설정 때마다 현재 버전의 전역 금지 된 암호 목록 암호의 강도 유효성을 검사할 때 입력을 키로 사용 됩니다. 이 유효성 검사 훨씬 더 강력한 암호에 Azure AD는 모든 고객에 대 한 결과입니다.

> [!NOTE]
> 또한 사이버 범죄에 대처 하려고 공격에서 유사한 전략을 사용 합니다. 따라서 Microsoft는이 목록의 콘텐츠를 공개적으로 게시 하지 않습니다.

## <a name="custom-banned-password-list"></a>사용자 지정 금지 암호 목록

일부 조직에서는 Microsoft에서 사용자 지정 금지 된 암호 목록에서 글로벌 금지 된 암호 목록 맨 위에서 자체 사용자 지정을 추가 하 여 보안을 더욱 개선 하려고 합니다. 이 목록에 추가 하는 용어 주로 조직 관련 용어와 같은 것이 좋습니다.

- 브랜드 이름
- 제품 이름
- 위치 (예를 들어 본사) 예:
- 회사별 내부 용어
- 즉 특정 회사에 있는 약어입니다.

용어 금지 된 암호를 사용자 지정 목록에 추가 되 면 암호를 유효성을 검사할 때 전역 금지 된 암호 목록에 다시 추가 될가 있습니다.

> [!NOTE]
> 금지 된 암호를 사용자 지정 목록에 최대 1000 조건의 제한 됩니다. 매우 큰 암호 목록을 차단에 대 한 설계 되지는 않았습니다. 사용자 지정 차단된 암호 목록 이점은 완벽 하 게 활용 하려면 먼저 검토 하는 것이 좋습니다 암호 평가 알고리즘을 이해 하 고 (참조 [암호 평가 되는 방식을](concept-password-ban-bad.md#how-are-passwords-evaluated)) 새 용어를 추가 하기 전에 사용자 지정 차단된 목록입니다. 알고리즘의 작동을 효율적으로 검색 하 고 많은 수의 취약 한 암호 및 변형 차단 기업의 설정 하는 방법 이해.

예를 들어: 고객의 이름이 "Contoso", "Widget" 이라는 제품을 사용 하면 및 런던에 거주 하는 것이 좋습니다. 이러한 고객에 대 한 낭비와 같은 특정 변형 이러한 용어를 차단 하려고 덜 안전한 것:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

대신 것이 훨씬 더 효율적이 고 안전한 주요 기본 용어를 차단 하려면:

- "Contoso"
- "London"
- "Widget"

암호 유효성 검사 알고리즘은 다음 자동으로 차단 약한 변형 위의 조합.

사용자 지정 금지 암호 목록 및 온-프레미스 Active Directory 통합을 사용하는 기능은 Azure Portal을 통해 관리됩니다.

![인증 방법에서 사용자 지정 차단된 암호 목록을 수정 합니다.](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>암호 스프레이 공격 및 타사 손상 된 암호 목록

하나의 Azure AD 암호 보호 혜택 키는 암호 스프레이 공격을 방어할 수 있도록 합니다. 이러한 동작 감지, 계정 잠금 또는 기타 수단을 통해 될 가능성을 크게 증가 되므로 몇 번 이상 지정 된 모든 개별 계정 공격에 대부분의 암호 스프레이 공격 시도 하지 마십시오. 따라서 대부분의 암호 스프레이 공격은 소수의 각 기업에서 계정에 대해 알려진된 약한 암호를 제출 의존 합니다. 이 기술은 공격자가 쉽게 손상 된 계정에서 동시 잠재적인 검색 임계값을 방지 하는 동안 신속 하 게 검색할 수 있습니다.

Azure AD 암호 보호 효율적으로 암호 스프레이 공격에서는 Azure AD에서 볼 수 있듯이 실제 보안 원격 분석 데이터를 기반으로 사용할 수 있는 모든 알려진된 취약 한 암호를 차단 하도록 설계 되었습니다.  Microsoft는 수백만 개의 공개적으로 알려진된 보안 위반을 이전에 손상 된 암호를 열거 하는 타사 웹 사이트입니다. 이러한 수백만 암호에 대 한 무작위 비교를 기반으로 하는 제 3 자 암호 유효성 검사 제품에 대 한 일반적인 것입니다. Microsoft 느낌 이러한 기술이 가장 좋은 방법은 암호 스프레이 공격자에 의해 사용 되는 일반적인 전략을 제공 하는 전체 암호 강도 개선 하지 않습니다.

> [!NOTE]
> 전역 차단된 암호 목록을 아닙니다. Microsoft는 타사 데이터에 대해 어떠한 원본, 손상 된 암호 목록을 포함 하 여 기반 합니다.

실제 암호 스프레이 공격을 비롯 한 사실에 실제 보안 원격 분석에서 소싱 되는 팩트에서 해당 보안 효과 증폭 Microsoft 글로벌 금지 목록 일부 타사 대량 목록에 비해 작은 경우에는 Microsoft 암호 유효성 검사 알고리즘 스마트 유사 항목 일치 하는 기술을 사용합니다. 최종 결과 효율적으로 감지 하 고 수백만 개의 기업에서 사용 되는 가장 일반적인 약한 암호를 차단 됩니다. 조직과 관련 용어 목록에 추가 사용자 지정 금지 된 암호를 선택 하는 고객 동일한 알고리즘을 활용할 수도 있습니다.

## <a name="on-premises-hybrid-scenarios"></a>온-프레미스 하이브리드 시나리오

클라우드 전용 계정을 보호하면 도움이 되지만, 많은 조직에서는 온-프레미스 Windows Server Active Directory를 비롯한 하이브리드 시나리오를 유지하고 있습니다. Windows Server Active Directory 에이전트 온-프레미스와 기존 인프라에 금지 된 암호 목록을 확장에 대 한 Azure AD 암호 보호를 설치 하는 것이 가능 합니다. 이제 온-프레미스에서 암호를 변경, 설정 또는 다시 설정하는 사용자와 관리자는 클라우드 전용 사용자와 동일한 암호 정책을 준수해야 합니다.

## <a name="how-are-passwords-evaluated"></a>암호 평가 방법

사용자가 암호를 변경하거나 재설정할 때마다 새 암호를 전역 및 사용자 지정 금지된 암호 목록(구성되어 있는 경우)과 대조해 유효성을 검사하는 방식으로 암호의 보안 수준과 복잡성을 확인합니다.

사용자 암호에 금지된 암호가 포함되어 있더라도 전체 암호의 보안 수준이 충분히 높으면 해당 암호를 사용할 수 있습니다. 암호를 새로 구성하면 다음 단계가 진행되어 암호의 전반적인 보안 수준을 평가함으로써 암호를 수락할지 아니면 거부할지가 결정됩니다.

### <a name="step-1-normalization"></a>1단계: 표준화

먼저 새 암호에 대해 정규화 프로세스가 진행됩니다. 이 기술을 사용 하면 소수의 금지 된 암호 훨씬 더 큰 잠재적으로 취약 한 암호 집합을 매핑할 수 있습니다.

정규화는 두 부분으로 진행됩니다.  먼저 대문자가 모두 소문자로 변경됩니다.  그런 후에 다음과 같이 일반 문자 대체가 수행됩니다.  

| 원래 문자  | 대체되는 문자 |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | ' |
| '\@'  | ' a' |

예: "blank"라는 암호가 금지된 상태에서 사용자가 암호를 "Bl@nK"로 변경하려 한다고 가정해 보겠습니다. "Bl@nk"는 구체적으로 금지되어 있지는 않지만 정규화 프로세스에서는 이 암호를 금지된 암호인 "blank"로 변환합니다.

### <a name="step-2-check-if-password-is-considered-banned"></a>2단계: 암호가 금지된 항목으로 간주되는지 확인

#### <a name="fuzzy-matching-behavior"></a>유사 일치 동작

유사 일치는 정규화된 암호가 전역 또는 사용자 지정 금지된 암호 목록에 있는 암호를 포함하는지를 확인하는 데 사용됩니다. 일치 프로세스는 편집 거리 1 비교를 기준으로 합니다.  

예: "abcdef"라는 암호가 금지된 상태에서 사용자가 암호를 다음 중 하나로 변경하려 한다고 가정해 보겠습니다.

'abcdeg'    *(마지막 문자가 'f'에서 'g'로 변경됨)* 'abcdefg'   *(끝에 'g'가 추가됨)* 'abcde'     *(맨 끝의 'f'가 삭제됨)*

위의 각 암호는 금지된 암호인 "abcdef"와 구체적으로 일치하지는 않습니다. 그러나 각 예제는 금지 된 용어 'a b의 1을 편집 거리 내에 있는 이므로 모든 간주 됩니다 "abcdef"를 일치 항목으로.

#### <a name="substring-matching-on-specific-terms"></a>부분 문자열 일치(특정 용어)

부분 문자열 일치는 정규화된 암호에서 사용자 이름/성 및 테넌트 이름을 확인하는 데 사용됩니다. Active Directory 도메인 컨트롤러에서 암호 유효성을 검사할 때는 테넌트 이름 일치가 수행되지 않습니다.

예: Pol "P0l123fb"를 자신의 암호를 재설정 하려는 사용자 한다고 가정해 보겠습니다. 정규화 후이 암호는 "pol123fb"가 됩니다. 부분 문자열 일치 하는 암호에는 "Pol" 사용자의 이름을 포함 하는 작업을 찾습니다. "P0l123fb" 금지 된 암호 목록 중 하나에 대해 구체적으로 없습니다, 경우에 암호에 "Pol"를 찾을 부분 문자열 일치 합니다. 이 암호는 거부됩니다.

#### <a name="score-calculation"></a>점수 계산

다음 단계에서는 사용자의 정규화된 새 암호에서 금지된 암호의 모든 인스턴스를 확인합니다. 그렇다면

1. 사용자의 암호에서 확인되는 금지된 각 암호가 1점으로 계산됩니다.
2. 나머지 각 고유 문자도 1점으로 계산됩니다.
3. 암호에 최소 5 (5) 수에 대 한 지점이 있어야 합니다.

다음 두 예제에서는 Contoso가 Azure AD 암호 보호를 사용 중이며 사용자 지정 목록에 "contoso"가 포함되어 있다고 가정하겠습니다. 그리고 전역 목록에는 "blank"가 포함되어 있다고 가정합니다.

예제: 사용자가 암호 "C0ntos0Blank12"로 변경합니다.

정규화 후에 이 암호는 "contosoblank12"가 됩니다. 일치 프로세스에서는 이 암호에 금지된 암호 2개(contoso, blank)가 포함되어 있음이 확인됩니다. 따라서 이 암호의 점수는

[contoso] + [공백] [1] + [2] = 4 포인트 +이 암호의 다섯 가지 (5) 지점 이므로 거부 됩니다.

예제: 사용자가 암호 "ContoS0Bl@nkf9!"로 변경합니다.

정규화 후에 이 암호는 "contosoblankf9!"가 됩니다. 일치 프로세스에서는 이 암호에 금지된 암호 2개(contoso, blank)가 포함되어 있음이 확인됩니다. 따라서 이 암호의 점수는

[contoso] + [공백] + [f] [9] + + [!] = 5 포인트가이 암호에 최소 5 개 지점 이므로 허용 됩니다.

   > [!IMPORTANT]
   > 금지된 암호 알고리즘과 전역 목록은 지속적인 보안 분석 및 연구를 토대로 Azure에서 언제든지 변경할 수 있으며 실제로 변경됩니다. 온-프레미스 DC 에이전트 서비스에 대 한 업데이트 된 알고리즘만 적용 됩니다 DC 에이전트 소프트웨어를 다시 설치 합니다.

## <a name="license-requirements"></a>라이선스 요구 사항

|   | 전역 금지 암호 목록을 사용하여 Azure AD 암호 보호 | 사용자 지정 금지 암호 목록을 사용하여 Azure AD 암호 보호|
| --- | --- | --- |
| 클라우드 전용 사용자 | Azure AD Free | Azure AD Premium P1 또는 P2 |
| 온-프레미스 Windows Server Active Directory에서 동기화된 사용자 | Azure AD Premium P1 또는 P2 | Azure AD Premium P1 또는 P2 |

> [!NOTE]
> 또한 Azure Active Directory에 동기화 되지 않은 온-프레미스 Windows Server Active Directory 사용자 동기화 된 사용자에 대 한 기존 라이선스 기반 Azure AD 암호 보호의 이점을 이용 합니다.

비용을 비롯한 추가 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

사용자가 암호를 금지된 문자열로 재설정하려고 하면 다음과 같은 오류 메시지가 표시됩니다.

암호에 쉽게 추측할 수 있는 단어, 구 또는 패턴이 포함되어 있습니다. 다른 암호로 다시 시도하세요.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 금지 암호 목록 구성](howto-password-ban-bad.md)
- [온-프레미스에서 Azure AD 암호 보호 에이전트 사용](howto-password-ban-bad-on-premises-deploy.md)
