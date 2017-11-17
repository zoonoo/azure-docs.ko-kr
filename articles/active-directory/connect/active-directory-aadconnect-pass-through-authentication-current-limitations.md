---
title: "Azure AD Connect: 통과 인증 - 현재 제한 사항 | Microsoft Docs"
description: "이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 문제의 현재 제한 사항에 대해 설명합니다."
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
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 4a33df43ca218545d6c684103a64f2cd1460913b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 통과 인증: 현재 제한 사항

>[!IMPORTANT]
>Azure AD 통과 인증은 무료 기능이며, 이 기능을 사용하기 위해 Azure AD 유료 버전이 필요하지 않습니다. 통과 인증은 Azure AD의 전 세계 인스턴스에서만 사용할 수 있고, [Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) 및 [Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/)에서는 사용할 수 없습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 시나리오는 완전히 지원됩니다.

- 사용자가 모든 웹 브라우저 기반 응용 프로그램에 로그인합니다.
- 사용자가 [최신 인증](https://aka.ms/modernauthga)을 지원하는 Office 365 클라이언트 응용 프로그램(최신 인증을 _사용하여_ Office 2016 및 Office 2013)에 로그인합니다.
- Windows 10 장치에 대한 Azure AD 조인.
- Exchange ActiveSync 지원.

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 지원되지 _않습니다_.

- 사용자가 최신 인증 _없이_ 레거시 Office 클라이언트 응용 프로그램(Office 2010 및 Office 2013)에 로그인합니다. 가능할 경우 조직은 최신 인증으로 전환하는 것이 좋습니다. 최신 인증을 사용하면 통과 인증 지원이 허용될 뿐 아니라 MFA(Multi-Factor Authentication) 등의 [조건부 액세스](../active-directory-conditional-access-azure-portal.md) 기능을 사용하여 사용자 계정을 보호할 수도 있습니다.
- 사용자가 비즈니스용 Skype 2016을 포함한 비즈니스용 Skype 클라이언트 응용 프로그램에 로그인합니다.
- 사용자가 PowerShell v1.0에 로그인합니다. PowerShell v2.0을 대신 사용하는 것이 좋습니다.
- Azure AD Domain Services
- MFA에 대한 앱 암호
- [자격 증명이 손실된](../active-directory-reporting-risk-events.md#leaked-credentials) 사용자 검색

>[!IMPORTANT]
>지원되지 않는 시나리오에_만_ 해당하는 해결 방법으로, Azure AD Connect 마법사의 [선택적 기능](active-directory-aadconnect-get-started-custom.md#optional-features) 페이지에서 암호 해시 동기화를 사용하도록 설정합니다. 암호 해시 동기화를 사용하면 온-프레미스 인프라가 완전히 손상된 경우 인증을 장애 조치(failover)할 수 있는 옵션도 제공됩니다. 통과 인증에서 암호 해시 동기화로의 이 장애 조치(failover)는 자동이 아니지만 Microsoft 지원의 도움을 받아 수행해야 합니다.

## <a name="next-steps"></a>다음 단계
- [**빠른 시작**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD 통과 인증을 작동하고 실행합니다.
- [**스마트 잠금**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - 테넌트에서 사용자 계정을 보호하도록 스마트 잠금 기능을 구성합니다.
- [**기술 심층 분석**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [**FAQ(질문과 대답)**](active-directory-aadconnect-pass-through-authentication-faq.md) - 질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**보안 심층 분석**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 해당 기능에 대한 자세한 추가 기술 정보입니다.
- [**Azure AD 원활한 SSO**](active-directory-aadconnect-sso.md) - 이 보완 기능에 대해 자세히 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.
