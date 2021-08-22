---
title: B2B 협업 사용자에 대한 조건부 액세스 - Azure AD
description: Azure Active Directory B2B 사용자에게 다단계 인증 정책을 적용하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40be574b048575a1cf056fcdcd97bc09d4f21098
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729617"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 협업 사용자에 대한 조건부 액세스

이 문서에서는 조직이 B2B 게스트 사용자가 리소스에 액세스하기 할 수 있도록 CA(조건부 액세스) 정책의 범위를 확장하는 방법을 설명합니다.
>[!NOTE]
>이 인증 또는 권한 부여 흐름은 IdP(ID 공급자)의 기존 사용자가 아닌 게스트 사용자에게는 약간 다릅니다.

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>외부 디렉터리에서 B2B 게스트 사용자에 대한 인증 흐름

다음 다이어그램은 흐름을 보여 줍니다. ![외부 디렉터리에서 B2B 게스트 사용자에 대한 인증 흐름을 보여 주는 이미지](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| 단계 | 설명 |
|--------------|-----------------------|
| 1. | B2B 게스트 사용자가 리소스에 대한 액세스를 요청합니다. 리소스는 사용자를 해당 리소스 테넌트(신뢰할 수 있는 IdP)로 리디렉션합니다.|
| 2. | 리소스 테넌트가 사용자를 외부로 식별하고 사용자를 B2B 게스트 사용자의 IdP으로 리디렉션합니다. 사용자는 IdP에서 기본 인증을 수행합니다.
| 3. | B2B 게스트 사용자의 IdP가 사용자에게 토큰을 발급합니다. 사용자는 토큰을 사용하여 리소스 테넌트로 다시 리디렉션됩니다. 리소스 테넌트는 토큰의 유효성을 검사한 다음 해당 CA 정책에 대해 사용자를 평가합니다. 예를 들어 리소스 테넌트가 사용자에게 Azure Active Directory(AD) Multi-Factor Authentication을 수행하도록 요구할 수 있습니다.
| 4. | 모든 리소스 테넌트 CA 정책이 충족되면 리소스 테넌트가 자체 토큰을 발급하고 사용자를 리소스로 리디렉션합니다.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>일회용 암호를 사용하는 B2B 게스트 사용자에 대한 인증 흐름

다음 다이어그램은 흐름을 보여 줍니다. ![일회용 암호를 사용하는 B2B 게스트 사용자에 대한 인증 흐름을 보여 주는 이미지.](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| 단계 | 설명 |
|--------------|-----------------------|
| 1. |사용자가 다른 테넌트의 리소스에 대한 액세스를 요청합니다. 리소스는 사용자를 해당 리소스 테넌트(신뢰할 수 있는 IdP)로 리디렉션합니다.|
| 2. | 리소스 테넌트는 사용자를 [외부 메일 OTP(일회용 암호) 사용자](./one-time-passcode.md)로 식별하고 OTP를 사용하여 사용자에게 메일을 보냅니다.|
| 3. | 사용자가 OTP를 검색하고 코드를 전송합니다. 리소스 테넌트는 CA 정책에 대해 사용자를 평가합니다.
| 4. | 모든 CA 정책이 충족되면 리소스 테넌트가 토큰을 발급하고 사용자를 리소스로 리디렉션합니다. |

>[!NOTE]
>사용자가 외부 리소스 테넌트를 사용하는 경우 B2B 게스트 사용자의 IdP CA 정책도 평가할 수 없습니다. 현재는 리소스 테넌트의 CA 정책만 게스트에 적용됩니다.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B 사용자를 위한 Azure AD Multi-Factor Authentication

조직은 B2B 게스트 사용자에게 여러 Azure AD Multi-Factor Authentication 정책을 적용할 수 있습니다. 조직의 상근 직원과 구성원에게 해당 정책을 사용하는 것과 같은 방법으로 정책을 테넌트, 앱 또는 개별 사용자 수준에서 적용할 수 있습니다.
게스트 사용자의 조직에 다단계 인증 기능이 있더라도 리소스 테넌트는 항상 사용자에 대한 Azure AD Multi-Factor Authentication을 할 책임이 있습니다. 예를 들면 다음과 같습니다.

1. Fabrikam이라는 회사의 관리자 또는 정보 근로자는 Contoso라는 다른 회사의 사용자를 초대하여 해당 애플리케이션 Woodgrove를 사용합니다.

2. Fabrikam의 Woodgrove 앱은 액세스 시 Azure AD Multi-Factor Authentication가 필요하도록 구성됩니다.

3. Contoso의 B2B 게스트 사용자가 Fabrikam 테넌트의 Woodgrove에 액세스하려고 하면 Azure AD Multi-Factor Authentication 챌린지를 완료하라는 메시지가 표시됩니다.

4. 그러면 게스트 사용자는 Fabrikam을 사용하여 Azure AD Multi-Factor Authentication를 설정하고 옵션을 선택할 수 있습니다.

5. 이 시나리오는 Azure AD 또는 개인 Microsoft 계정(MSA)의 모든 ID에 적용됩니다. 예를 들어 Contoso의 사용자가 소셜 ID를 사용하여 인증하는 경우입니다.

6. Fabrikam은 Azure AD Multi-Factor Authentication을 충분히 지원하는 프리미엄 Azure AD 라이선스를 보유해야 합니다. Contoso의 사용자는 Fabrikam의 이 라이선스를 사용합니다. B2B 라이선스에 관한 자세한 내용은 [Azure AD External Identities에 대한 청구 모델](./external-identities-pricing.md)을 참조하세요.

>[!NOTE]
>Azure AD Multi-Factor Authentication은 예측 가능성을 보장하기 위해 리소스 테넌트에서 수행됩니다. 게스트 사용자가 로그인하면 백그라운드에 표시되는 리소스 테넌트 로그인 페이지와 포그라운드에 표시되는 자신의 홈 테넌트 로그인 페이지 및 회사 로고를 볼 수 있습니다.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B 사용자에 대한 Azure AD Multi-Factor Authentication 설정

B2B 협업 사용자에 대한 Azure AD Multi-Factor Authentication을 설정하려면 다음 비디오를 시청하세요.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>제품 상환에 대한 B2B 사용자 Azure AD Multi-Factor Authentication

Azure AD Multi-Factor Authentication 상환 환경에 대한 자세한 정보를 얻으려면 다음 비디오를 시청하세요.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>B2B 사용자에 대한 Azure AD Multi-Factor Authentication 재설정

이제 B2B 게스트 사용자를 증명하는 데 사용할 수 있는 PowerShell cmdlet은 다음과 같습니다.

1. Azure에 연결

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. 증명 방법이 있는 모든 사용자 가져오기

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   예를 들면 다음과 같습니다.

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. B2B 협업 사용자가 증명 방법을 다시 설정하도록 하려면 특정 사용자에 대한 Azure AD Multi-Factor Authentication 방법을 재설정합니다. 
   예를 들면 다음과 같습니다.

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>B2B 사용자에 대한 조건부 액세스

B2B 게스트 사용자에 대한 CA 정책에 영향을 주는 요인은 다양합니다.

### <a name="device-based-conditional-access"></a>디바이스 기반 조건부 액세스

CA에는 사용자의 [디바이스가 규격 또는 하이브리드 Azure AD에 조인](../conditional-access/concept-conditional-access-conditions.md#device-state-preview)되도록 요구하는 옵션이 있습니다. B2B 게스트 사용자는 리소스 테넌트가 자신의 디바이스를 관리할 수 있는 경우에만 규정 준수를 충족할 수 있습니다. 한 번에 둘 이상의 조직에서 디바이스를 관리할 수 없습니다. B2B 게스트 사용자는 온-프레미스 AD 계정이 없으므로 하이브리드 Azure AD 조인을 충족할 수 없습니다. 

>[!Note]
>외부 사용자에게 관리 디바이스를 요구하지 않는 것이 좋습니다.

### <a name="mobile-application-management-policies"></a>모바일 애플리케이션 관리 정책

**승인된 클라이언트 앱 요구** 및 **앱 보호 정책 요구** 와 같은 CA 권한 부여 컨트롤은 디바이스를 테넌트에 등록해야 합니다. 컨트롤은 [iOS 및 Android 디바이스](../conditional-access/concept-conditional-access-conditions.md#device-platforms)에만 적용할 수 있습니다. 그러나 사용자의 디바이스가 이미 다른 조직에 의해 관리되고 있는 경우 컨트롤을 B2B 게스트 사용자에게 적용할 수 없습니다. 모바일 디바이스는 한 번에 둘 이상의 테넌트에 등록할 수 없습니다. 모바일 디바이스가 다른 조직에서 관리되는 경우 사용자는 차단됩니다. 

>[!NOTE]
>외부 사용자에게 앱 보호 정책을 요구하지 않는 것이 좋습니다.

### <a name="location-based-conditional-access"></a>위치 기반 조건부 액세스

초대하는 조직이 신뢰할 수 있는 IP 주소 범위를 만들어 파트너 조직을 정의할 수 있는 경우 IP 범위를 기반으로 하는 [위치 기반 정책](../conditional-access/concept-conditional-access-conditions.md#locations)을 적용할 수 있습니다.

**지리적 위치** 에 따라 정책을 적용할 수도 있습니다.

### <a name="risk-based-conditional-access"></a>위험 기반 조건부 액세스

B2B 게스트 사용자가 권한 부여 컨트롤을 충족하는 경우 [로그인 위험 정책](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)이 적용됩니다. 예를 들어, 조직에서 중간 또는 높은 로그인 위험에 대해 Azure AD Multi-Factor Authentication가 필요할 수 있습니다. 그러나 이전에 리소스 테넌트의 Azure AD Multi-Factor Authentication에 등록하지 않은 사용자는 차단됩니다. 이 작업은 악의적인 사용자가 합법적인 사용자의 암호를 손상하는 경우 자신의 Azure AD Multi-Factor Authentication 자격 증명을 등록하지 못하도록 하기 위한 것입니다.

그러나 [사용자 위험 정책](../conditional-access/concept-conditional-access-conditions.md#user-risk)은 리소스 테넌트에서 확인할 수 없습니다. 예를 들어 높은 위험 수준인 게스트 사용자 때문에 암호를 변경해야 한다면, 리소스 디렉터리에서 암호를 다시 설정할 수 없기 때문에 그 게스트 사용자는 차단됩니다.

### <a name="conditional-access-client-apps-condition"></a>조건부 액세스 클라이언트 앱 조건

[클라이언트 앱 조건](../conditional-access/concept-conditional-access-conditions.md#client-apps)은 다른 유형의 사용자와 마찬가지로 B2B 게스트 사용자에 대해 동일하게 동작합니다. 예를 들어 게스트 사용자가 레거시 인증 프로토콜을 사용하지 못하도록 할 수 있습니다.

### <a name="conditional-access-session-controls"></a>조건부 액세스 세션 컨트롤

[세션 컨트롤](../conditional-access/concept-conditional-access-session.md)은 다른 유형의 사용자와 마찬가지로 B2B 게스트 사용자에 대해 동일하게 동작합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 Azure AD B2B 협업에 대한 다음 문서를 참조하세요.

- [Azure AD B2B 협업이란?](./what-is-b2b.md)
- [ID 보호 및 B2B 사용자](../identity-protection/concept-identity-protection-b2b.md)
- [외부 ID 가격](https://azure.microsoft.com/pricing/details/active-directory/external-identities/)
- [FAQ(질문과 대답)](./faq.yml)
