---
title: Azure Active Directory에서 ID 인프라를 보호하기 위한 5단계
description: 이 문서에서는 관리자가 Azure AD 기능을 사용하여 해당 조직을 보호하기 위해 수행해야 하는 중요한 작업 목록을 간략히 안내합니다.
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: 94d96cab28f738984b3d05d5eee0754e8c5e75b6
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341582"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>ID 인프라를 보호하기 위한 5단계

이 문서를 읽는다면 보안의 중요성을 인식하고 있다는 뜻입니다. 이미 조직의 보안을 책임지고 있을 가능성도 있습니다. 보안의 중요성을 다른 사람들에게 납득시켜야 한다면 최신 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/intelligence-report)를 읽도록 보내주세요.

이 문서는 사이버 공격에 대비하기 위한 5단계 검사 목록을 사용하여 Azure Active Directory의 기능을 통해 더 안전한 상태를 형성하는 데 도움이 됩니다.

이 검사 목록은 다음 사항의 수행 방법 설명을 통해 중요한 권장 작업을 빠르게 배포하여 조직을 즉시 보호하는 데 도움이 됩니다.

* 자격 증명을 강화합니다.
* 공격 노출 영역을 줄입니다.
* 위협 응답을 자동화합니다.
* 감사 및 모니터링의 인식률을 높입니다.
* 자가 진단으로 더 예측 가능하고 완벽한 최종 사용자 보안을 사용하도록 설정합니다.

> [!NOTE]
> 이 문서의 다양한 권장 사항은 ID 공급자로 Azure Active Directory를 사용하도록 구성되어 있는 응용 프로그램에만 적용됩니다. Single Sign-On에 대해 앱을 구성하면 해당 앱에 자격 증명 정책, 위협 요소 탐지, 감사, 로깅 및 기타 기능의 이점이 추가됩니다. [Azure Active Directory를 통한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso)은 이러한 모든 권장 사항의 기반이 되는 토대입니다.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>시작하기 전에: MFA를 통해 권한 있는 계정을 보호

이 검사 목록을 시작하기 전에 이 검사 목록을 읽는 도중에 손상되지 않도록 해야 합니다. 먼저 권한 있는 계정을 보호해야 합니다.

공격자가 권한 있는 계정의 제어권을 확보하면 엄청난 피해가 발생할 수 있으므로 이러한 계정을 보호하는 것이 무엇보다도 중요합니다. [기준 보호](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection)를 사용하여 조직의 모든 관리자가 [MFA(Azure Multi-Factor Authentication)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)를 사용하도록 설정하고 제공합니다. MFA를 구현하지 않았다면, 지금 구현하세요! 이 문제는 중요합니다.

모두 준비되었나요? 이제 검사 목록을 시작하겠습니다.

## <a name="step-1---strengthen-your-credentials"></a>1단계 - 자격 증명을 강화합니다. 

대부분의 엔터프라이즈 보안 위반은 암호 스프레이, 위반 재생 또는 피싱 같은 방법 중 하나로 손상된 계정으로 시작됩니다. 이러한 공격에 대해 자세히 알아보려면 이 비디오(1시간 15분)를 시청하세요.
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

ID 시스템의 사용자가 취약한 암호를 사용하고 다단계 인증으로 강화하지 않는다면 이는 손상 여부의 문제가 아니라 “손상 빈도”의 문제가 됩니다.

### <a name="make-sure-your-organization-use-strong-authentication"></a>조직에서 강력한 인증을 사용하도록 해야 함

암호가 추측되고, 피싱되고, 맬웨어로 도난 또는 재사용되는 횟수를 고려할 때 강력한 자격 증명의 형식을 갖춘 암호로 돌아가는 것은 중요합니다. [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)에 대해 자세히 알아보세요.

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>기존의 복잡성, 만료 규칙을 벗어나 일반적으로 공격을 받는 암호 사용 금지 시작

