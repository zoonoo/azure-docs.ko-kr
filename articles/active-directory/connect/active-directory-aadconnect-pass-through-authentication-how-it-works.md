---
title: "Azure AD Connect: 통과 인증 - 작동 방식? | Microsoft Docs"
description: "이 문서에서는 Azure Active Directory 통과 인증 작동 방식을 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 9ded5e0199f5ca48e2a00d2afee0e4c13b3a3460
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory 통과 인증: 기술 심층 분석
다음 문서는 Azure AD 통과 인증의 작동 방법에 대한 개요입니다.  자세한 기술 및 보안 정보는 [**보안 심층 분석**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) 문서를 참조하세요.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory 통과 인증 작동 방식은?

사용자가 Azure AD(Azure Active Directory)로 보호되는 응용 프로그램에 로그인하려고 하고 통과 인증이 테넌트에서 사용하도록 설정되어 있으면 다음 단계가 수행됩니다.

1. 사용자가 응용 프로그램에 액세스하려고 합니다(예: Outlook 웹앱 - https://outlook.office365.com/owa/).
2. 사용자가 아직 로그인하지 않은 경우 해당 사용자는 Azure AD 로그인 페이지로 리디렉션됩니다.
3. 사용자가 사용자 이름과 암호를 Azure AD 로그인 페이지에 입력하고 "로그인" 단추를 클릭합니다.
4. 로그인 요청을 수신하는 Azure AD에서 공개 키로 암호화된 사용자 이름 및 암호를 큐에 넣습니다.
5. 온-프레미스 통과 인증 에이전트는 큐에 아웃바운드 호출을 실행하고 사용자 이름 및 암호화된 암호를 검색합니다.
6. 에이전트는 해당 개인 키를 사용하여 암호를 해독합니다.
7. 그런 다음 에이전트가 표준 Windows API(Active Directory Federation Services에서 사용되는 것과 비슷한 메커니즘)를 사용하여 사용자 이름과 암호를 Active Directory에 대해 유효성을 검사합니다. 사용자 이름은 온-프레미스 기본 사용자 이름(일반적으로 `userPrincipalName`) 또는 Azure AD Connect에 구성된 또 다른 특성(`Alternate ID`라고 함) 중 하나일 수 있습니다.
8. 그런 다음 온-프레미스 Active Directory DC(도메인 컨트롤러)는 요청을 평가하고 적절한 응답(성공, 실패, 암호 만료됨 또는 사용자 차단됨)을 에이전트에 반환합니다.
9. 그러면 에이전트가 이 응답을 다시 Azure AD로 반환합니다.
10. Azure AD는 응답을 평가하고 사용자에게 적절히 응답합니다(예: 사용자가 즉시 로그인하도록 하거나 MFA(Multi-Factor Authentication)에 대해 요청).
11. 사용자 로그인에 성공하면 해당 사용자는 해당 응용 프로그램에 액세스할 수 있습니다.

다음 다이어그램은 관련된 모든 구성 요소와 단계를 보여 줍니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>다음 단계
- [**현재 제한 사항** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [**빠른 시작**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD 통과 인증을 작동하고 실행합니다.
- [**스마트 잠금**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - 테넌트에서 사용자 계정을 보호하도록 스마트 잠금 기능을 구성합니다.
- [**FAQ(질문과 대답)**](active-directory-aadconnect-pass-through-authentication-faq.md) - 질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**보안 심층 분석**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 해당 기능에 대한 자세한 추가 기술 정보입니다.
- [**Azure AD 원활한 SSO**](active-directory-aadconnect-sso.md) - 이 보완 기능에 대해 자세히 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.
