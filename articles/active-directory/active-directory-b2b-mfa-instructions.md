---
title: "Azure Active Directory B2B 공동 작업 사용자에 대한 Multi-Factor Authentication | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업 기능은 회사 응용 프로그램에 대한 선택적 액세스를 위해 MFA(Multi-Factor Authentication)를 지원합니다."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 894f94fdefe081679b1e35183bd4127be35cd33c


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Azure Active Directory B2B 공동 작업 사용자에 대한 Multi-Factor Authentication

이 Azure AD B2B 공동 작업 공개 미리 보기 수정 버전에서 Microsoft는 조직이 B2B 공동 작업 사용자에게도 MFA(Multi-Factor Authentication)를 적용할 수 있도록 하는 기능을 추가했습니다. 이 수정 버전에서 MFA는 항상 리소스 테넌시에서 적용됩니다.

이것은 다음을 의미합니다.
1. 회사 A의 관리자 또는 정보 근로자가 회사 B의 사용자를 회사 A의 Foo 응용 프로그램에 초대합니다.
2. 회사 A의 응용 프로그램 *Foo*는 액세스 시 MFA를 요구하도록 구성됩니다.
3. 회사 B의 사용자가 회사 A 테넌트에서 Foo 앱에 액세스하려고 하면 회사 A의 MFA 정책이 요구하는 MFA 챌린지를 완료하도록 요구됩니다.
4. 사용자는 회사 A와 MFA를 설정할 수 있고 해당 MFA 옵션을 선택합니다.
5. 이러한 방식은 어떤 ID에도 잘 맞습니다(Azure AD 또는 회사 B의 사용자가 소셜 ID를 사용하여 인증을 받는 경우 MSA).
6. 회사 A는 MFA를 지원하는 적절한 프리미엄 Azure AD SKU가 있어야 합니다. 회사 B의 사용자는 회사 A의 이 라이선스를 사용합니다.
7. 요약하자면 파트너 조직 자체(MFA 기능이 있어도 관계 없음)가 아닌 초대하는 테넌시가 *항상* B2B 공동 작업 사용자의 MFA를 책임집니다. 이후 릴리스에서는 초대하는 조직이 초대하는 조직의 MFA를 사용하는 대신 특정 파트너 조직의 MFA를 신뢰할 수 있도록 할 예정입니다.

## <a name="setting-up-mfa-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대한 MFA 설정
B2B 공동 작업 사용자에 대한 MFA를 설정하는 작업이 얼마나 간단한지 알아보려면 [이 비디오](https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup)를 시청하세요.

## <a name="b2b-collaboration-users-mfa-experience-for-offer-redemption"></a>제안 상환을 위한 B2B 공동 작업 사용자 MFA 환경
[이 비디오](https://channel9.msdn.com/Blogs/Azure/MFA-redemption)에 표시된 것처럼 아래 애니메이션을 통해 상환 환경을 확인할 수 있습니다.

## <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대해 다시 설정된 MFA
현재 관리자는 다음 PowerShell cmdlet을 사용하여 B2B 공동 작업 사용자에게 다시 입증하도록 요구할 수 있습니다. 따라서 B2B 공동 작업 사용자의 증명 방법을 다시 설정하려면 다음 PowerShell cmdlet을 사용해야 합니다.

> [!NOTE]
> 새 cmdlet을 사용하려면 https://www.powershellgallery.com/packages/AzureADPreview에서 다운로드할 수 있는 Azure AD PowerShell V2 모듈을 설치해야 합니다.

1. Azure에 연결

  ```
  Connect-MsolService and login
  ```
2. 증명 방법이 있는 모든 사용자 가져오기

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  다음은 예제입니다.

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 특정 사용자에 대한 MFA 방법을 다시 설정합니다. 그런 후 해당 UserPrincipalName으로 다시 설정 명령을 실행하여 B2B 공동 작업 사용자에게 증명 방법을 다시 설정하도록 요구할 수 있습니다. 예제:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-admin-add-users.md)
* [정보 근로자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-iw-add-users.md)
* [B2B 공동 작업 초대 전자 메일의 요소](active-directory-b2b-invitation-email.md)
* [B2B 공동 작업 초대 상환](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 공동 작업 라이선스](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 공동 작업 문제 해결](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 공동 작업 API 및 사용자 지정](active-directory-b2b-api.md)
* [초대 없이 B2B 공동 작업 사용자 추가](active-directory-b2b-add-user-without-invite.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


