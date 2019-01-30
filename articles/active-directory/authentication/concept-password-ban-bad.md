---
title: Azure AD에서 동적으로 금지된 암호
description: Azure AD 동적으로 금지된 암호를 사용하여 환경에서 취약한 암호를 금지할 수 있습니다.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.openlocfilehash: 7cb1acace3dd8605d7506013a6f1c0273dafa32f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421439"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>조직에서 잘못된 암호 제거

|     |
| --- |
| Azure AD 암호 보호 및 사용자 지정 금지 암호 목록은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

업계의 주요 기업들은 여러 위치에서 동일한 암호를 사용하지 않아야 하고, Password123 같은 간단한 암호도 사용하지 말라고 조언합니다. 사용자가 규정을 준수하게 만들려면 조직에서 어떻게 해야 할까요? 사용자가 일반적인 암호 또는 최근에 데이터 보안 위반 사건에 연루된 것으로 알려진 암호를 사용하지 못하게 하려면 어떻게 해야 할까요?

## <a name="global-banned-password-list"></a>전역 금지 암호 목록

Microsoft는 항상 한 발 앞서 사이버 범죄에 대처하려고 합니다. Azure AD ID 보호 팀은 일반적으로 사용되는 보안 위험이 있는 암호를 지속적으로 살펴보고 있습니다. 그리고 소위 말하는 전역 금지 암호 목록에서 자주 보이는 암호를 차단합니다. 사이버 범죄자 역시 비슷한 전략을 공격에 사용하므로 Microsoft는 이 목록의 콘텐츠를 공개적으로 게시하지 않습니다. 이와 같이 취약한 암호는 Microsoft 고객에게 실질적인 위협으로 다가오기 전에 차단됩니다. 현재 보안 노력에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/intelligence-report)를 참조하세요.

## <a name="preview-custom-banned-password-list"></a>미리 보기: 사용자 지정 금지 암호 목록

일부 조직은 Microsoft에서 사용자 금지 암호 목록이라고 부르는 전역 금지 암호 목록 위에 자체적인 사용자 지정을 추가하여 보안을 강화하려 합니다. Contoso 같은 대기업 고객은 브랜드 이름, 회사 관련 용어 또는 기타 항목의 변형을 차단하도록 선택할 수 있습니다.

사용자 지정 금지 암호 목록 및 온-프레미스 Active Directory 통합을 사용하는 기능은 Azure Portal을 통해 관리됩니다.

![Azure Portal의 인증 방법에서 사용자 지정 금지 암호 목록을 수정할 수 있습니다.](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>온-프레미스 하이브리드 시나리오

클라우드 전용 계정을 보호하면 도움이 되지만, 많은 조직에서는 온-프레미스 Windows Server Active Directory를 비롯한 하이브리드 시나리오를 유지하고 있습니다. 온-프레미스에 Windows Server Active Directory(미리 보기) 에이전트용 Azure AD 암호 보호를 설치하여 금지 암호 목록을 기존 인프라로 확장할 수 있습니다. 이제 온-프레미스에서 암호를 변경, 설정 또는 다시 설정하는 사용자와 관리자는 클라우드 전용 사용자와 동일한 암호 정책을 준수해야 합니다.

## <a name="how-does-the-banned-password-list-work"></a>금지 암호 목록의 작동 원리

금지 암호 목록은 문자열을 소문자로 변환한 후 편집 거리가 1 이내이고 유사 항목이 있는 알려진 금지 암호와 비교하여 목록의 암호를 일치시킵니다.

예제: 한 조직에서 password라는 단어를 차단한다고 가정합시다.
   - 사용자가 자신의 암호를 "password"의 변형인 "P@ssword"로 설정하려고 이 암호가 차단됩니다.
   - 관리자가 사용자 암호를 "Password123!"으로 설정하려고 시도할 경우 이 암호는 "password123!"으로 변환되고 password의 변형이므로 차단됩니다.

사용자가 Azure AD 암호를 다시 설정하거나 변경하려고 시도할 때마다 이 프로세스를 통해 금지된 암호 목록에 없는 것인지 검사하는 작업이 진행됩니다. 이 검사는 셀프 서비스 암호 재설정, 암호 해시 동기화 및 통과 인증을 사용하는 하이브리드 시나리오에 포함됩니다.

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
