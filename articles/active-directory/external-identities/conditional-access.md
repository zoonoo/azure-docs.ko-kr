---
title: B2B 공동 작업 사용자에 대 한 조건부 액세스-Azure AD
description: Azure Active Directory B2B 사용자에 게 multi-factor authentication 정책을 적용 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646285"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대 한 조건부 액세스

이 문서에서는 조직이 B2B 게스트 사용자가 리소스에 액세스 하기 위해 조건부 액세스 (CA) 정책의 범위를 지정할 수 있는 방법을 설명 합니다.
>[!NOTE]
>이 인증 또는 권한 부여 흐름은 IdP (Id 공급자)의 기존 사용자가 아닌 게스트 사용자에 게 약간 차이가 있습니다.

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>외부 디렉터리에서 B2B 게스트 사용자에 대 한 인증 흐름

다음 다이어그램은 흐름을 보여 줍니다. ![ 이미지는 외부 디렉터리에서 B2B 게스트 사용자에 대 한 인증 흐름을 보여 줍니다.](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| 단계 | 설명 |
|--------------|-----------------------|
| 1. | B2B 게스트 사용자가 리소스에 대 한 액세스를 요청 합니다. 리소스는 사용자를 해당 리소스 테 넌 트 (신뢰할 수 있는 IdP)로 리디렉션합니다.|
| 2. | 리소스 테 넌 트는 사용자를 외부로 식별 하 고 사용자를 B2B 게스트 사용자의 IdP으로 리디렉션합니다. 사용자가 IdP에서 기본 인증을 수행 합니다.
| 3. | B2B 게스트 사용자의 IdP는 사용자에 게 토큰을 발급 합니다. 사용자는 토큰을 사용 하 여 리소스 테 넌 트로 다시 리디렉션됩니다. 리소스 테 넌 트는 토큰의 유효성을 검사 한 다음 해당 CA 정책에 대해 사용자를 평가 합니다. 예를 들어 리소스 테 넌 트에서 사용자가 AD (Azure Active Directory) Multi-Factor Authentication를 수행 하도록 요구할 수 있습니다.
| 4. | 모든 리소스 테 넌 트 CA 정책이 충족 되 면 리소스 테 넌 트가 자체 토큰을 발급 하 고 사용자를 리소스로 리디렉션합니다.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>한 번의 암호를 사용 하는 B2B 게스트 사용자에 대 한 인증 흐름

다음 다이어그램은 흐름을 보여 줍니다. ![ 이미지는 한 시간 암호를 사용 하 여 B2B 게스트 사용자에 대 한 인증 흐름을 보여 줍니다.](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| 단계 | 설명 |
|--------------|-----------------------|
| 1. |사용자가 다른 테 넌 트의 리소스에 대 한 액세스를 요청 합니다. 리소스는 사용자를 해당 리소스 테 넌 트 (신뢰할 수 있는 IdP)로 리디렉션합니다.|
| 2. | 리소스 테 넌 트는 사용자를 [외부 전자 메일 otp (일회용 암호) 사용자](./one-time-passcode.md) 로 식별 하 고 otp를 사용 하 여 사용자에 게 전자 메일을 보냅니다.|
| 3. | 사용자가 OTP를 검색 하 고 코드를 전송 합니다. 리소스 테 넌 트는 CA 정책에 대해 사용자를 평가 합니다.
| 4. | 모든 CA 정책이 충족 되 면 리소스 테 넌 트가 토큰을 발급 하 고 사용자를 리소스로 리디렉션합니다. |

>[!NOTE]
>사용자가 외부 리소스 테 넌 트를 사용 하는 경우 B2B 게스트 사용자의 IdP CA 정책도 평가할 수 없습니다. 현재는 리소스 테 넌 트의 CA 정책만 게스트에 적용 됩니다.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B 사용자를 위한 Azure AD Multi-Factor Authentication

조직은 B2B 게스트 사용자에 대해 여러 Azure AD Multi-Factor Authentication 정책을 적용할 수 있습니다. 이러한 정책은 조직의 전일 직원 및 구성원에 대해 사용 하도록 설정 된 것과 동일한 방식으로 테 넌 트, 앱 또는 개별 사용자 수준에서 적용 될 수 있습니다.
리소스 테 넌 트는 게스트 사용자의 조직에 Multi-Factor Authentication 기능이 있더라도 항상 사용자를 위한 Azure AD Multi-Factor Authentication 책임이 있습니다. 예제는 다음과 같습니다.

1. Fabrikam 이라는 회사의 관리자 또는 정보 근로자는 Contoso 라는 다른 회사의 사용자를 초대 하 여 해당 응용 프로그램 Woodgrove를 사용 합니다.

2. Fabrikam의 Woodgrove 앱은 액세스 시 Azure AD Multi-Factor Authentication 필요 하도록 구성 됩니다.

3. Contoso의 B2B 게스트 사용자가 Fabrikam 테 넌 트의 Woodgrove에 액세스 하려고 하면 Azure AD Multi-Factor Authentication 챌린지를 완료 하 라는 메시지가 표시 됩니다.

4. 그러면 게스트 사용자는 Fabrikam을 사용 하 여 Azure AD Multi-Factor Authentication를 설정 하 고 옵션을 선택할 수 있습니다.

5. 이 시나리오는 Azure AD 또는 개인 Microsoft 계정 (MSA)의 모든 id에 대해 작동 합니다. 예를 들어 Contoso의 사용자가 소셜 ID를 사용 하 여 인증 하는 경우

6. Fabrikam은 Azure AD Multi-Factor Authentication를 지 원하는 충분 한 프리미엄 Azure AD 라이선스를 보유 해야 합니다. Contoso의 사용자는 Fabrikam의이 라이선스를 사용 합니다. B2B 라이선스에 대 한 자세한 내용은 [AZURE AD external id에 대 한 청구 모델을](./external-identities-pricing.md) 참조 하세요.

>[!NOTE]
>Azure AD Multi-Factor Authentication는 예측 가능성을 보장 하기 위해 리소스 테 넌 트에서 수행 됩니다.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>B2B 사용자에 대 한 Azure AD Multi-Factor Authentication 설정

B2B 공동 작업 사용자에 대 한 Azure AD Multi-Factor Authentication을 설정 하려면 다음 비디오를 시청 하세요.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>제품 상환에 대 한 B2B 사용자 Azure AD Multi-Factor Authentication

Azure AD Multi-Factor Authentication 상환 환경에 대해 자세히 알아보려면 다음 비디오를 시청 하세요.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>B2B 사용자에 대 한 Azure AD Multi-Factor Authentication 다시 설정

이제 B2B 게스트 사용자를 증명 하는 데 사용할 수 있는 PowerShell cmdlet은 다음과 같습니다.

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

3. B2B 공동 작업 사용자가 증명 방법을 다시 설정 하도록 요구 하도록 특정 사용자에 대 한 Azure AD Multi-Factor Authentication 메서드를 다시 설정 합니다. 
   예를 들면 다음과 같습니다.

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>B2B 사용자에 대 한 조건부 액세스

B2B 게스트 사용자에 대 한 CA 정책에 영향을 주는 다양 한 요인이 있습니다.

### <a name="device-based-conditional-access"></a>디바이스 기반 조건부 액세스

CA에는 사용자의 [장치가 규격 또는 하이브리드 AZURE AD에 가입](../conditional-access/concept-conditional-access-conditions.md#device-state-preview)되도록 요구 하는 옵션이 있습니다. B2B 게스트 사용자는 리소스 테 넌 트가 자신의 장치를 관리할 수 있는 경우에만 규정 준수를 충족할 수 있습니다. 한 번에 둘 이상의 조직에서 장치를 관리할 수 없습니다. B2B 게스트 사용자는 온-프레미스 AD 계정이 없으므로 하이브리드 Azure AD 조인을 만족할 수 없습니다. 게스트 사용자의 장치가 관리 되지 않는 경우에만 리소스 테 넌 트에서 장치를 등록 하거나 등록 한 다음 장치를 규격으로 만들 수 있습니다. 그러면 사용자가 grant 컨트롤을 만족할 수 있습니다.

>[!Note]
>외부 사용자를 위해 관리 되는 장치를 요구 하지 않는 것이 좋습니다.

### <a name="mobile-application-management-policies"></a>모바일 애플리케이션 관리 정책

**승인 된 클라이언트 앱 필요** 및 **앱 보호 정책 요구** 와 같은 CA 부여 컨트롤은 장치를 테 넌 트에 등록 해야 합니다. 이러한 컨트롤은 [iOS 및 Android 장치](../conditional-access/concept-conditional-access-conditions.md#device-platforms)에만 적용할 수 있습니다. 그러나 사용자의 장치가 이미 다른 조직에 의해 관리 되 고 있는 경우 이러한 컨트롤은 B2B 게스트 사용자에 게 적용할 수 없습니다. 모바일 장치는 한 번에 둘 이상의 테 넌 트에 등록할 수 없습니다. 모바일 장치가 다른 조직에서 관리 되는 경우 사용자가 차단 됩니다. 게스트 사용자의 장치가 관리 되지 않는 경우에만 리소스 테 넌 트에 장치를 등록할 수 있습니다. 그러면 사용자가 grant 컨트롤을 만족할 수 있습니다.  

>[!NOTE]
>외부 사용자에 대해 앱 보호 정책을 요구 하지 않는 것이 좋습니다.

### <a name="location-based-conditional-access"></a>위치 기반 조건부 액세스

초대 하는 조직이 파트너 조직을 정의 하는 신뢰할 수 있는 IP 주소 범위를 만들 수 있는 경우 IP 범위를 기반으로 하는 [위치 기반 정책을](../conditional-access/concept-conditional-access-conditions.md#locations) 적용할 수 있습니다.

**지리적 위치** 에 따라 정책을 적용할 수도 있습니다.

### <a name="risk-based-conditional-access"></a>위험 기반 조건부 액세스

B2B 게스트 사용자가 grant 컨트롤을 충족 하는 경우 [로그인 위험 정책이](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) 적용 됩니다. 예를 들어, 조직에서 중간 또는 높은 로그인 위험에 대해 Azure AD Multi-Factor Authentication 필요할 수 있습니다. 그러나 사용자가 이전에 Azure AD Multi-Factor Authentication에 등록 하지 않은 경우에는 사용자가 차단 됩니다. 이 작업은 악의적인 사용자가 합법적인 사용자의 암호를 손상 시키는 경우 자신의 Azure AD Multi-Factor Authentication 자격 증명을 등록 하지 못하도록 하기 위한 것입니다.

그러나 [사용자 위험 정책은](../conditional-access/concept-conditional-access-conditions.md#user-risk) 리소스 테 넌 트에서 확인할 수 없습니다. 예를 들어 높은 위험 수준 게스트 사용자를 위해 암호를 변경 해야 하는 경우 리소스 디렉터리에서 암호를 다시 설정할 수 없기 때문에 차단 됩니다.

### <a name="conditional-access-client-apps-condition"></a>조건부 액세스 클라이언트 앱 조건

[클라이언트 앱 조건은](../conditional-access/concept-conditional-access-conditions.md#client-apps) 다른 유형의 사용자와 마찬가지로 B2B 게스트 사용자에 대해 동일 하 게 동작 합니다. 예를 들어 게스트 사용자가 레거시 인증 프로토콜을 사용 하지 못하도록 할 수 있습니다.

### <a name="conditional-access-session-controls"></a>조건부 액세스 세션 컨트롤

[세션 컨트롤](../conditional-access/concept-conditional-access-session.md) 은 다른 유형의 사용자와 마찬가지로 B2B 게스트 사용자에 대해 동일 하 게 동작 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 Azure AD B2B 공동 작업에 대 한 다음 문서를 참조 하세요.

- [Azure AD B2B 협업이란?](./what-is-b2b.md)
- [ID 보호 및 B2B 사용자](../identity-protection/concept-identity-protection-b2b.md)
- [외부 ID 가격](https://azure.microsoft.com/pricing/details/active-directory/)
- [FAQ(질문과 대답)](./faq.md)