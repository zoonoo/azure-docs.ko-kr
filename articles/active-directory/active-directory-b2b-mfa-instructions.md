---
title: "Azure Active Directory B2B 공동 작업 사용자에 대한 조건부 액세스 | Microsoft Docs"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: fac4657e9b78e900c052384e212aa9f3915f970d
ms.lasthandoff: 04/13/2017


---

# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대한 조건부 액세스

## <a name="multi-factor-authentication-for-b2b-users"></a>B2B 사용자에 대한 다단계 인증
Azure AD B2B 공동 작업을 통해 조직에서는 B2B 사용자에 대한 다단계 인증 정책을 고유하게 적용할 수 있습니다. 조직의 전일제 직원과 구성원에 대해 이러한 정책을 사용하는 것과 같은 방법으로 이러한 정책을 테넌트 수준, 앱 수준 또는 개별 사용자 수준에서 적용할 수 있습니다. MFA 정책은 리소스 조직에서 적용됩니다.

이것은 다음을 의미합니다.
1. 회사 A의 관리자 또는 정보 근로자가 회사 B의 사용자를 회사 A의 Foo 응용 프로그램에 초대합니다.
2. 회사 A의 응용 프로그램 *Foo*는 액세스 시 MFA를 요구하도록 구성됩니다.
3. 회사 B의 사용자가 회사 A 테넌트에서 Foo 앱에 액세스하려고 하면 회사 A의 MFA 정책이 요구하는 MFA 챌린지를 완료하도록 요구됩니다.
4. 사용자는 회사 A와 MFA를 설정할 수 있고 해당 MFA 옵션을 선택합니다.
5. 이러한 방식은 어떤 ID에도 잘 맞습니다(Azure AD 또는 회사 B의 사용자가 소셜 ID를 사용하여 인증을 받는 경우 MSA).
6. 회사 A는 MFA를 지원하는 적절한 프리미엄 Azure AD SKU가 있어야 합니다. 회사 B의 사용자는 회사 A의 이 라이선스를 사용합니다.

요약하자면 파트너 조직 자체(MFA 기능이 있어도 관계 없음)가 아닌 초대하는 테넌시가 *항상* B2B 공동 작업 사용자의 MFA를 책임집니다.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대한 MFA 설정
B2B 공동 작업 사용자에 대한 MFA를 설정하는 작업이 얼마나 간단한지 알아보려면 다음 비디오를 시청하세요.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B 사용자는 제안 상환을 위해 MFA 환경을 사용합니다.
다음 비디오에 표시된 것처럼 아래 애니메이션을 통해 상환 환경을 확인할 수 있습니다.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대해 다시 설정된 MFA
현재 관리자는 다음 PowerShell cmdlet을 사용하여 B2B 공동 작업 사용자에게 다시 입증하도록 요구할 수 있습니다. 따라서 B2B 공동 작업 사용자의 증명 방법을 다시 설정하려면 다음 PowerShell cmdlet을 사용해야 합니다.