많은 조직에서는 기존의 복잡성(예: 특수 문자) 및 암호 만료 규칙을 사용합니다. Microsoft의 연구에서 이러한 정책은 사용자가 추측하기가 더 쉬운 암호를 선택하는 원인이 되므로 유해함이 나타났습니다.

[NIST 지침](https://pages.nist.gov/800-63-3/sp800-63b.html)과 일치하는 Microsoft의 권장 사항은 다음 세 가지를 구현하기 위한 것입니다.

1. 8자 이상의 암호가 필요합니다. 사용자가 예측 가능한 암호를 선택하거나, 파일에 암호를 저장하거나, 적어둘 수 있으므로 긴 암호가 반드시 더 나은 것은 아닙니다.
2. **Summer2018!** 과 같은 암호를 사용자가 쉽게 추측하도록 하는 만료 규칙을 사용하지 않습니다.
3. 사용자가 암호에 예측 가능한 문자 대용을 선택할 수 있으므로 문자 조합을 요구 사항을 사용하지 않고, 사용자가 일반적으로 공격 받는 암호를 선택하지 않도록 합니다.

직접 Azure AD에서 ID를 만들 경우 사용자에 대해 [암호가 만료되지 않도록 PowerShell](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)을 사용할 수 있습니다. ID를 Azure AD에 동기화하는 데 Azure AD Connect와 함께 온-프레미스 AD를 사용하는 조직에서는(하이브리드 배포라고도 함) [도메인 그룹 정책 설정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) 또는 [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy)을 사용하여 온-프레미스 [인텔리전스 암호 정책](https://aka.ms/passwordguidance)을 구현해야 합니다.

Azure Active Directory의 [동적으로 금지된 암호](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) 기능은 현재 공격자 동작을 사용하여 사용자가 쉽게 추측할 수 있는 암호를 설정하지 못하도록 합니다. 이 기능은 항상 켜져 있으며 하이브리드 배포를 사용하는 조직에서는 [암호 쓰기 저장](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback)을 활성화하여 이 기능을 활용하거나 [Windows Server Active Directory에 대한 Azure AD 암호 보호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)를 배포할 수 있습니다. Azure AD 암호 보호는 보통 일반적인 암호를 선택하거나 사용자가 구성 가능한 사용자 지정 암호를 구성할 수 없도록 차단합니다.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>유출된 자격 증명을 보호 및 작동 중단에 대비해 복원력을 추가

조직에서 하이브리드 ID 솔루션을 사용하는 경우 다음과 같은 두 가지 이유로 암호 해시 동기화를 사용하도록 설정해야 합니다.

* Azure AD 관리의 [유출된 자격 증명이 있는 사용자](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) 보고서는 “다크 웹”에서 노출된 사용자 이름 및 암호 쌍에 대해 경고합니다. 엄청난 양의 암호가 피싱, 맬웨어 및 나중에 위반되는 타사 사이트에서 암호 재사용을 통해 유출됩니다. Microsoft는 이러한 유출된 자격 증명 중 많은 부분을 찾아 조직의 자격 증명과 일치하는 경우 이 보고서를 통해 알립니다. 단, [암호 해시 동기화를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)한 경우에만 적용됩니다!
* 온-프레미스 장애(예: 랜섬웨어 공격)의 경우 [암호 해시 동기화를 사용하여 클라우드 인증](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)으로 전환할 수 있습니다. 이 백업 인증 방법을 사용하면 Office 365를 비롯하여 Azure Active Directory를 통한 인증을 위해 구성된 앱에 계속 액세스할 수 있습니다.

[암호 해시 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) 작동 방식에 대해 자세히 알아보세요.

### <a name="implement-ad-fs-extranet-lockout"></a>AD FS 엑스트라넷 잠금 구현

Azure AD에 직접 인증하도록 응용 프로그램을 구성하는 조직은 [Azure AD 스마트 잠금](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)의 이점을 활용합니다. Windows Server 2012 R2에서 AD FS를 사용하는 경우 AD FS [엑스트라넷 잠금 보호](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)를 구현합니다. Windows Server 2016에서 AD FS를 사용하는 경우 [엑스트라넷 스마트 잠금](https://support.microsoft.com/en-us/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)을 구현합니다. AD FS 스마트 엑스트라넷 잠금은 AD FS를 대상으로 하는 무차별 암호 대입 공격으로부터 사용자를 보호하면서 사용자가 Active Directory에서 잠기지 않도록 방지합니다.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>본질적으로 안전하며 사용이 더 용이한 자격 증명의 이점 활용

[Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)를 사용하면 PC 및 모바일 장치에서 강력한 2단계 인증으로 암호를 바꿀 수 있습니다. 이 인증은 장치에 연결되는 사용자 자격 증명의 새로운 유형으로 구성되며 생체 인식 또는 PIN을 사용합니다.

## <a name="step-2---reduce-your-attack-surface"></a>2단계 - 공격 노출 영역 줄이기

암호 손상의 확장성을 고려할 때 조직에서 공격 노출 영역을 최소화하는 것은 중요합니다. 오래되고 덜 안전한 프로토콜의 사용 제거, 진입점 액세스 제한 및 리소스에 대한 더 중요한 관리 액세스의 제어 실행은 공격 노출 영역을 줄이는 데 도움이 될 수 있습니다.

### <a name="block-legacy-authentication"></a>레거시 인증 차단

자체 레거시 메서드를 사용하여 Azure AD로 인증하고 회사 데이터에 액세스하는 앱은 조직에 또 다른 위험을 제기합니다. 레거시 인증을 사용한 앱으로는 POP3, IMAP4 또는 SMTP 클라이언트가 있습니다. 레거시 인증 앱은 사용자를 대신하여 인증하고 Azure AD가 고급 보안 평가를 수행하지 못하도록 방지합니다. 대체 최신 인증은 다단계 인증 및 조건부 액세스를 지원하므로 보안 위험이 줄어듭니다. 다음과 같은 세 가지 작업이 권장됩니다.

1. [AD FS를 사용하는 경우 레거시 인증](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)을 차단합니다.
2. [최신 인증을 사용하도록 SharePoint Online 및 Exchange Online](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)을 설정합니다.
3. [레거시 인증을 차단하는 조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)을 사용합니다.

### <a name="block-invalid-authentication-entry-points"></a>잘못된 인증 진입점 차단

위반이 발생할 때 가상 위반 사고방식을 사용하여 손상된 사용자 자격 증명의 영향을 줄여야 합니다. 사용자 환경의 각 앱의 경우 권한이 있는 그룹, 네트워크, 장치 및 기타 요소가 무엇인지 유효한 사용 사례를 고려한 다음, 나머지를 차단합니다. [서비스 계정 또는 높은 권한](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)의 사용을 제한하도록 주의해야 합니다. [Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 사용하면 정의하는 특정 조건에 따라 자신의 앱 및 리소스에 권한이 부여된 사용자가 액세스하는 방법을 제어할 수 있습니다.

서비스 계정(자동화된 방식으로 작업을 수행하는 데 사용하는 계정)에 특히 주의해야 합니다. 조건부 액세스를 사용하면 그러한 계정이 적절한 서비스에 대해, IP에서, 하루 어떤 시간에만 실행되도록 할 수 있습니다.

### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 구현

“가정 위반”의 또 다른 영향은 손상된 계정을 권한 있는 역할로 작동할 수 있는 가능성을 최소화해야 한다는 점입니다. 

[Azure AD PIM(Privileged Identity Management)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)은 다음을 수행하는 데 유용하여 계정 권한을 최소화하는 데 도움이 됩니다.

* 관리자 역할에 할당된 사용자를 식별하고 관리합니다.
* 제거해야 하는 사용되지 않거나 과도한 권한 역할을 파악합니다.
* 권한 있는 역할이 다단계 인증으로 보호되도록 규칙을 설정합니다.
* 권한 있는 역할이 권한 있는 작업을 수행하기에 충분히 긴 기간 동안만 부여되도록 규칙을 설정합니다.

Azure AD PIM을 사용하도록 설정한 다음, 관리자 역할이 할당된 사용자를 보고, 해당 역할에서 필요 없는 계정을 제거합니다. 나머지 권한 있는 사용자의 경우 영구 상태에서 적격 상태로 이동합니다. 마지막으로, 이러한 권한 있는 역할에 대한 액세스 권한을 얻어야 하는 경우 안전하게 수행할 수 있도록 적절한 정책을 설정합니다.

권한 있는 계정 프로세스 배포의 일부로, 직접 잠글 경우 Azure AD에 액세스할 수 있도록 [두 개 이상의 비상 계정을 만드는 모범 사례](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)를 수행합니다.

## <a name="step-3---automate-threat-response"></a>3단계 - 위협 응답 자동화

Azure Active Directory에는 검색 및 응답 사이의 대기 시간을 제거하기 위해 자동으로 공격을 가로채는 많은 기능이 있습니다. 범죄자들이 사용자 환경에 진입하는 데 사용하는 시간을 줄이면 비용 및 위험을 줄일 수 있습니다. 다음은 이를 수행할 수 있는 구체적인 단계입니다.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection을 사용하여 사용자 위험 보안 정책 구현

사용자 위험은 사용자의 ID가 손상되었다는 가능성을 나타내며, 사용자의 ID와 연결된 [사용자 위험 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)를 기반으로 계산됩니다. 사용자 위험 정책은 특정 사용자 또는 그룹에 대해 위험 수준을 평가하는 조건부 액세스 정책입니다. 낮음, 보통, 높음 위험 수준에 따라 액세스를 차단하거나 다단계 인증을 사용한 보안 암호 변경을 요구하도록 정책을 구성할 수 있습니다. Microsoft의 권장 사항은 높은 위험 수준에 있는 사용자의 경우 보안 암호 변경이 필요합니다.

![위험에 대한 플래그가 지정된 사용자](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection을 사용하여 로그인 위험 정책 구현

로그인 위험은 계정 소유자가 아닌 누군가가 ID를 사용하여 로그인을 시도할 가능성을 말합니다. [로그인 위험 정책](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 특정 사용자 또는 그룹에 대해 위험 수준을 평가하는 조건부 액세스 정책입니다. 위험 수준(높음/보통/낮음)에 따라 정책은 액세스를 차단하거나 다단계 인증을 강제 적용하도록 구성할 수 있습니다. 보통 이상의 위험 로그인에서는 다단계 인증을 받도록 해야 합니다.

![익명 IP 주소에서 로그인](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>4단계 - 인식률 높이기

보안 관련 이벤트의 감사와 로깅 및 관련 경고는 효과적인 보호 전략의 중요한 구성 요소입니다. 보안 로그 및 보고서는 의심스러운 활동에 대한 전자 기록을 제공하며, 네트워크의 외부 침투와 내부 공격의 시도 또는 성공을 나타낼 수 있는 패턴을 검색할 수 있도록 도움을 줍니다. 감사를 사용하여 사용자 활동을 모니터링하고, 규제 준수를 문서화하며, 법정 분석 등을 수행할 수 있습니다. 경고는 보안 이벤트의 알림을 제공합니다.

### <a name="monitor-azure-ad"></a>Azure AD 모니터링

Microsoft Azure 서비스 및 기능은 구성 가능한 보안 감사 및 로깅 옵션을 제공하여 보안 정책과 메커니즘의 차이를 식별하고 이러한 차이를 해결하여 위반을 방지할 수 있게 합니다. [Azure 로깅 및 감사](https://docs.microsoft.com/azure/security/azure-log-audit)와 [Azure Active Directory 포털의 감사 작업 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)를 사용할 수 있습니다.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>하이브리드 환경에서 Azure AD Connect Health 모니터링

[Azure AD Connect Health를 통한 ADFS 모니터링](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)은 ADFS 인프라에서 잠재적인 문제 및 공격의 시각화에 대한 통찰력을 높여줍니다. Azure AD Connect Health는 세부 정보, 해결 단계 및 인증 트래픽에 관련된 여러 가지 메트릭에 대한 사용 현황 분석, 성능 모니터링 및 보고서와 같은 관련된 문서에 대한 링크를 제공합니다.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD ID Identity Protection 이벤트 모니터링

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 주는 잠재적 취약성을 검색하는 데 사용할 수 있는 알림, 모니터링 및 보고 도구입니다. 유출된 자격 증명, 불가능한 이동 및 감염된 장치, 익명 IP 주소, 의심스러운 활동 및 알 수 없는 위치에 연결된 IP 주소에서의 로그인과 같은 위험 이벤트를 검색합니다. 위험한 사용자의 이메일을 수신 및/또는 주 단위 요약 이메일을 수신하는 알림 경고를 사용하도록 설정합니다.

![위험에 대한 플래그가 지정된 사용자](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>5단계 - 최종 사용자 자가 진단 사용

사용자는 최대한 생산성과 보안의 균형을 맞추고자 합니다. 결국 보안에 대한 기반을 설정하는 사고 방식을 통해 경험에 착수하는 것과 같은 방식으로 사용자에게 권한을 부여하면서 한편으론 경계를 게을리하지 않으면 조직에서 마찰을 없앨 수 있습니다. 

### <a name="implement-self-service-password-reset"></a>셀프 서비스 암호 재설정 구현

IT 관리자는 Azure의 [SSPR(셀프 서비스 암호 재설정)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)을 사용하여 사용자에게 해당 암호 또는 계정을 관리자 작업 없이 재설정하거나 잠금 해제할 수 있도록 할 수 있습니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템에 액세스하는지 추적하는 구체적인 보고서가 포함되어 있습니다. 

### <a name="implement-self-service-group-management"></a>셀프 서비스 그룹 관리 구현

Azure AD는 보안 그룹 및 Office 365 그룹을 사용하여 리소스에 대한 액세스를 관리하는 기능을 제공합니다. 이러한 그룹은 IT 관리자 대신 그룹 소유자가 관리할 수 있습니다. [셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)로 알려진 이 기능을 사용하면 관리자가 해당 요청을 처리하길 의존하지 않고도 관리 역할에 할당되지 않은 그룹 소유자가 그룹을 만들고 관리할 수 있습니다.

### <a name="implement-azure-ad-access-reviews"></a>Azure AD 액세스 검토 구현

[Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)를 사용하면 필요하지 않은 경우 연장된 기간 동안 사용자에게 액세스 권한을 부여하지 않는 보안 표준을 유지하도록 그룹 구성원, 엔터프라이즈 응용 프로그램에 대한 액세스 및 권한 있는 역할 할당을 관리할 수 있습니다.

## <a name="summary"></a>요약

보안 ID 인프라에 대한 여러 측면이 있지만 이 5가지 검사 목록은 더 안전한 ID 인프라를 신속하게 달성하는 데 도움이 됩니다.

* 자격 증명을 강화합니다.
* 공격 노출 영역을 줄입니다.
* 위협 응답을 자동화합니다.
* 감사 및 모니터링의 인식률을 높입니다.
* 자가 진단으로 더 예측 가능하고 완벽한 최종 사용자 보안을 사용하도록 설정합니다.

ID 보안에 대해 진지하게 고려해 주신 것에 감사를 드리며, 이 문서가 여러분 조직의 더 안전한 태세를 위한 유용한 로드맵이 되길 바랍니다.

## <a name="next-steps"></a>다음 단계
권장 사항의 계획 및 배포에 대한 도움이 필요한 경우 [Azure AD 프로젝트 배포 계획](http://aka.ms/deploymentplans)을 참조하세요.
