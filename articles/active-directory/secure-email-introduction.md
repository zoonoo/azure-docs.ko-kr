---
title: "Azure AD 전자 메일 보안 정책 및 구성 | Microsoft docs"
description: "전자 메일 보안 정책 및 구성을 배포하기 위한 Microsoft 권장 사항 및 핵심 개념을 설명합니다."
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 06/14/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: bb869e67340aa7a9f645c0b51f5a44d91ab51ce8
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---

# <a name="secure-email-policies-and-configurations"></a>전자 메일 보안 정책 및 구성
 
## <a name="introduction"></a>소개

이 문서의 권장 사항은 엔터프라이즈 전반에 전자 메일 정책 및 구성을 적용하기 위한 일반적인 Microsoft 지침을 설명합니다. 이러한 권장 사항은 직원이 안전하고 생산적인지 확인합니다.  특히 이 문서는 조직의 전자 메일을 보호하는 동시에 사용자에게 미치는 영향을 최소화하는 데 중점을 둡니다.

### <a name="intended-audience"></a>대상 그룹

이 문서는 [Exchange Online](https://technet.microsoft.com/library/jj200580.aspx)(Office 365) 및 [Microsoft Enterprise Mobility + Security](http://microsoft.com/ems) 제품에 익숙한 엔터프라이즈 인프라 설계자 및 IT 전문가를 대상으로 합니다. 이러한 제품에는 Azure Active Directory(ID), Microsoft Intune(장치 관리) 및 Azure Information Protection(데이터 보호)가 포함되지만 이에 국한되지는 않습니다. 

### <a name="customer-environment"></a>고객 환경

이 문서에서 권장하는 정책은 Microsoft 클라우드 내에서 완전히 작동하는 엔터프라이즈 조직 및 온-프레미스 및 Microsoft 클라우드에 배포된 인프라가 있는 고객에게 적용할 수 있습니다. 

### <a name="assumptions"></a>가정

이 문서의 권장 사항 대부분은 EMS(Enterprise Mobility + Security) E5 구독에서만 사용할 수 있는 서비스를 사용합니다. 제시된 권장 사항은 전체 EMS E5 구독 기능을 전제로 합니다. 

이 문서에서는 보호된 전자 메일에 권장되는 구성을 새로 배포된 환경에 적용하는 방법에 대해 설명합니다.  앞으로 기존 정책 및 구성을 다음에 설명된 정책 및 구성으로 마이그레이션하는 방법에 대한 추가 지침을 문서로 제공할 수 있습니다.

### <a name="caveats"></a>주의 사항

조직에는 권장되는 구성을 벗어나는 정책을 적용해야 하는 특정 권장 사항을 비롯한 규정 또는 기타 준수 요구 사항이 적용될 수 있습니다.  이러한 구성은 이전에는 사용할 수 없었던 사용 컨트롤을 권장합니다.  이러한 컨트롤은 보안과 생산성 간의 균형을 나타내기 때문에 사용하는 것이 좋습니다.  

다양한 조직의 보호 요구 사항을 처리하기 위해 최선을 다했습니다. 하지만 가능한 요구 사항 또는 조직 고유의 측면을 모두 처리할 수는 없습니다. 이 문서는 Microsoft 및 안전하고 생산적인 엔터프라이즈 팀이 조직에서 정책을 올바르게 적용하는 방법을 구상하는 가이드입니다. 

>[!NOTE]
>이러한 권장 사항에 설명된 보호 기능을 이해하는 데 필요한 핵심 개념에 대한 개요는 [EMS 및 Office 365 서비스 설명](secure-email-ems-office365-service-descriptions.md)을 참조하세요.
>

## <a name="core-concepts"></a>핵심 개념

작업을 진행하면서 불필요한 마찰을 겪는 사용자가 조직의 보안 정책을 우회하면 세계의 모든 보안 조치가 허사가 됩니다. Azure AD SSO(Single Sign-On)는 사용자의 부담을 최소화하려고 합니다. 이렇게 하면 사용자는 조직의 액세스 제어 정책을 여전히 준수하면서 생산성을 유지할 수 있습니다. 

### <a name="single-sign-on-authentication"></a>Single Sign-On 인증

다음 다이어그램은 일반적인 SSO 인증 흐름을 보여줍니다.

![최종 사용자 Single Sign-On 환경](./media/secure-email/typical-authentication-flow.png)

인증을 시작하기 위해 클라이언트는 과거에 얻은 자격 증명(예 : 사용자 이름 및 암호) 및/또는 SSO 아티팩트를 Azure AD에 제출합니다. SSO 아티팩트는 브라우저의 세션 토큰 또는 다양한 응용 프로그램의 새로 고침 토큰입니다. 

Azure AD는 자격 증명 및/또는 SSO 아티팩트를 확인하고 적용 가능한 모든 정책을 평가합니다. 그런 다음 리소스 공급자(다이어그램의 Exchange Online)에 대한 액세스 토큰을 발급합니다. Azure AD는 클라이언트가 향후 요청에서 SSO를 달성하도록 허용하는 응답의 일부로 SSO 아티팩트도 발급합니다. 

클라이언트는 SSO 아티팩트를 저장하고 액세스 토큰을 ID에 대한 증명으로 리소스 공급자에게 제출합니다. Exchange Online에서 액세스 토큰을 확인하고 필요한 검사를 수행한 후 전자 메일 메시지에 액세스할 수 있는 권한을 클라이언트에 부여합니다.

#### <a name="single-sign-on-sso-refresh-tokens"></a>SSO(Single Sign-On) 새로 고침 토큰

SSO는 다양한 방법으로 구현할 수 있습니다. 예를 들어, ID 공급자의 쿠키를 SSO 아티팩트로 사용하여 브라우저 내에 사용자의 로그인 상태를 저장할 수 있습니다. 그런 다음 동일한 ID 공급자를 사용하는 응용 프로그램에 자동으로(자격 증명 확인 없이) 로그인하려는 시도가 가능합니다. 

사용자가 Azure AD로 인증하면 사용자 브라우저와 Azure AD를 사용하여 SSO 세션이 설정됩니다. 쿠키 형식의 SSO 토큰은 이 세션을 나타냅니다. Azure AD는 두 가지 종류 즉, 영구적 및 비영구적 SSO 세션 토큰을 사용합니다. 영구적 세션 토큰은 로그인 중에 "로그인 유지" 확인란을 선택하면 브라우저에 의해 영구적 쿠키로 저장됩니다. 비영구적인 세션 토큰은 세션 쿠키로 저장되며 브라우저가 닫힐 때 제거됩니다. 

[OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html) 및 [OAuth 2.0](https://tools.ietf.org/html/rfc6749)과 같은 최신 인증 프로토콜을 사용할 수 있는 강력한 응용 프로그램의 경우 새로 고침 토큰을 SSO 아티팩트(앞서 설명한 SSO 쿠키 외에)로 사용하여 SSO를 사용하도록 설정됩니다. 응용 프로그램이 새 액세스 토큰을 요청하면 새로 고침 토큰이 권한 부여 서버에 제공됩니다. 

새로 고침 토큰에는 사용자를 인증할 때 사용되는 종류의 인증 방법에 대한 클레임 및 특성이 포함됩니다. 예를 들어 사용자가 여러 가지 방법(사용자 이름 및 암호와 전화 기반 인증)을 사용하여 인증에 성공한 후에는 MFA(Multi-Factor Authentication) 클레임이 새로 고침 토큰에 존재합니다. 또한 MFA 유효 기간 등의 데이터가 포함된 추가 클레임이 있을 수 있습니다. 

새로 고침 토큰을 사용하면 새로운 대화형 인증을 수행할 필요 없이 클라이언트가 새 액세스 토큰을 얻을 수 있습니다. 새로 고침 토큰은 액세스 토큰보다 수명이 훨씬 길며 새 액세스 및 새로 고침 토큰 쌍을 얻기 위해 사용될 수 있습니다. 그런 다음 새로 얻은 새로 고침 토큰을 사용하여 다른 액세스 및 새로 고침 토큰 집합을 계속 가져올 수 있습니다. 

클라이언트는 새로 고침 토큰 최대 비활성 시간 설정이 만료되거나 새로 고침 토큰의 최대 기간이 만료되거나 인증 및 권한 부여 정책 요구 사항이 변경될 때까지 이 SSO 프로세스를 계속합니다. 이러한 변경은 원본 새로 고침 토큰이 발급되는 시간 동안 발생합니다. 예를 들어 암호 재설정과 같은 중요한 사용자 특성 변경도 새 인증 토큰을 생성해야 합니다. 클라이언트가 계속 진행하려면 새로운 대화형 인증을 수행해야 합니다. 기본적으로 클라이언트가 지금까지 경험하지 못한 SSO 프로세스의 중단을 의미합니다. 

#### <a name="conditional-access"></a>조건부 액세스

응용 프로그램의 인증 서버로서 Azure AD는 액세스하려는 리소스에 적용된 모든 조건부 액세스 정책의 평가를 기반으로 액세스 토큰을 발급할지 여부를 결정합니다. 정책 요구 사항이 충족되면 액세스 토큰 및 업데이트된 새로 고침 토큰이 발급됩니다. 정책이 충족되지 않으면 사용자는 정책을 충족하는 방법에 대한 지침을 받거나 MFA(Multi-Factor Authentication)를 비롯한 추가 단계를 완료해야 합니다.  MFA가 완료되면 MFA 클레임이 결과 새로 고침 토큰에 추가됩니다.  

새로 고침 토큰의 클레임은 시간이 지남에 따라 누적됩니다. 일부 클레임에는 만료 타임라인이 있으며 이 시간이 지나면 권한 부여 확인 중에 더 이상 고려되지 않습니다. 이로 인해 예기치 않은 결과가 발생할 수 있습니다. 예를 들어 엑스트라넷 위치에서 오는 인증 시도에 MFA가 필요하도록 조건부 액세스 정책이 구성되어있는 경우가 있습니다. 이런 경우 엑스트라넷에서 리소스에 액세스할 때 예상되는 MFA 프롬프트가 표시되지 않을 수 있습니다. 가능한 이유는 사용자가 인트라넷을 나가지 직전에 MFA를 수행한 경우입니다. 따라서 해당 액세스 토큰에 유효한 MFA 클레임이 있습니다. 이러한 MFA 클레임은 정책 요구 사항을 충족하므로 Azure AD는 사용자에게 추가 MFA 요청을 표시하지 않습니다.

#### <a name="token-lifetime-policy"></a>토큰 수명 정책

토큰의 개별 클레임 만료 외에 토큰 자체에 만료 시간이 있습니다. 이전에 언급했듯이 만료된 토큰은 SSO 환경이 손상될 수 있는 한 가지 이유입니다. [토큰 수명 정책](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)을 사용하여 Azure AD에서 발급한 토큰의 수명을 설정할 수 있습니다. 위에서 유추할 수 있듯이 SSO 세션의 윤곽 정의는 캡처하는 것이 더 어렵습니다. 예를 들어, 다양한 앱이 외관상 연결이 끊어진 다양한 요소로 인해 SSO 세션의 수명에 영향을 미칠 수 있습니다.

>[!NOTE]
>Azure AD 전역 관리자는 새로 고침 토큰의 유효 기간과 비활성 기간을 제어할 수 있습니다. [Azure Active Directory에서 구성 가능한 토큰 수명](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) 문서에 설명된 설정을 사용하는 액세스 토큰 및 클레임에 대한 정보.
>

#### <a name="primary-refresh-tokens"></a>기본 새로 고침 토큰

지금까지 이 문서는 단일 클라이언트 컨텍스트 내에서 SSO가 작동하는 방식에 대해 설명했습니다. 하지만 단일 앱 내에서 SSO를 경험하기에는 충분하지 않습니다. 요즘 사용자는 Microsoft Office와 같은 동일한 응용 프로그램 제품군 내에서 여러 응용 프로그램에 걸친 대화형 워크플로를 경험합니다. 사용자는 내부적으로 개발된 LOB 응용 프로그램을 비롯한 관련 없는 응용 프로그램을 통해 액세스해야 합니다. 

일반적으로 도메인에 가입된 Windows 장치는 Windows 통합 인증(Kerberos)을 사용하여 여러 응용프로그램 및 리소스에서 높은 수준의 SSO 환경을 구현할 수 있습니다. 이러한 앱에는 Internet Explorer 나 Edge와 같은 지원되는 브라우저가 포함됩니다. PRT(Primary Refresh Token)의 형태로 Azure AD 영역에 대한 아날로그가 있습니다. 

권한 있는 PRT 토큰은 엄선된 클라이언트 엔터티 집합(예: 플랫폼 시스템 구성 요소)에서만 사용할 수 있습니다. 그런 다음 엔터티는 다른 클라이언트 응용 프로그램이 원활한 SSO 환경을 제공할 수 있도록 조정된 인증 액세스를 허용할 수 있습니다. 

[iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios) 및 [Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android) 장치의 Office 365 사용자를 위해 인증 broker 기능을 적용하여 필요한 인증 수를 줄이기 위한 추가 작업이 수행되었습니다. 이 기능은 Microsoft Authenticator 및 Intune 회사 포털 앱에 기본 제공됩니다.

>[!NOTE]
>이 문서에서 설명하는 권장 사항에서는 앱 중 하나가 사용자의 iOS 또는 Android 장치에 배포되어 있다고 가정합니다. 예를 들어, 인증 broker(Microsoft Authenticator 또는 Intune 회사 포털)와 같은 응용 프로그램.
>

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

MFA(Multi-Factor Authentication)는 인증 주체에 대해 높은 수준의 신뢰를 제공합니다. 이것은 해당 주체가 주체의 ID에 대해 다수의 증명 또는 증거를 제공하기 때문입니다. 증명은 주체와 인증 기관만 아는 미리 설정된 비밀 정보이거나 주체만 소유할 것으로 예상되는 물리적 엔터티일 수 있습니다. MFA는 대개 단계적으로 수행됩니다. 먼저 암호를 사용하여 ID를 설정한 다음 두 번째 요소로 다른(악의적인 공격에 덜 취약한) 인증 방법을(또는 그 반대로) 요구합니다.

다른 인증 기관은 MFA 또는 강력한 인증을 약간 다르게 해석할 수 있습니다. 예를 들어 일부 인증 기관(또는 보다 구체적으로 해당 인증 기관에서 정책을 구성하는 관리자)은 실제 스마트 카드 기반 인증을 MFA로 해석하도록 선택할 수 있습니다. 엄밀히 말해 스마트 카드 인증이 단일 단계 인증인 경우에도 발생할 수 있습니다. 

실제 스마트 카드를 요구하고 스마트 카드를 사용하기 위해 PIN(비밀)을 입력해야 하는 요구 사항의 조합은 MFA로 해석될 수 있습니다. 그러나 일부 사람들은 보다 번거로운 인증 방법을 더 빈번하게 수행해야 하는 경우에 대해 좀더 관대할 수 있습니다. 따라서 일반적으로 강력한 인증이 필요한 리소스에 대해서는 더 강력한 인증 사이에서 발생하는 일반 인증이 유효한 것으로 간주됩니다. 예를 들어, 일부 조직에서는 몇 시간 또는 며칠마다 사용자가 MFA를 수행하도록 요구하는 것이 가능할 수 있습니다. 시간은 보호하고 있는 리소스의 민감도에 따라 달라지며 자원에 액세스하려는 사용자의 물리적 위치가 변경되지 않는 한 변하지 않습니다.

Azure AD와 ADFS는 MFA를 사용하여 인증이 MFA로 수행되는지 여부를 나타냅니다. 기본적으로 Azure AD는 [Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) 또는 [비즈니스용 Windows Hello](https://docs.microsoft.com/en-us/windows/access-protection/hello-for-business/hello-identity-verification)로 인증이 수행될 때 MFA 클레임으로 토큰을 발급합니다. 페더레이션 시나리오에서 Azure AD는 ADFS와 같은 페더레이션된 ID 공급자로부터 MFA 클레임을 받아서 토큰의 MFA 클레임으로 이어갑니다. 

### <a name="security-guidelines"></a>보안 지침

이 섹션에는 이후 섹션에 제공되는 전자 메일 보안 권장 사항을 구현할 때 따라야 하는 일반적인 보안 지침이 포함되어 있습니다.

#### <a name="security-and-productivity-trade-offs"></a>보안 및 생산성 절충

보안과 생산성 사이에는 상충 관계가 있습니다. 이러한 상충 관계를 이해하는 데 도움을 주기 위해 SFU(Security-Functionality-Usability/사용 용이성)라는 보안 트라이어드가 널리 사용됩니다.

![보안 및 생산성 절충](./media/secure-email/security-triad.png)

SFU 트라이어드 모델을 기반으로 이 문서의 권장 사항은 다음 원칙을 따릅니다. 

* 대상 그룹 파악 - 직무/보안 기준에 따라 유연성 확보
* 적시에 보안 정책을 적용하고 의미가 있는지 확인

#### <a name="administrators-versus-users"></a>관리자 대 사용자

관리 계정이 있거나 임시로 관리 계정/권한을 받을 수 있는 모든 사용자가 포함된 보안 그룹을 만드는 것이 좋습니다. 이러한 보안 그룹은 Azure AD 및 Office 365 관리자에게 한정된 조건부 액세스 정책을 정의하는 데 사용됩니다.  

정책 권장 사항은 계정과 관련된 권한을 고려합니다. [Office 365 관리자](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) 역할은 Exchange Online 및 Office 365에 비해 훨씬 많은 권한을 갖습니다. 따라서 이러한 계정에 대한 정책 권장 사항은 일반 사용자 계정에 대한 권장 사항보다 엄격합니다. 관리자를 언급하는 모든 정책은 Office 365 관리 계정에 권장되는 정책을 나타냅니다.

#### <a name="reduce-the-number-of-accounts-with-persistent-admin-access"></a>영구적인 관리 액세스로 계정 수 줄이기

Azure AD Privileged Identity Management를 사용하여 영구적인 관리 계정 수를 줄입니다 또한 Office 365 관리자는 일반적인 비관리 용으로 별도의 사용자 계정을 갖는 것이 좋습니다. 작업 기능과 관련된 작업을 완료하는 데 필요한 경우에만 관리 계정을 적용합니다.

## <a name="next-steps"></a>다음 단계
[권장된 정책](secure-email-recommended-policies.md)