1. Azure에 연결

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. 증명 방법이 있는 모든 사용자 가져오기

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  다음은 예제입니다.

  ```
  PS C:\Users\tjwasserGet-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. 특정 사용자에 대한 MFA 방법을 다시 설정합니다. 그런 후 해당 UserPrincipalName으로 다시 설정 명령을 실행하여 B2B 공동 작업 사용자에게 증명 방법을 다시 설정하도록 요구할 수 있습니다. 예제:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>리소스 테넌트에서 MFA를 수행하는 이유는 무엇인가요?

현재 릴리스에서 MFA는 항상 리소스 테넌트에 있습니다. 그 이유는 예측 가능성 때문입니다.

예를 들어 Contoso 사용자(Sally)가 Fabrikam에 초대되고 Fabrikam이 B2B 사용자에 대한 MFA를 사용하도록 설정했다고 가정해 보겠습니다.

이제 Contoso에서 MFA 정책이 App1에 사용되지만 App2에는 사용되지 않는 경우 Sally가 Fabrikam에서 MFA되어야 하는지 여부를 결정하기 위해 토큰의 Contoso MFA 클레임을 확인하면 다음 문제가 발생할 수 있습니다.

* 1일차: Sally는 App1에 액세스하지만 Fabrikam에서 MFA 프롬프트가 표시되지 않으므로 Contoso에서 MFA되었습니다.

* 2일차: Sally는 Contoso에서 App 2에 액세스했으므로 이제 Fabrikam에 액세스할 때 Fabrikam에서 MFA에 등록해야 합니다.

이 상황은 Sally에게 매우 혼란스러우므로 로그인 완료가 어려울 수 있습니다.

도한 Contoso에 MFA 기능이 있는 경우에도 Fabrikam에서 항상 Contoso MFA 정책을 신뢰하는 것은 아닙니다.

마지막으로 리소스 테넌트 MFA는 MSA 및 소셜 ID에도 적용되고 MFA가 설정되지 않은 파트너 조직에도 적용됩니다.

따라서 B2B 사용자에 대한 MFA 권장 사항에는 항상 리소스 MFA가 필요합니다. 이렇게 하면 MFA가 이중 적용되는 경우가 있지만 리소스 테넌트에 액세스할 때마다 최종 사용자 경험을 예측할 수 있습니다. Sally는 리소스 테넌트를 사용하여 MFA에 등록해야 합니다.

### <a name="device-location-and-risk-based-conditional-access-for-b2b-users"></a>B2B 사용자에 대한 장치, 위치 및 위험 기반 조건부 액세스

Contoso 조직이 회사 데이터에 대한 장치 기반 조건부 액세스 정책을 적용할 수 있으면 관리되지 않는 장치(Contoso 조직에서 관리되지 않고 Contoso 장치 정책을 준수하지 않는 장치)에서는 액세스할 수 없습니다.

B2B 사용자의 장치가 Contoso에서 관리되지 않는 경우 이는 이러한 정책이 적용된 컨텍스트가 무엇이든 파트너 조직 B2B 사용자의 액세스가 차단될 것임을 의미합니다.

다른 조직의 사용자가 초대하는 조직에서 관리되는 장치를 보유할 것이라는 예상은 큰 장애물입니다. 따라서 이후 업데이트에서는 Contoso가 특정 파트너의 장치 준수 상태를 신뢰하도록 할 예정입니다. 이렇게 하면 Contoso는 Fabrikam의 사용자가 Fabrikam에서 관리되는 장치를 사용 중인 경우 Contoso 리소스에 액세스할 수 있는 정책을 적용할 수 있습니다.

그 사이에 Contoso는 장치 기반 조건부 액세스 정책에서 특정 파트너 사용자가 포함된 제외 목록을 만들 수 있습니다.

#### <a name="location-based-conditional-access-for-b2b"></a>B2B에 대한 위치 기반 조건부 액세스

초대하는 조직(예: Contoso)이 파트너 조직(예: Fabrikam)을 정의하는 신뢰할 수 있는 네트워크 경계(예: IP 주소 범위)를 만들 수 있는 경우 위치 기반 조건부 액세스 정책을 B2B 사용자에게 적용할 수 있습니다.

#### <a name="risk-based-conditional-access-for-b2b"></a>B2B에 대한 위험 기반 조건부 액세스

위험 평가는 B2B 사용자의 홈 조직(B2B 사용자의 ID 테넌트)에서 수행되기 때문에 현재는 로그인 위험 기반 정책을 B2B 사용자에게 적용할 수 없습니다.

이후 업데이트에서는 Contoso가 알고 있는 위험뿐 아니라 다른 곳에서 발생할 수 있기 때문에 알 방법이 없는 위험으로부터 외부적으로 공유되는 앱 및 데이터를 보호할 수 있도록 파트너의 위험 점수를 페더레이션하는 것을 고려하고 있습니다(파트너의 동의에 따라).

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

